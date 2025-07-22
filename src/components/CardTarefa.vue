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
                        <Mensagem :texto="texto" />
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Cancelar</button>
                        <button type="button" class="configButton" data-bs-dismiss="modal"
                            @click="deletarColuna()">Deletar Coluna</button>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import axios from "axios"
import { Modal } from "bootstrap";
import Mensagem from "./Mensagem.vue";

export default {
    name: "CardTarefa",

    components: {
        Mensagem,
    },

    props: {
        titulo: String,
        tarefas: Array,
        idColuna: String
    },

    data() {
        return {
            tarefaModal: {},
            dadosTarefaEditar: {},
            texto: "Todas as tarefas atribuídas a coluna, serão deletadas!",
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
                this.texto = "Erro ao editar a tarefa:" + this.tarefaModal.id + ", erro:" + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
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
                this.texto = "Erro ao deletar a coluna: " + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
            }
        },


        //Drag-and-Drop

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
                this.texto = "Erro ao mover a tarefa, erro:" + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
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
