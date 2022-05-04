<template>
    <a-row :gutter="[16,16]">
      <a-col :span="12">
        <a-select placeholder="请选择分局" :value="getValueSting1" @change="getDepartByUnit"  v-model:value="value1">
          <a-select-option :value="undefined">请选择</a-select-option>
          <a-select-option v-for="(item, key) in dictOptions1" :key="key" :value="item.value">
           <span style="display: inline-block;width: 100%" :title=" item.text || item.label ">
            {{ item.text || item.label }}
           </span>
          </a-select-option>
        </a-select>
      </a-col>

    <a-col :span="12">
    <a-select placeholder="请选择单位" :value="getValueSting2" v-model:value="value2" @change='handleInput'>
      <a-select-option :value="undefined">请选择</a-select-option>
      <a-select-option v-for="(item, key) in dictOptions2" :key="key" :value="item.value">
      <span style="display: inline-block;width: 100%" :title=" item.text || item.label ">
        {{ item.text || item.label }}
      </span>
      </a-select-option>
    </a-select>
    </a-col>
    </a-row>

</template>

<script>
import { httpAction, getAction,putAction } from '@/api/manage'


export default {
  name: 'ZSelectLinkage',
  props: {
    value: [String, Number],
    getPopupContainer:{
      type: Function,
      default: (node) => node.parentNode
    }
  },
  data() {
    return {
      dictOptions1: [],
      dictOptions2: [],
      value1: '',
      value2: ''
    }
  },
  computed: {
    getValueSting1(){
      // update-begin author:wangshuai date:20200601 for: 不显示placeholder的文字 ------
      // 当有null或“” placeholder不显示
      return this.value1 != null ? this.value1.toString() : undefined;
      // update-end author:wangshuai date:20200601 for: 不显示placeholder的文字 ------
    },
    getValueSting2(){
      // update-begin author:wangshuai date:20200601 for: 不显示placeholder的文字 ------
      // 当有null或“” placeholder不显示
      return this.value != null ? this.value.toString() : undefined;
      // update-end author:wangshuai date:20200601 for: 不显示placeholder的文字 ------
    },
  },
  created() {
    this.initDictData()
  },
  watch:{
    value:{
      immediate:true,
      handler() {
        if (this.value){
          this.initDictData();
          console.log("thiaisd",this.value)
          this.getDepartByid();
        }

      },
    }
  },
  methods: {
    initDictData(){
      getAction(`/sys/sysDepart/getDepartByBranch`,{}).then(res=>{
        if (res.success) {
          this.dictOptions1 = res.result
        } else {
          console.error('getDictItems error: : ', res)
          this.dictOptions1 = []
        }
      })
    },
    getDepartByUnit(id){
      getAction(`/sys/sysDepart/getDepartByUnit`,{id:id}).then(res=>{
        if (res.success) {
          console.log("res.result",res.result)
          this.dictOptions2 = res.result
        } else {
          console.error('getDictItems error: : ', res)
          this.dictOptions2 = []
        }
      })
    },
    getDepartByid(){
      getAction(`/sys/sysDepart/listAll`,{id:this.value}).then(res=>{
        if (res.success) {
         this.value1 =  res.result[0].parentId;
         this.value2 = res.result[0].id;
          this.getDepartByUnit(this.value1);
        }
      })
    },
    handleInput(e='') {
      let val;
      if(Object.keys(e).includes('target')){
        val = e.target.value
      }else{
        val = e
      }
      console.log(val);
      this.$emit('change', val);
      //LOWCOD-2146 【菜单】数据规则，选择自定义SQL 规则值无法输入空格
      this.$emit('input', val);
    },
   },
  model:{
    prop: 'value',
    event: 'change'
  }
}
</script>

<style scoped>

</style>