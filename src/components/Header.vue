<template>
    <header class="d-flex justify-content-center p-2 align-items-center">
        <h1>Painel de Tarefas</h1>
        <BtnAdicionar @clicou="emitirAdicionar"/>
        <BtnAdicionarColuna @adicionadaNovaColuna="emitirAdicionar"/>
    </header>
</template>

<style scoped>
header{
      background: #1e1e2f;
}
</style>


<script>
import BtnAdicionar from './btnAdicionar.vue';
import BtnAdicionarColuna from './BtnAdicionarColuna.vue';

export default {
    name: "Header",

    components:{
        BtnAdicionar,
        BtnAdicionarColuna
    },

    methods:{
        emitirAdicionar(){
            console.log('Evento clicou recebido no Header!');
            this.$emit('adicionar');
        }
    }
}
</script>