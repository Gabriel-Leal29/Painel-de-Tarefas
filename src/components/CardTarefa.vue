<template>
    <section class="principal text-center rounded">
        <!-- :data-bs-target -> para puxar o id de cada modal diferente puxado por componetne CardTarefa-->
        <h2 class="p-1 rounded m-2" data-bs-toggle="modal" :data-bs-target="'#modalColuna' + idColuna">

            {{ titulo }}

        </h2>

        <div @dragover.prevent @drop="onDrop" class="h-100">
            <p class="border border-1 rounded p-2 pTarefas mt-1 mb-3 shadow rounded" v-for="tarefa in tarefas"
                :key="tarefa.id" @click="abrirModal(tarefa)" draggable="true" @dragstart="onDragStart(tarefa, $event)">
                {{ tarefa.title }}
            </p>
        </div>



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
                        <button type="button" class="configButton" data-bs-dismiss="modal"
                            @click="deletarTarefa(this.dadosTarefaEditar.id)">
                            Deletar
                        </button>
                        <button type="button" class="configButton" data-bs-dismiss="modal"
                            @click="editarTarefa()">Editar Tarefa</button>
                    </div>
                </div>
            </div>
        </div>

        <!--modal das Colunas-->
        <!-- :id -> para puxar o id de cada modal diferente puxado por componetne CardTarefa-->
        <div class="modal fade" :id="'modalColuna' + idColuna" tabindex="-1" role="dialog"
            aria-labelledby="modalColunaLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">Excluir Coluna</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close">

                        </button>
                    </div>
                    <div class="modal-body">
                        <p>Deseja excluir a coluna : <b>"{{ titulo }}"</b>?</p>
                        <div class="alert alert-danger d-flex align-items-center" role="alert">
                            <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Danger:">
                                <use xlink:href="#exclamation-triangle-fill" />
                            </svg>
                            <div>
                                Todas as tarefas presentes em "{{ titulo }}", serão deletadas automaticamente!
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Cancelar</button>
                        <button type="button" class="configButton" data-bs-dismiss="modal"
                            @click="deletarColuna()">Deletar Coluna</button>
                    </div>
                </div>
            </div>
        </div>

        <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
            <symbol id="exclamation-triangle-fill" fill="currentColor" viewBox="0 0 16 16">
                <path
                    d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767L8.982 1.566zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5zm.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2z" />
            </symbol>
        </svg>
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
        idColuna: String
    },

    data() {
        return {
            tarefaModal: {},
            dadosTarefaEditar: {},
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

        async editarTarefa() {
            try {
                await axios.put(`http://localhost:3000/tasks/${this.tarefaModal.id}`, {
                    id: this.tarefaModal.id,
                    title: this.dadosTarefaEditar.title,
                    description: this.dadosTarefaEditar.description,
                    columnId: this.tarefaModal.columnId
                });

                //atualizando a coluna, para o valor da tag p mudar
                this.tarefaModal.title = this.dadosTarefaEditar.title;
                this.tarefaModal.description = this.dadosTarefaEditar.description;
            } catch (erro) {
                console.log("Erro ao editar a tarefa:" + this.tarefaModal.id + ", erro:" + erro);
            }
        },

        async deletarTarefa(id) {
            try {
                const idStr = String(id);    //garante que id é string
                await axios.delete(`http://localhost:3000/tasks/${idStr}`);
                this.$emit('tarefaDeletada');
            } catch (erro) {
                console.log("Erro ao deletar a tarefa:", erro);
            }
        },
        async deletarColuna() {
            try {
                for (const tarefa of this.tarefas) {
                    await this.deletarTarefa(tarefa.id);
                }
                await axios.delete(`http://localhost:3000/columns/${this.idColuna}`);
                this.$emit('colunaDeletada')
            } catch (erro) {
                console.log("Erro ao deletar a coluna!")
            }
        },

        onDragStart(tarefa, event) {
            //envia o id da tarefa
            event.dataTransfer.setData('text/plain', tarefa.id);
        },

        async onDrop(event) {
            //solta a tarfa do id
            const tarefaId = event.dataTransfer.getData('text/plain');

            try {
                await axios.patch(`http://localhost:3000/tasks/${tarefaId}`, {
                    columnId: this.idColuna
                });

                this.$emit('tarefaMovida');
            } catch (erro) {
                console.log("Erro ao mover a coluna: ", erro)
            }
        },

        onDragStartColuna(event) {
            //envia o id da coluna
            event.dataTransfer.setData('text/plain', this.idColuna);
            event.dataTransfer.effectAllowed = 'move';
        },

    },
};
</script>

<style scoped>
.principal {
    border: 2px solid #3f3f4f;
    min-width: 16rem;
    color: #f1f1f1;
}

h2 {
    border-bottom: 2px solid #d1c4e9;
}

.pTarefas,
h2 {
    transition: 0.5s;
    cursor: pointer;
}

.pTarefas:hover,
h2:hover {
    background: #5c6bc0;
    border-bottom: 2px solid #f3e8ff;
}

label {
    overflow-x: hidden;
}

.modal-body,
.modal-header,
.modal-footer {
    background: #1e1e2f;
}
</style>
