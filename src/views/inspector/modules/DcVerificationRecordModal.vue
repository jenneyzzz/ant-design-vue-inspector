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
    <dc-verification-record-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"/>
  </j-modal>
</template>

<script>

  import DcVerificationRecordForm from './DcVerificationRecordForm'

  export default {
    name: 'DcVerificationRecordModal',
    components: {
      DcVerificationRecordForm
    },
    data() {
      return {
        title:'',
        width:800,
        visible: false,
        disableSubmit: false,
        mainId: '',
        fullscreen: false,
      }
    },
    methods:{
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

<style scoped>
</style>