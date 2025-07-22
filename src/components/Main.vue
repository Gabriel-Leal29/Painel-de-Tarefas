<template>
    <main class="d-flex justify-content-center">
        <section class="painel row p-4 mt-2 mb-2 rounded">
            <cardTarefa v-for="coluna in colunas" :key="coluna.id" :titulo="coluna.title" :idColuna="coluna.id"
                :tarefas="tarefas.filter(tarefa => tarefa.columnId == coluna.id)" @tarefaDeletada="getDados"
                @colunaDeletada="getDados" @tarefaMovida="getDados" class="col-md-auto" />
        </section>
        <div v-show="erro" ref="modalAviso" class="modal" tabindex="-1" aria-hidden="true">
            <Mensagem :texto="texto" />
        </div>

    </main>
</template>

<style scoped>
main {
    background: #1e1e2f;
    height: 100vh;
}

.painel {
    background: #2c2c3e;
    gap: 40px;
    max-height: 90%;
}

.modal-body,
.modal-header,
.modal-footer {
    background: #1e1e2f;
}
</style>

<script>
import cardTarefa from "./CardTarefa.vue"
import axios from "axios"
import Mensagem from "./Mensagem.vue"

export default {
    name: "Main",

    components: {
        cardTarefa,
        Mensagem
    },

    data() {
        return {
            colunas: [],
            tarefas: [],
            erro: false,
            texto: "",
        }
    },

    methods: {
        async getDados() {
            try {
                const nomeColunas = await axios.get("http://localhost:3000/columns");
                this.colunas = nomeColunas.data;

                const tarefas = await axios.get("http://localhost:3000/tasks");
                this.tarefas = tarefas.data;
            } catch (erro) {
                console.log("Erro ao carregar os dados: " ,erro);
            }
        },
    },

    mounted() {
        this.getDados();
    }
}

</script>