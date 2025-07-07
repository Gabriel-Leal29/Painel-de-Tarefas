<template>
    <header class="d-flex justify-content-center p-2 border-bottom ">
        <h1>Painel de Tarefas</h1>
        <BtnAdicionar @clicou="emitirAdicionar"/>
        <BtnAdicionarColuna @adicionadaNovaColuna="emitirAdicionar"/>
    </header>
</template>

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