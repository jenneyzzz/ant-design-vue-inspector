<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="部门" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="department">
              <a-input v-model="model.department" placeholder="请输入部门"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="系统名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="name">
              <a-input v-model="model.name" placeholder="请输入系统名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="标语（逗号分隔）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="slogan">
              <a-textarea v-model="model.slogan" rows="4" placeholder="请输入标语（逗号分隔）" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="标签（逗号分隔）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tag">
              <a-textarea v-model="model.tag" rows="4" placeholder="请输入标签（逗号分隔）" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="推进浏览器图标" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="browserIcon">
              <j-image-upload isMultiple  v-model="model.browserIcon" ></j-image-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="兼容浏览器" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="compatibleBrowser">
              <a-input v-model="model.compatibleBrowser" placeholder="请输入兼容浏览器"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'SiteInfoForm',
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
        },
        url: {
          add: "/system/siteInfo/add",
          edit: "/system/siteInfo/edit",
          queryById: "/system/siteInfo/queryById"
        }
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
    }
  }
</script>