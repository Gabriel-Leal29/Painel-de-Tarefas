<template>
    <main class="d-flex justify-content-center">
        <section class="painel d-flex p-4 mt-4 rounded">

            <Coluna v-for="(coluna,id) in colunas" :key="id" :titulo="coluna.title"/>

        </section>
    </main>
</template>

<style>
main {
    background: #D3D3D3;
    height: 100vh;
}

.painel {
    background: white;
    gap: 40px;
    width: 80%;
    height: 80%;
}
</style>

<script>
import Coluna from "./Coluna.vue"
import axios from "axios"

export default {
    name: "Main",

    components: {
        Coluna,
    },

    data() {
        return {
            colunas: [],
            tarefas:[],
        }
    },

    methods: {
        async getColunas() {
            try {
                const resp = await axios.get("http://localhost:3000/columns");
                this.colunas = resp.data;
            }catch(erro){
                console.log("Erro ao carregas as colunas: "+e);
            }
        },

        async getTarefas(){
            try{
                const resp = axios.get("http://localhost:3000/tasks");
                this.tarefas = resp;
            }catch(erro){
                console.log("Erro ao pegar as tarefas: "+e);
            }
        }
    },

    mounted(){
        this.getColunas();
        this.getTarefas();
    }
}

</script>