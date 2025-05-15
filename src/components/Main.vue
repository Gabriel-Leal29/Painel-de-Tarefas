<template>
    <main class="d-flex justify-content-center">
        <section class="painel d-flex p-4 mt-4 rounded row">

            <Coluna v-for="coluna in colunas" :key="coluna.id"
            :titulo="coluna.title"
            :tarefas="pegarTarefas(coluna.id)"
            />

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
        async getDados(){
            try {
                const nomeColunas = await axios.get("http://localhost:3000/columns");
                this.colunas = nomeColunas.data;

                const tarefas = await axios.get("http://localhost:3000/tasks");
                this.tarefas = tarefas.data;
            }catch(erro){
                console.log("Erro ao carregas os dados: "+erro);
            }
        },

        pegarTarefas(idColuna){
            let tarefasDaColuna = [];

            for(let i=0;i<this.tarefas.length;i++){
                if(this.tarefas[i].columnId==idColuna){
                    tarefasDaColuna.push(this.tarefas[i]);
                }
            }
            return tarefasDaColuna;
        }
    },

    mounted(){
        this.getDados();
    }
}

</script>