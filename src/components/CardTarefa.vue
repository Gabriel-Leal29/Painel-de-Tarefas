<template>
    <section class="principal text-center rounded col-auto">
        <h2>{{ titulo }}</h2>

        <p class="border border-1 rounded p-2" v-for="tarefa in tarefas" :key="tarefa.id"
         @click="abrirModal(tarefa)">
            {{ tarefa.title }}
        </p>

        <div class="modal fade" id="ModalTarefa" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true"
            ref="modalTarefa">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">
                            Editar Tarefa
                        </h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <!--Titulo da tarefa-->
                        <div class="mb-3 row">
                            <label for="tituloTarefa" class="col-sm-2 col-form-label">Título:</label>
                            <div class="col-sm-10">
                                <input type="text" readonly class="form-control" id="tituloTarefa"
                                    :value="tarefaModal.title" v-model="novoTitle">
                            </div>
                        </div>
                        <!--Descricao da tarefa-->
                        <div class="mb-3 row">
                            <label for="tituloTarefa" class="col-sm-2 col-form-label">Descrição:</label>
                            <div class="col-sm-10">
                                <input type="text" readonly class="form-control" id="tituloTarefa"
                                    :value="tarefaModal.description" v-model="novaDescription">
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
                            Remover
                        </button>
                        <button type="button" class="btn btn-primary">Salvar</button>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import axios from "axios"
import { Modal } from "bootstrap";

export default {
    name: "CardTarefa",

    props: {
        titulo: String,
        tarefas: Array,
    },

    data() {
        return {
            tarefaModal: {},
            novoTitle:"",
            novaDescription:"",

        };
    },

    mounted() {
        this.bsModalInstance = new Modal(this.$refs.modalTarefa);
    },

    methods: {
        abrirModal(tarefa) {
            this.tarefaModal = tarefa;
            this.$nextTick(() => {
                this.bsModalInstance.show();
            });
        },

        async editarTarefa(){
            axios.put("http://localhost:3000/tasks/"+this.tarefaModal.id,{

            })
        }
    },
};
</script>

<style scoped>
.principal {
    background: white;
    border: 2px solid black;
}

h2 {
    border-bottom: 2px solid rgb(180, 178, 178);
}

p {
    transition: 0.5s;
    cursor: pointer;
}

p:hover {
    background: rgb(56, 55, 55);
    color: white;
}

label{
    overflow-x: hidden;
}
</style>
