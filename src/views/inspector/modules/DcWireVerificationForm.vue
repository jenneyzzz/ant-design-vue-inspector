<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24" >
            <a-form-model-item label="核查人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="verifier">
              <j-select-personnel v-model="model.verifier" :multiple="false"  ></j-select-personnel>
            </a-form-model-item>
          </a-col>
          <a-col :span="24" >
            <a-form-model-item label="核查时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="verificationTime">
              <j-date placeholder="请选择核查时间" v-model="model.verificationTime" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24" >
            <a-form-model-item label="核查方式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="method">
              <j-multi-select-tag type="list_multi" v-model="model.method" dictCode="verification_method" placeholder="请选择核查方式" @change='verificationChange' />
            </a-form-model-item>
          </a-col>
          <a-col :span="24" >
            <a-form-model-item label="核查结论" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="conclusion">
              <j-dict-select-tag type="list" v-model="model.conclusion" dictCode="verification_conclusion" placeholder="请选择核查结论" @change='conclusionChange' />
            </a-form-model-item>
          </a-col>
          <a-col :span="24" >
            <a-form-model-item label="核查报告" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="report">
              <j-upload v-model="model.report"  ></j-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24" >
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="remark">
              <a-textarea v-model="model.remark" rows="4" placeholder="请输入备注" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
    <!-- 子表单区域 -->
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <a-tab-pane tab="问责情况" :key="refKeys[0]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[0]"
          :loading="dcWireAccountabilityTable.loading"
          :columns="dcWireAccountabilityTable.columns"
          :dataSource="dcWireAccountabilityTable.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="false"
          :rowSelection="true"
          :toolbar="true"
          @added='sumRows'
        />
        <table>
          <tr>
            <th > <div class='sumText'>问责人数：</div></th>
            <th> <div class='sumText' >{{ numberRows }} 人</div></th>
          </tr>
        </table>
      </a-tab-pane>
    </a-tabs>
  </a-spin>
</template>

<script>

import { getAction } from '@/api/manage'
import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
import { JVXETypes } from '@/components/jeecg/JVxeTable'
import { getRefPromise,VALIDATE_FAILED} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
import { validateDuplicateValue } from '@/utils/util'
import JFormContainer from '@/components/jeecg/JFormContainer'

  export default {
    name: 'DcWireVerificationForm',
    mixins: [JVxeTableModelMixin],
    components: {
      JFormContainer,
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
        addDefaultRowNum: 0,
        validatorRules: {
          verifier: [
            { required: true, message: '请输入核查人!'},
          ],
          verificationTime: [
            { required: true, message: '请输入核查时间!'},
          ],
          method: [
            { required: true, message: '请输入核查方式!'},
          ],
          conclusion: [
            { required: true, message: '请输入核查结论!'},
          ],
          remark: [],

        },
        refKeys: ['dcWireAccountability', ],
        tableKeys:['dcWireAccountability', ],
        activeKey: 'dcWireAccountability',
        // 问责情况
        dcWireAccountabilityTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '姓名',
              key: 'name',
              type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true,message: '${title}不能为空'}],
            },
            {
              title: '性别',
              key: 'sex',
              type: JVXETypes.select,
              options:[],
              dictCode:"sex",
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true,message: '${title}不能为空'}]
            },
            {
              title: '电话号码',
              key: 'phone',
              type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ pattern: "m", message: "${title}格式不正确" }],
            },
            {
              title: '警号/编号',
              key: 'number',
              type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true,message: '${title}不能为空'}]
            },
            {
              title: '职务',
              key: 'positionId',
              type: JVXETypes.select,
              options:[],
              dictCode:"position_rank",
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true,message: '${title}不能为空'}]
            },
            {
              title: '措施',
              key: 'measures',
              type: JVXETypes.selectMultiple,
              options:[],
              dictCode:"accountability_measures",
              width:"340px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true,message: '${title}不能为空'}]
            },
          ]
        },
        url: {
          add: "/inspector/dcWireVerification/add",
          edit: "/inspector/dcWireVerification/edit",
          queryById: "/inspector/dcWireVerification/queryById",
          dcWireAccountability: {
            list: '/inspector/dcWireVerification/queryDcWireAccountabilityByMainId'
          },
        },
        numberRows: 0,
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
        this.dcWireAccountabilityTable.dataSource=[]
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
          this.requestSubTableData(this.url.dcWireAccountability.list, params, this.dcWireAccountabilityTable,this.sumRows)

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
          dcWireAccountabilityList: allValues.tablesValue[0].tableData,
        }
      },
      validateError(msg){
        this.$message.error(msg)
      },
      conclusionChange(val){
        this.$refs.dcWireAccountability.removeRows();
        //为必填项
        if (val === "1" || val === "3" || val === "4"){
          for (let i = 0; i < this.dcWireAccountabilityTable.columns.length; i++) {
            this.$set(this.dcWireAccountabilityTable.columns[i],"validateRules",[]);
          }
          this.$refs.dcWireAccountability.addRows();
        }
      },
      verificationChange(val){
        if (val.indexOf('8') !== -1){
          this.$set(this.validatorRules.remark,0,{ required: true, message: '请输入备注!'})
        }else {
          this.$set(this.validatorRules.remark,0,{})
        }
      },
      sumRows(data){

        if (data.code === 200){
          this.numberRows = data.result.length
          return
        }
        if ( typeof(this.$refs.dcWireAccountability) != 'undefined'){
          let list = this.$refs.dcWireAccountability.getAll();
          this.numberRows  = list.tableData.length
        }
     }
    }
  }
</script>
<style scoped>
  .sumText{
    color: rgba(0, 0, 0, 0.85);
    line-height: 24px;
    text-align: center;
    padding: 0 10px;
  }
</style>