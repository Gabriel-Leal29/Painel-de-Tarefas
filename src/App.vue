<template>
  <div>
      <Header @adicionar="chamarFuncaoMain"/>
      <Main ref="mainComponente"/>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Main from "./components/Main.vue";

export default{
  components:{
    Header,
    Main,
  },

  methods:{
    chamarFuncaoMain(){
      console.log("evento recebido no app.vue")
      this.$refs.mainComponente.getDados();
    },
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
