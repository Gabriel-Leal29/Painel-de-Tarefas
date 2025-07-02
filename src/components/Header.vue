<template>
    <header class="d-flex justify-content-center p-2 border-bottom ">
        <h1>Painel de Tarefas</h1>
        <BtnAdicionar @clicou="emitirAdicionar"/>
    </header>
</template>

<script>
import BtnAdicionar from './btnAdicionar.vue';

export default {
    name: "Header",

    components:{
        BtnAdicionar
    },

    methods:{
        emitirAdicionar(){
            console.log('Evento clicou recebido no Header!');
            this.$emit('adicionar');
        }
    }
}
</script>