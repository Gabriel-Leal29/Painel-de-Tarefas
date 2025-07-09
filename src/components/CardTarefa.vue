<template>
    <section class="principal text-center rounded col-auto">
                <!-- :data-bs-target -> para puxar o id de cada modal diferente puxado por componetne CardTarefa-->
        <h2 data-bs-toggle="modal" :data-bs-target="'#modalColuna'+idColuna" class="p-1 rounded">

            {{ titulo }}

        </h2>

        <p class="border border-1 rounded p-2 pTarefas" v-for="tarefa in tarefas" :key="tarefa.id" @click="abrirModal(tarefa)">
            {{ tarefa.title }}
        </p>

        <!-- modal das tarefas -->
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
                                <input type="text" class="form-control" id="tituloTarefa"
                                    v-model="dadosTarefaEditar.title">
                            </div>
                        </div>
                        <!--Descricao da tarefa-->
                        <div class="mb-3 row">
                            <label for="tituloTarefa" class="col-sm-2 col-form-label">Descrição:</label>
                            <div class="col-sm-10">
                                <input type="text" class="form-control" id="tituloTarefa"
                                    v-model="dadosTarefaEditar.description">
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal"
                            @click="deletarTarefa()">
                            Deletar
                        </button>
                        <button type="button" class="btn btn-primary" data-bs-dismiss="modal"
                            @click="editarTarefa()">Editar Tarefa</button>
                    </div>
                </div>
            </div>
        </div>

        <!--modal das Colunas-->
        <!-- :id -> para puxar o id de cada modal diferente puxado por componetne CardTarefa-->
         <div class="modal fade" :id="'modalColuna'+idColuna" tabindex="-1" role="dialog" aria-labelledby="modalColunaLabel"
            aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">Excluir Coluna</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close">
                            
                        </button>
                    </div>
                    <div class="modal-body">
                        <p>Deseja excluir a coluna : <b>"{{ titulo }}"</b>?</p>
                        <p class="alert alert-danger">Todas as tarefas presentes dentro da coluna, serão deletadas com ela!</p>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                        <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="deletarColuna()">Deletar Coluna</button>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
//FAZER
//COLOCAR LARGURA IGUAL NOS CARDS, ATUALIZAR OS CARDS AO CLICAR EM ADICIONAR


import axios from "axios"
import { Modal } from "bootstrap";

export default {
    name: "CardTarefa",

    props: {
        titulo: String,
        tarefas: Array,
        idColuna: String
    },

    data() {
        return {
            tarefaModal: {},
            dadosTarefaEditar: {}
        };
    },

    mounted() {
        this.bsModalInstance = new Modal(this.$refs.modalTarefa);
    },

    methods: {
        abrirModal(tarefa) {
            this.tarefaModal = tarefa;
            this.dadosTarefaEditar = { ...tarefa };//copia do obj tarefa
            //para nao mudar o input ao digitar, e quando sair do modal, el manter o valor sem clicar em salvar
            this.$nextTick(() => {
                this.bsModalInstance.show();
            });
        },

        async deletarTarefa() {
            try {
                await axios.delete("http://localhost:3000/tasks/" + this.tarefaModal.id);
                this.$emit('tarefaDeletada');
            } catch (erro) {
                console.log("Erro ao deletar a tarefa:" + this.tarefaModal.id + ", erro:" + erro);
            }
        },

        editarTarefa() {
            try {
                axios.put("http://localhost:3000/tasks/" + this.tarefaModal.id, {
                    id: this.tarefaModal.id,
                    title: this.dadosTarefaEditar.title,
                    description: this.dadosTarefaEditar.description,
                    columnId: this.tarefaModal.columnId
                }).then(() => {
                    //atualizando a coluna, para o valor da tag p mudar
                    this.tarefaModal.title = this.dadosTarefaEditar.title;
                    this.tarefaModal.description = this.dadosTarefaEditar.description;
                })
            } catch (erro) {
                console.log("Erro ao editar a tarefa:" + this.tarefaModal.id + ", erro:" + erro);
            }
        },

        //DELETAR A COLUNA COM AS TAREFAS DENTRO
        async deletarColuna() {
            try {
                await axios.delete("http://localhost:3000/columns/" + this.idColuna);
                this.$emit('colunaDeletada');
                //emit para o main.vue atualizar os dados
            } catch (erro) {
                console.log("Erro ao deletar a coluna, erro:" + erro);
            }
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

.pTarefas, h2 {
    transition: 0.5s;
    cursor: pointer;
}

.pTarefas:hover, h2:hover {
    background: rgb(56, 55, 55);
    color: white;
    border-bottom: 2px solid rgb(56, 55, 55);
}

label {
    overflow-x: hidden;
}
</style>
