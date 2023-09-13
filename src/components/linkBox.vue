<template>
  <div class="link">
    <span>{{origin}}</span>
    <a :href="'https://'+short" target="_blank">{{short}}</a>
    <span class="copy" @click="copy" ref="copy"></span>
  </div>
</template>

<script>
export default{
  props:['origin' , 'short'],
  methods:{
    copy(){
      navigator.clipboard.writeText(this.short)
      this.$emit("copied")
      this.$refs.copy.classList.add("active")
    }
  },
  emits:['copied']
}
</script>