<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"
    @cancel="handleCancel"
    cancelText="关闭">
    <dc-verification-clues-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></dc-verification-clues-form>
  </j-modal>
</template>

<script>

  import DcVerificationCluesForm from './DcVerificationCluesForm'
  export default {
    name: 'DcVerificationCluesModal',
    components: {
      DcVerificationCluesForm
    },
    data () {
      return {
        title:'',
        width:800,
        visible: false,
        disableSubmit: false,
        mainId: '',
      }
    },
    methods: {
      add () {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
          this.$refs.realForm.model.wireCenterId = this.mainId;
        })
      },
      edit (record) {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.edit(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$refs.realForm.submitForm();
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>