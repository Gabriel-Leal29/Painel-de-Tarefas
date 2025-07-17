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

p,h1,h2,button,label, .modal-title,header{
    color: #f1f1f1;
}

.configButton {
  margin-left: 3rem;
  padding: 0.5rem;
  border-radius: 0.375rem;
  background: #6366f1;
}

option{
  color:black;
}

.modal{
    backdrop-filter: blur(3px);
}
</style>
