<template>
    <main class="d-flex justify-content-center">
        <section class="painel d-flex p-4 mt-4 rounded row">
            <cardTarefa v-for="coluna in colunas" :key="coluna.id"
            :titulo="coluna.title"
            :tarefas="tarefas.filter(tarefa => tarefa.columnId == coluna.id)"
            @tarefaDeletada="getDados"
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
import cardTarefa from "./CardTarefa.vue"
import axios from "axios"

export default {
    name: "Main",

    components: {
        cardTarefa,
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
    },

    mounted(){
        this.getDados();
    }
}

</script>