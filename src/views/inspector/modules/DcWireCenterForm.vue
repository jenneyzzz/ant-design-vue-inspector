<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="受理时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="slsj">
              <j-date placeholder="请选择受理时间" v-model="model.slsj"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="回复时限" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="hfsx">
              <j-date placeholder="请选择回复时限" v-model="model.hfsx"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="受理方式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="admissibility">
              <j-dict-select-tag type="list" v-model="model.admissibility" dictCode="admissibility" placeholder="请选择受理方式" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="code">
              <a-input v-model="model.code" placeholder="请输入编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="举报人信息" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jbrxx">
              <a-input v-model="model.jbrxx" placeholder="请输入举报人信息"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="联系电话" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="phone">
              <a-input v-model="model.phone" placeholder="请输入联系电话"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="投诉简要内容" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tsjynr">
              <a-textarea v-model="model.tsjynr" rows="4" placeholder="请输入投诉简要内容" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="所属分局" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="companyName">
              <j-select-depart v-model="model.companyName" multi  />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="部门" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="caseUnit">
              <a-input v-model="model.caseUnit" placeholder="请输入部门"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="主办民警" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="zbmj">
<!--              <a-input v-model="model.zbmj" placeholder="请输入主办民警"  ></a-input>-->
              <j-select-personnel v-model="model.zbmj" :multiple="false" :returnId='false'></j-select-personnel>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="协办民警" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="xbmj">
<!--              <a-input v-model="model.xbmj" placeholder="请输入协办民警"  ></a-input>-->
              <j-select-personnel v-model="model.xbmj" :multiple="true" :returnId='false'></j-select-personnel>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="调查结论" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="verification">
              <a-input v-model="model.verification" placeholder="请输入调查结论"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="核查报告" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="hcbg">
              <j-upload v-model="model.hcbg"   ></j-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="remark">
              <a-textarea v-model="model.remark" rows="4" placeholder="请输入备注" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="办结归档时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="bjgdsj">
              <j-date placeholder="请选择办结归档时间" v-model="model.bjgdsj"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="归档情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="archivingStatus">
              <j-dict-select-tag type="list" v-model="model.archivingStatus" dictCode="archiving_status" placeholder="请选择归档情况" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="性质" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="xz">
              <a-input v-model="model.xz" placeholder="请输入性质"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="发生时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="fssj">
              <a-input v-model="model.fssj" placeholder="请输入发生时间"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="责任追究" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="zrzj">
              <a-textarea v-model="model.zrzj" rows="4" placeholder="请输入责任追究" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="处理情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="clqk">
              <a-textarea v-model="model.clqk" rows="4" placeholder="请输入处理情况" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="回访情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="hfqk">
              <a-textarea v-model="model.hfqk" rows="4" placeholder="请输入回访情况" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
    <!-- 子表单区域 -->
    <a-tabs v-model="activeKey" @change="handleChangeTabs" v-show='formDisabled' >
      <a-tab-pane tab="更新记录" :key="refKeys[0]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[0]"
          :loading="dcWireLogTable.loading"
          :columns="dcWireLogTable.columns"
          :dataSource="dcWireLogTable.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
        />
      </a-tab-pane>
    </a-tabs>
  </a-spin>
</template>

<script>

import { getAction, httpAction } from '@/api/manage'
  import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
  import { JVXETypes } from '@/components/jeecg/JVxeTable'
  import { getRefPromise,VALIDATE_FAILED} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'

  export default {
    name: 'DcWireCenterForm',
    mixins: [JVxeTableModelMixin],
    components: {
      JFormContainer
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        // 新增时子表默认添加几行空数据
        addDefaultRowNum: 1,
        validatorRules: {
        },
        refKeys: ['dcWireLog', ],
        tableKeys:['dcWireLog', ],
        activeKey: 'dcWireLog',
        // 线索中心更新记录
        dcWireLogTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '时间',
              key: 'submitTime',
              type: JVXETypes.datetime,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '更新账号',
              key: 'createBy',
              type: JVXETypes.userSelect,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '更新内容',
              key: 'content',
              type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            // {
            //   title: '附件',
            //   key: 'filelist',
            //   type: JVXETypes.file,
            //   token:true,
            //   responseName:"message",
            //   width:"200px",
            //   placeholder: '请选择文件',
            //   defaultValue:'',
            // },
          ]
        },
        url: {
          add: "/inspector/dcWireCenter/add",
          edit: "/inspector/dcWireCenter/edit",
          queryById: "/inspector/dcWireCenter/queryById",
          dcWireLog: {
            list: '/inspector/dcWireCenter/queryDcWireLogByMainId'
          },
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
    },
    methods: {
      addBefore(){
        this.dcWireLogTable.dataSource=[]
      },
      getAllTable() {
        let values = this.tableKeys.map(key => getRefPromise(this, key))
        return Promise.all(values)
      },
      /** 调用完edit()方法之后会自动调用此方法 */
      editAfter() {
        this.$nextTick(() => {
        })
        // 加载子表数据
        if (this.model.id) {
          let params = { id: this.model.id }
          this.requestSubTableData(this.url.dcWireLog.list, params, this.dcWireLogTable)
        }
      },
      //校验所有一对一子表表单
      validateSubForm(allValues){
        return new Promise((resolve,reject)=>{
          Promise.all([
          ]).then(() => {
            resolve(allValues)
          }).catch(e => {
            if (e.error === VALIDATE_FAILED) {
              // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
              this.activeKey = e.index == null ? this.activeKey : this.refKeys[e.index]
            } else {
              console.error(e)
            }
          })
        })
      },
      /** 整理成formData */
      classifyIntoFormData(allValues) {
        let main = Object.assign(this.model, allValues.formValue)
        return {
          ...main, // 展开
          dcWireLogList: allValues.tablesValue[0].tableData,
        }
      },
      validateError(msg){
        this.$message.error(msg)
      },
    }
  }
</script>