<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="简要案情/案件名称">
              <j-input placeholder="请输入简要案情/案件名称" v-model="queryParam.briefing"></j-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="模型名称">
              <j-dict-select-tag placeholder="请选择模型名称" v-model="queryParam.alertCategory" dictCode="dc_model_store,name,id"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
<!--            <a-col :xl="10" :lg="11" :md="12" :sm="24">-->
<!--              <a-form-item label="预警时间">-->
<!--                <j-date :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" placeholder="请选择开始时间" class="query-group-cust" v-model="queryParam.alertTime_begin"></j-date>-->
<!--                <span class="query-group-split-cust"></span>-->
<!--                <j-date :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" placeholder="请选择结束时间" class="query-group-cust" v-model="queryParam.alertTime_end"></j-date>-->
<!--              </a-form-item>-->
<!--            </a-col>-->
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="所属分局">
                <j-select-depart placeholder="请选择所属分局" v-model="queryParam.companyName"/>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="核查情况">
                <j-dict-select-tag placeholder="请选择核查情况" v-model="queryParam.verification" dictCode="verification"/>
              </a-form-item>
            </a-col>
          </template>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator right-table">
      <a-button @click="handleAdd" type="primary" icon="plus" v-has="'warning:add'">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('预警中心')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel" v-has="'warning:import'">
        <a-button type="primary" icon="import" >导入</a-button>
      </a-upload>
      <a-button @click="archiveList" type="primary" icon="plus" >{{ archivingStatus ? '未归档': '已归档' }}</a-button>
      <!-- 高级查询区域 -->
<!--      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>-->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
          <a-menu-item key="2" @click="batchArchive"><a-icon type="wallet"/>归档</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <div style='display: flex;align-items: center;justify-content: center;' >
          <div >
             <a  @click="handleEdit(record)">编辑</a>
             <a-divider type="vertical" />
          </div>
          <div v-has="'warning:inspector'"  v-if='record.archivingStatus == 0'>
             <a  @click="verificationEdit(record.id,'督察')">督察</a>
             <a-divider type="vertical" />
          </div>
          <div v-has="'warning:verification'" v-if='record.archivingStatus == 0'>
            <a v-if='record.verification == 0' @click="verificationAdd(record.id)">核查</a>
            <a v-else @click="verificationEdit(record.id,'核查')">编辑核查</a>
            <a-divider type="vertical" />
          </div>
          <div v-has="'warning:archive'" v-if='record.archivingStatus == 0'>
             <a    @click="archiveEdit(record.id)">归档</a>
             <a-divider type="vertical" />
          </div>
            <div v-if='record.archivingStatus == 1'>
               <a    @click="verificationDetail(record.id)">核查详情</a>
               <a-divider type="vertical" />
            </div>
          <a @click="handleDetail(record)">详情</a>
          </div>
<!--          <a-dropdown>-->
<!--            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->
<!--            <a-menu slot="overlay">-->
<!--              <a-menu-item>-->
<!--                <a @click="handleDetail(record)">详情</a>-->
<!--              </a-menu-item>-->
<!--              <a-menu-item>-->
<!--                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">-->
<!--                  <a>删除</a>-->
<!--                </a-popconfirm>-->
<!--              </a-menu-item>-->
<!--            </a-menu>-->
<!--          </a-dropdown>-->
        </span>

      </a-table>
    </div>

    <dc-warning-center-modal ref="modalForm" @ok="modalFormOk"></dc-warning-center-modal>
    <dc-verification-record-modal ref="verification" @ok="modalFormOk"/>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import DcWarningCenterModal from './modules/DcWarningCenterModal'
  import {filterMultiDictText} from '@/components/dict/JDictSelectUtil'
  import DcVerificationRecordModal from './modules/DcVerificationRecordModal'
  import { deleteAction, getAction, postAction, putAction } from '@/api/manage'

  export default {
    name: 'DcWarningCenterList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      DcWarningCenterModal,
      DcVerificationRecordModal
    },
    data: function() {
      return {
        description: '预警中心管理页面',
        // 表头
        columns: [],
        columns1: [
          {
            title: '事件单/案件编号',
            align: 'center',
            dataIndex: 'sjbhAjbh',
            ellipsis: true
          },
          {
            title: '简要案情/案件名称',
            align: 'center',
            dataIndex: 'briefing',
            width: 300,
            ellipsis: true
          },
          {
            title: '模型名称',
            align: 'center',
            dataIndex: 'alertCategory_dictText',
            ellipsis: true
          },
          {
            title: '所属分局',
            align: 'center',
            dataIndex: 'companyName_dictText',
            ellipsis: true,
            width: 120,
          },
          {
            title: '办案单位名称',
            align: 'center',
            dataIndex: 'caseUnit',
            ellipsis: true,
            width: 120,
            customRender: function(text) {
              if (text.length != 0) {
                if (text.lastIndexOf('局') != -1){
                  let startIndex = text.lastIndexOf('局');
                  return  text.substring(++startIndex);
                }
              }
              return text
            }
          },
          {
            title: '核查情况',
            align: 'center',
            dataIndex: 'verification_dictText',
            width: 100,
            ellipsis: true
          },
          {
            title: '归档情况',
            align: 'center',
            dataIndex: 'archivingStatus_dictText',
            width: 100,
            ellipsis: true
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            fixed: 'right',
            width: 200,
            scopedSlots: { customRender: 'action' }
          }
        ],
        columns2: [

          {
            title: '事件单/案件编号',
            align: 'center',
            dataIndex: 'sjbhAjbh',
            ellipsis: true
          },
          {
            title: '简要案情/案件名称',
            align: 'center',
            dataIndex: 'briefing',
            width: 300,
            ellipsis: true
          },
          {
            title: '模型名称',
            align: 'center',
            dataIndex: 'alertCategory_dictText',
            ellipsis: true
          },
          {
            title: '所属分局',
            align: 'center',
            dataIndex: 'companyName_dictText',
            ellipsis: true,
            width: 120,
          },
          {
            title: '办案单位名称',
            align: 'center',
            dataIndex: 'caseUnit',
            ellipsis: true,
            width: 120,
            customRender: function(text) {
              if (text.length != 0) {
                if (text.lastIndexOf('局') != -1){
                  let startIndex = text.lastIndexOf('局');
                  return  text.substring(++startIndex);
                }
              }
              return text
            }
          },
          {
            title: '核查情况',
            align: 'center',
            dataIndex: 'verification_dictText',
            width: 100,
            ellipsis: true
          },
          {
            title: '归档情况',
            align: 'center',
            dataIndex: 'archivingStatus_dictText',
            width: 100,
            ellipsis: true
          },
          {
            title: '核查结论',
            align: 'center',
            width: 100,
            dataIndex: 'conclusion_dictText'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            // fixed: 'right',
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: '/inspector/dcWarningCenter/list',
          delete: '/inspector/dcWarningCenter/delete',
          deleteBatch: '/inspector/dcWarningCenter/deleteBatch',
          exportXlsUrl: '/inspector/dcWarningCenter/exportXls',
          importExcelUrl: 'inspector/dcWarningCenter/importExcel',
          queryByWarningCenterId: '/inspector/dcVerificationRecord/queryByWarningCenterId',
          archiveBatch: '/inspector/dcWarningCenter/archiveBatch'
        },
        dictOptions: {},
        superFieldList: [],
        archivingStatus: false
      }
    },
    created() {
      this.getSuperFieldList();
      this.columns = this.columns1;
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'briefing',text:'简要情况',dictCode:''})
        fieldList.push({type:'string',value:'alertCategory',text:'预警类别'})
        fieldList.push({type:'sel_depart',value:'companyName',text:'单位名称'})
        fieldList.push({type:'string',value:'departmentName',text:'部门名称',dictCode:''})
        fieldList.push({type:'string',value:'verification',text:'核查情况',dictCode:'verification'})
        fieldList.push({type:'string',value:'archivingStatus',text:'归档情况',dictCode:'archiving_status'})
        this.superFieldList = fieldList
      },
      verificationAdd(mainId){
        this.$refs.verification.add();
        this.$refs.verification.fullscreen = true;
        this.$refs.verification.title = "核查";
        this.$refs.verification.disableSubmit = false;
        this.$refs.verification.mainId = mainId;
      },
      verificationEdit(mainId,title){
        getAction(this.url.queryByWarningCenterId, { id:mainId }).then(res=>{
          if(res.success && res.result){
            this.$refs.verification.edit(res.result);
            this.$refs.verification.fullscreen = true;
            this.$refs.verification.title = "编辑" + title;
            this.$refs.verification.disableSubmit = false;
          }else{
            this.$refs.verification.add();
            this.$refs.verification.fullscreen = true;
            this.$refs.verification.title = title;
            this.$refs.verification.disableSubmit = false;
            this.$refs.verification.mainId = mainId;
          }
        })
      },
      archiveEdit(id){
        var that = this;
        this.$confirm({
          title: "确认归档",
          content: "是否归档选中数据?",
          onOk: function () {
            putAction(that.url.archiveBatch,  {ids: id } ).then((res)=>{
              if(res.success){
                console.log(res)
                that.$message.success(res.message);
                that.loadData(1);
              }else{
                that.$message.warning(res.message);
              }
            })
          }
        })
      },
      batchArchive(){
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return;
        } else {
          var ids = "";
          for (var a = 0; a < this.selectedRowKeys.length; a++) {
            ids += this.selectedRowKeys[a] + ",";
          }
          var that = this;
          this.$confirm({
            title: "确认归档",
            content: "是否归档选中数据?",
            onOk: function () {
              that.loading = true;
              postAction(that.url.archiveBatch, {ids: ids}).then((res) => {
                if (res.success) {
                  //重新计算分页问题
                  that.reCalculatePage(that.selectedRowKeys.length)
                  that.$message.success(res.message);
                  that.loadData(1);
                  that.onClearSelected();
                } else {
                  that.$message.warning(res.message);
                }
              }).finally(() => {
                that.loading = false;
              });
            }
          });
        }
      },
      searchReset(){
        console.log("wwww");
        if (this.archivingStatus){
          this.queryParam = {}
          this.queryParam.archivingStatus = "1";
          this.loadData(1);
        }else {
          this.queryParam = {};
          this.loadData(1);
        }
      },
      archiveList(){
        if (!this.archivingStatus){
          this.archivingStatus = true;
          this.columns = this.columns2;
          this.queryParam = {}
          this.queryParam.archivingStatus = "1";
          this.searchQuery();
        }else {
          this.archivingStatus = false;
          this.columns = this.columns1;
          this.searchReset();
        }
      },
      verificationDetail(mainId){
        getAction(this.url.queryByWarningCenterId, { id:mainId }).then(res=>{
          console.log(  "aaaaaa",res);
          if(res.success && res.result){
            let record = res.result;
            this.$refs.verification.edit(record);
            this.$refs.verification.title="核查详情";
            this.$refs.verification.disableSubmit = true;
          }else {
            this.$message.warning(res.message);
          }
        })
      },
      handleDetail:function(record){
        this.$refs.modalForm.edit(record);
        this.$refs.modalForm.fullscreen = true;
        this.$refs.modalForm.title="详情";
        this.$refs.modalForm.disableSubmit = true;
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
  .right-table{
    display: flex;
    justify-content: flex-end;
  }
</style>