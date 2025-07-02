<template>
    <header class="d-flex justify-content-center p-2 border-bottom ">
        <h1>Painel de Tarefas</h1>
        <btnAdicionar/>
    </header>
</template>

<script>
import axios from 'axios'
import btnAdicionar from './btnAdicionar.vue';

export default {
    name: "Header",

    components:{
        btnAdicionar
    }
}
</script>