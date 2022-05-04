<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="投诉简要内容">
              <j-input placeholder="请输入投诉简要内容" v-model="queryParam.tsjynr"></j-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="所属分局">
              <j-select-depart placeholder="请选择所属分局" v-model="queryParam.companyName"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="主办民警">
                <a-input placeholder="请输入主办民警" v-model="queryParam.zbmj"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="调查结论">
                <j-input placeholder="请输入调查结论" v-model="queryParam.verification"></j-input>
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
      <a-button @click="handleAdd" type="primary" icon="plus" v-has="'wire:add'">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('预警中心')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel" v-has="'wire:import'">
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
          <div v-has="'wire:edit'" >
             <a  @click="handleEdit(record)">编辑</a>
             <a-divider type="vertical" />
          </div>
          <div v-has="'wire:inspector'"  v-if='record.archivingStatus == 0'>
             <a  @click="cluesEdit(record.id,'线索核查')">督察</a>
             <a-divider type="vertical" />
          </div>
          <div v-has="'wire:verification'" v-if='record.archivingStatus != 1'>
            <a v-if='record.verification == 0' @click="verificationAdd(record.id)">核查</a>
            <a v-else @click="verificationEdit(record.id,'核查')">编辑核查</a>
            <a-divider type="vertical" />
          </div>
          <div v-has="'wire:archive'" v-if='record.archivingStatus == 0'>
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

    <dc-wire-center-modal ref="modalForm" @ok="modalFormOk"></dc-wire-center-modal>
    <dc-wire-verification-modal ref="verification" @ok="modalFormOk"/>
    <!-- 指派线索   -->
    <dc-verification-clues-modal ref="clues" @ok="modalFormOk"></dc-verification-clues-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import DcWireCenterModal from './modules/DcWireCenterModal'
  import DcWireVerificationModal from '@views/inspector/modules/DcWireVerificationModal'
  import { deleteAction, getAction, postAction, putAction } from '@/api/manage'
  import DcVerificationCluesModal from './modules/DcVerificationCluesModal'

  export default {
    name: 'DcWireCenterList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      DcWireVerificationModal,
      DcWireCenterModal,
      DcVerificationCluesModal
    },
    data () {
      return {
        description: '线索中心管理页面',
        // 表头
        columns: [],
        columns1: [
          {
            title:'受理时间',
            align:"center",
            dataIndex: 'slsj',
            width: 100,
            ellipsis: true,
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'受理方式',
            align:"center",
            dataIndex: 'admissibility_dictText',
            width: 170,
            ellipsis: true
          },
          {
            title:'举报人信息',
            align:"center",
            width: 100,
            dataIndex: 'jbrxx',
          },
          {
            title:'投诉简要内容',
            align:"center",
            dataIndex: 'tsjynr',
            width: 200,
            ellipsis: true
          },
          {
            title:'所属分局',
            align:"center",
            dataIndex: 'companyName_dictText',
            ellipsis: true
          },
          {
            title:'部门',
            align:"center",
            ellipsis: true,
            dataIndex: 'caseUnit'
          },
          {
            title:'主办民警',
            align:"center",
            dataIndex: 'zbmj',

            ellipsis: true
          },
          {
            title:'调查结论',
            align:"center",
            dataIndex: 'verification',
            ellipsis: true
          },
          {
            title:'归档情况',
            align:"center",
            width: 80,
            dataIndex: 'archivingStatus_dictText',
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
            title:'受理时间',
            align:"center",
            dataIndex: 'slsj',
            width: 100,
            ellipsis: true,
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'受理方式',
            align:"center",
            dataIndex: 'admissibility_dictText',
            width: 170,
            ellipsis: true
          },
          {
            title:'举报人信息',
            align:"center",
            width: 100,
            dataIndex: 'jbrxx',
          },
          {
            title:'投诉简要内容',
            align:"center",
            dataIndex: 'tsjynr',
            width: 200,
            ellipsis: true
          },
          {
            title:'所属分局',
            align:"center",
            dataIndex: 'companyName_dictText',
            ellipsis: true
          },
          {
            title:'部门',
            align:"center",
            ellipsis: true,
            dataIndex: 'caseUnit'
          },
          {
            title:'主办民警',
            align:"center",
            dataIndex: 'zbmj',

            ellipsis: true
          },
          {
            title:'调查结论',
            align:"center",
            dataIndex: 'verification',
            ellipsis: true
          },
          {
            title:'归档情况',
            align:"center",
            width: 80,
            dataIndex: 'archivingStatus_dictText',
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
        url: {
          list: "/inspector/dcWireCenter/list",
          delete: "/inspector/dcWireCenter/delete",
          deleteBatch: "/inspector/dcWireCenter/deleteBatch",
          exportXlsUrl: "/inspector/dcWireCenter/exportXls",
          importExcelUrl: "inspector/dcWireCenter/importExcel",
          queryByWarningCenterId: '/inspector/dcWireVerification/queryByWireCenterId',
          queryById: '/inspector/dcWireCenter/queryById',
          archiveBatch: '/inspector/dcWireCenter/archiveBatch'
        },
        dictOptions:{},
        superFieldList:[],
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
        fieldList.push({type:'date',value:'slsj',text:'受理时间'})
        fieldList.push({type:'date',value:'hfsx',text:'回复时限'})
        fieldList.push({type:'string',value:'admissibility',text:'受理方式',dictCode:''})
        fieldList.push({type:'string',value:'code',text:'编号',dictCode:''})
        fieldList.push({type:'string',value:'jbrxx',text:'举报人信息',dictCode:''})
        fieldList.push({type:'string',value:'phone',text:'联系电话',dictCode:''})
        fieldList.push({type:'string',value:'tsjynr',text:'投诉简要内容',dictCode:''})
        fieldList.push({type:'string',value:'companyName',text:'所属分局',dictCode:''})
        fieldList.push({type:'string',value:'caseUnit',text:'部门',dictCode:''})
        fieldList.push({type:'string',value:'zbmj',text:'主办民警',dictCode:''})
        fieldList.push({type:'string',value:'xbmj',text:'协办民警',dictCode:''})
        fieldList.push({type:'string',value:'verification',text:'调查结论',dictCode:''})
        fieldList.push({type:'string',value:'hcbg',text:'核查报告',dictCode:''})
        fieldList.push({type:'string',value:'remark',text:'备注',dictCode:''})
        fieldList.push({type:'date',value:'bjgdsj',text:'办结归档时间'})
        fieldList.push({type:'string',value:'archivingStatus',text:'归档情况',dictCode:''})
        fieldList.push({type:'string',value:'xz',text:'性质',dictCode:''})
        fieldList.push({type:'string',value:'fssj',text:'发生时间',dictCode:''})
        fieldList.push({type:'string',value:'zrzj',text:'责任追究',dictCode:''})
        fieldList.push({type:'string',value:'clqk',text:'处理情况',dictCode:''})
        fieldList.push({type:'string',value:'hfqk',text:'回访情况',dictCode:''})
        fieldList.push({type:'string',value:'signOff',text:'签收情况',dictCode:''})
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
      cluesEdit(mainId,title){
        getAction(this.url.queryById, { id:mainId }).then(res=>{
          if(res.success && res.result){
            this.$refs.clues.edit(res.result);
            // this.$refs.verification.fullscreen = true;
            this.$refs.clues.title = "编辑" + title;
            this.$refs.clues.disableSubmit = false;
          }else {
            this.$refs.clues.edit({ id:mainId });
            this.$refs.clues.title = "编辑" + title;
            this.$refs.clues.disableSubmit = false;
          }
        })
      }
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