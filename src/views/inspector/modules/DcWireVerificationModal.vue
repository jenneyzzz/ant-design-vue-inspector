<template>
  <j-modal
    :title="title"
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"
    :fullscreen='fullscreen'
    @cancel="handleCancel">
    <dc-wire-verification-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></dc-wire-verification-form>
  </j-modal>
</template>

<script>

  import DcWireVerificationForm from './DcWireVerificationForm'
  export default {
    name: 'DcWireVerificationModal',
    components: {
      DcWireVerificationForm
    },
    data () {
      return {
        title:'',
        width:800,
        visible: false,
        disableSubmit: false,
        mainId: '',
        fullscreen: false,
      }
    },
    methods: {
      add () {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
          this.$refs.realForm.model.warningCenterId = this.mainId;
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
        this.$refs.realForm.handleOk();
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