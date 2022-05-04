<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="事件单/案件编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sjbhAjbh">
              <a-input v-model="model.sjbhAjbh" placeholder="请输入事件单/案件编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="简要案情" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="briefing">
              <a-textarea rows="4" placeholder="请输入简要案情"  v-model="model.briefing" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="预警模型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alertCategory">
              <j-dict-select-tag type="list" v-model="model.alertCategory" dictCode="dc_model_store,name,id" placeholder="请选择预警模型" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="所属分局" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="companyName">
              <j-select-depart v-model="model.companyName" multi  />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="办案单位名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="caseUnit">
                <z-select-linkage  v-model="model.caseUnit" ></z-select-linkage>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="事件单编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="eventsNo">
              <a-input v-model="model.eventsNo" placeholder="请输入事件单编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="签收情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="signOff">
              <j-dict-select-tag type="list" v-model="model.signOff" dictCode="sign_off" placeholder="请选择签收情况" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="核查情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="verification">
              <j-dict-select-tag type="list" v-model="model.verification" dictCode="verification" placeholder="请选择核查情况" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="归档情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="archivingStatus">
              <j-dict-select-tag type="list" v-model="model.archivingStatus" dictCode="archiving_status" placeholder="请选择归档情况" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="报警时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmTime">
              <j-date placeholder="请选择报警时间"  v-model="model.alarmTime" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="联系电话" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="phone">
              <a-input v-model="model.phone" placeholder="请输入联系电话"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="接警单位编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jjdwbh">
              <a-input v-model="model.jjdwbh" placeholder="请输入接警单位编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="接警单位名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jjdwmc">
<!--              <a-input v-model="model.jjdwmc" placeholder="请输入接警单位名称"  ></a-input>-->
              <z-select-linkage  v-model="model.jjdwmc" ></z-select-linkage>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="接警案由名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jjaymc">
<!--              <a-input v-model="model.jjaymc" placeholder="请输入接警案由名称"  ></a-input>-->
              <j-dict-select-tag placeholder="请选择接警案由名称" v-model="model.jjaymc" dictCode="case_name" @change='jjaymcChange'/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="接线员工号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jxygh">
              <a-input v-model="model.jxygh" placeholder="请输入接线员工号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="接线员姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jxyxm">
              <a-input v-model="model.jxyxm" placeholder="请输入接线员姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="报警方式编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="bjfsbh">
              <a-input v-model="model.bjfsbh" placeholder="请输入报警方式编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="报警方式名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="bjfsmc">
              <a-input v-model="model.bjfsmc" placeholder="请输入报警方式名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="事发地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sfdz">
              <a-input v-model="model.sfdz" placeholder="请输入事发地址"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="行政区划编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="xzqhbh">
              <a-input v-model="model.xzqhbh" placeholder="请输入行政区划编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="地市名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="dsmc">
              <div class="z-area-linkage">
                <area-cascader type='all' v-model="model.dsmc" :data='pcaa' @change='areaChange' ></area-cascader>
              </div>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="警情级别" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jqjb">
              <a-input v-model="model.jqjb" placeholder="请输入警情级别"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="辖区单位编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="xqdwbh">
              <a-input v-model="model.xqdwbh" placeholder="请输入辖区单位编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="辖区单位编号名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="xqdwbhmc">
              <a-input v-model="model.xqdwbhmc" placeholder="请输入辖区单位编号名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="案由编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="aybh">
              <a-input v-model="model.aybh" placeholder="请输入案由编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="最新案由名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="zxaymc">
<!--              <a-input v-model="model.zxaymc" placeholder="请输入最新案由名称"  ></a-input>-->
              <j-dict-select-tag placeholder="请选择最新案由名称" v-model="model.zxaymc" dictCode="case_name" @change='zxaymcChange'/>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="事件详情" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sjxq">
              <a-textarea rows="4" v-model="model.sjxq" placeholder="请输入事件详情"  ></a-textarea>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="处理情况" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="clxq">
              <a-textarea rows="4" v-model="model.clxq" placeholder="请输入处理情况"  ></a-textarea>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="处理时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="clsj">
              <j-date placeholder="请选择处理时间"  v-model="model.clsj" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="处警单位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="cjdw">
              <a-input v-model="model.cjdw" placeholder="请输入处警单位"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="单位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="dw">
              <a-input v-model="model.dw" placeholder="请输入单位"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="处理结果编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="cljgbh">
              <a-input v-model="model.cljgbh" placeholder="请输入处理结果编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="案件编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ajbh">
              <a-input v-model="model.ajbh" placeholder="请输入案件编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="案件名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ajmc">
              <a-input v-model="model.ajmc" placeholder="请输入案件名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="案件类别代码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ajlbdm">
              <a-input v-model="model.ajlbdm" placeholder="请输入案件类别代码"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="受理时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="slsj">
              <j-date placeholder="请选择受理时间"  v-model="model.slsj" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="所属地市" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ssds">
              <a-input v-model="model.ssds" placeholder="请输入所属地市"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="受理单位代码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sldwdm">
              <a-input v-model="model.sldwdm" placeholder="请输入受理单位代码"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="受理单位名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sldwmc">
              <a-input v-model="model.sldwmc" placeholder="请输入受理单位名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="办案单位代码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="badwdm">
<!--              <a-input v-model="model.badwdm" placeholder="请输入办案单位代码"  ></a-input>-->
              <z-select-linkage  v-model="model.badwdm" ></z-select-linkage>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="主办人姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="zbrxm">
              <a-input v-model="model.zbrxm" placeholder="请输入主办人姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="主办人公民身份证号码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sfzhm">
              <a-input v-model="model.sfzhm" placeholder="请输入主办人公民身份证号码"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="主办人联系电话" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="zbrlxdh">
              <a-input v-model="model.zbrlxdh" placeholder="请输入主办人联系电话"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="案件类别名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ajlbmc">
              <a-input v-model="model.ajlbmc" placeholder="请输入案件类别名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="立案日期" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="larq">
              <j-date placeholder="请选择立案日期"  v-model="model.larq" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="立案单位代码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ladwdm">
              <a-input v-model="model.ladwdm" placeholder="请输入立案单位代码"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="立案单位名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ladwmc">
              <a-input v-model="model.ladwmc" placeholder="请输入立案单位名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="价值" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="jz">
              <a-input v-model="model.jz" placeholder="请输入价值"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction,putAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'
  const ruleBaseURL = '/sys/fillRule/executeRuleByCode/'
  export default {
    name: 'DcWarningCenterForm',
    components: {
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
          jjdwbh: ''
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
        validatorRules: {
        },
        url: {
          add: "/inspector/dcWarningCenter/add",
          edit: "/inspector/dcWarningCenter/edit",
          queryById: "/inspector/dcWarningCenter/queryById",
          rule: {
            orderNum: ruleBaseURL + 'unit_number_rule'
          },
        },
        pcaa: this.$Jpcaa,
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods: {
      add () {
        this.getOrderNum();
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            httpAction(httpurl,this.model,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
      areaChange(arr){
        console.log("data",arr)
        let keyList = [];
        let nameList = [];
        for (let i = 0; i < arr.length; i++) {
          let key = Object.keys(arr[i])[0]
          let value = arr[i][key]
          nameList.push(value);
          keyList.push(key)
        }
        console.log("name",name)
        this.model.dsmc = nameList[1];
        this.model.xzqhbh = keyList[1];
        console.log("model.dsmc",this.model.dsmc)
      },
      getOrderNum() {
        putAction(this.url.rule.orderNum, this.model).then(res => {
          // 执行成功，获取返回的值，并赋到页面上
          if (res.success) {
            this.model.jjdwbh = res.result
          }
        })
      },
      jjaymcChange(data){
        console.log(data)
      },
      zxaymcChange(data){
        console.log(data)
      }
    }
  }
</script>
<style lang="less" scoped>
.z-area-linkage {
  height:40px;
  /deep/ .area-cascader-wrap .area-select {
    width: 100%;
  }

  /deep/ .area-select .area-selected-trigger {
    line-height: 1.15;
  }
}

</style>