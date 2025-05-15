<template>
    <section class="principal text-center rounded col-auto">
        <h2>{{ titulo }}</h2>

        <p class="border border-1 rounded p-2" v-for="tarefa in tarefas" :key="tarefa.id" @click="abrirModal(tarefa)"
            style="cursor:pointer">
            {{ tarefa.title }}
        </p>

        <div class="modal fade" id="ModalTarefa" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true"
            ref="modalTarefa">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">
                            Editar Tarefa: {{ tarefaModal.title }}
                        </h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <input type="text" readonly class="form-control" id="nomeTarefa" :value="tarefaModal.title" />
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
            bsModalInstance: null,
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
</style>
