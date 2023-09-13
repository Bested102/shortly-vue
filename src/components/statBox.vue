<template>
  <div>
    <div class="img">
      <img :src='imgPath'/>
    </div>
      <h3>{{title}}</h3>
      <p>
        {{content}}
      </p>
  </div>
</template>

<script>
  export default{
    props:['img' , 'title' , 'content'],
    computed:{
      imgPath(){
        return new URL(`/src/assets/images/${this.img}` ,import.meta.url).href
      }
    }
  }
</script>