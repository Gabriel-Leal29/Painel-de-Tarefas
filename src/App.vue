<template>
  <div>
      <Header/>
      <Main/>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Main from "./components/Main.vue";

export default{
  components:{
    Header,
    Main,
  }
}
</script>

<style>
*{
  margin:0;
  box-sizing: border-box;
  padding: 0;
  overflow-y: hidden;
}
</style>
