<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="人员类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="userType">
              <a-radio-group v-model="model.userType">
                <a-radio :value="0" label="内部人员">内部人员</a-radio>
                <a-radio :value="1" label="外部人员">外部人员</a-radio>
              </a-radio-group>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="name">
              <a-input v-model="model.name" placeholder="请输入姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          
          <a-col :span="12">
            <a-form-model-item label="性别" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sex">
              <a-radio-group v-model="model.sex">
                <a-radio :value="1" label="男">男</a-radio>
                <a-radio :value="2" label="女">女</a-radio>
              </a-radio-group>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="出生日期" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="birthday">
              <j-date placeholder="请选择出生日期" v-model="model.birthday"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="idNo">
              <a-input placeholder="请输入身份证号" v-model="model.idNo"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="职务" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="post">
              <j-select-position placeholder="请选择职务" :multiple="false" v-model="model.post" :userType="model.userType" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item v-if="model.userType!==1" label="工号/警号" prop="policeNo" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-model="model.policeNo" placeholder="请输入工号/警号"  ></a-input>
            </a-form-model-item>
            <a-form-model-item v-else label="工号" prop="policeNum" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-model="model.policeNo" placeholder="工号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="学历" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="education">
              <j-dict-select-tag type="list" v-model="model.education" dictCode="education" placeholder="请选择学历" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="毕业专业" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="specialty">
              <a-select placeholder="请选择毕业专业" v-model="model.specialty">
                <a-select-option v-for="items in specialty_list" :key="items.label" :value="items.value">
                    {{items.label}}
                </a-select-option>
              </a-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="毕业院校" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="graduateFrom">
              <a-select placeholder="请选择毕业院校" v-model="model.graduateFrom">
                <a-select-option v-for="items in graduateFromList" :key="items.label" :value="items.value">
                    {{items.label}}
                </a-select-option>
              </a-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="特长" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="expertise">
              <a-input v-model="model.expertise" placeholder="请输入特长"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="在岗状态" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="workStatus">
              <j-dict-select-tag type="list" v-model="model.workStatus" dictCode="work_status" placeholder="请选择在岗状态" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="办案经验" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="handleCaseExper">
              <a-select placeholder="请选择办案经验" v-model="model.handleCaseExper">
                <a-select-option v-for="items in handleCaseExperList" :key="items.label" :value="items.value">
                    {{items.label}}
                </a-select-option>
              </a-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="手机号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="phone">
              <a-input v-model="model.phone" placeholder="请输入手机号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="所属单位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="unit">
              <j-select-depart v-model="model.unit" multi  />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="所属部门" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="departId">
              <j-select-depart v-model="model.departId" multi  />
            </a-form-model-item>
          </a-col>
          <a-col :span="12">
            <a-form-model-item label="头像" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="avatar">
              <j-image-upload isMultiple  v-model="model.avatar" ></j-image-upload>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction, getDictItemsByCode } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'SysPersonnelForm',
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
          userType: [
              { required: true, message: '请选择人员类型!'},
           ],
           name: [
              { required: true, message: '请输入姓名!'},
           ],
           sex: [
              { required: true, message: '请输入性别!'},
           ],
           post: [
              { required: true, message: '请输入职务!'},
           ],
           policeNum: [
              { required: false, message: ''},
           ],
           policeNo: [
              { required: true, message: '请输入工号/警号!'},
           ],
           education: [
              { required: true, message: '请输入学历!'},
           ],
           specialty: [
              { required: true, message: '请选择专业!'},
           ],
           workStatus: [
              { required: true, message: '请输入在岗状态!'},
           ],
           idNo: [
              { required: true, message: '请输入身份证号!'},
           ],
           phone: [
              { required: true, message: '请输入手机号!'},
              { pattern: /^1[3456789]\d{9}$/, message: '请输入正确的手机号码!'},
           ],
           unit: [
              { required: true, message: '请输入所属单位!'},
           ]
        },
        url: {
          add: "/system/sysPersonnel/add",
          edit: "/system/sysPersonnel/edit",
          queryById: "/system/sysPersonnel/queryById"
        },
        specialty_list:[], // 毕业专业
        graduateFromList:[],// 毕业院校
        handleCaseExperList:[] // 办案经验
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
    mounted(){
      this.getDictData({code:'bananjingyan'});
      this.getDictData({code:'biyezhuanye'});
      this.getDictData({code:'biyeyuanxiao'});
    },
    methods: {
      add () {
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
      getDictData(param){
        getDictItemsByCode(param).then((res)=>{
          switch(param.code){
            case "bananjingyan":
              this.handleCaseExperList = res
              break;
            case "biyeyuanxiao":
              this.graduateFromList = res
              break;
            case "biyezhuanye":
              this.specialty_list = res
              break;
          }
        })
      }
    }
  }
</script>