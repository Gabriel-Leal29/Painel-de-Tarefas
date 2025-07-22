<template>
    <div>
        <button @click="getNomesColunas()" class="configButton" data-bs-toggle="modal" data-bs-target="#exampleModal">
            Adicionar Tarefa</button>

        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">Adicionar Tarefa</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="row g-3 align-items-center">
                            <div class="col-auto">
                                <label for="titulo" class="col-form-label">Título da Tarefa:</label>
                            </div>
                            <div class="col">
                                <input type="text" id="titulo" class="form-control" v-model="tituloTarefa">
                            </div>
                        </div>

                        <div class="row mt-2">
                            <div class="col-auto">
                                <label for="nome" class="col-form-label">Descrição:</label>
                            </div>
                            <div class="col">
                                <input type="text" class="form-control" id="nome" aria-describedby="emailHelp"
                                    v-model="descTarefa">
                            </div>
                        </div>


                        <label for="select" class="">Selecione a coluna:</label>
                        <select name="select" class="form-select" v-model="colunaSelecionada">
                            <option :value="coluna" v-for="(coluna, id) in NomesColunas" :key="id">{{ coluna.title }}
                            </option>
                            <!--:value=coluna -: passo o objeto dentro do array pelo valor para acessar o id-->
                        </select>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Cancelar</button>
                        <button type="button" class="configButton" @click="adicionarTarefa()"
                            data-bs-dismiss="modal">Adicionar</button>
                    </div>
                </div>
            </div>
        </div>

        
        <div class="modal fade" ref="modalAviso" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5">Aviso</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <Mensagem :texto="texto" />
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Fechar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.modal-body,
.modal-header,
.modal-footer {
    background: #1e1e2f;
}
</style>

<script>
import axios from 'axios'
import * as bootstrap from 'bootstrap';
import Mensagem from './Mensagem.vue'

export default {
    name: "btnAdicionar",

    components:{
        Mensagem
    },

    data() {
        return {
            NomesColunas: [],
            tituloTarefa: "",
            descTarefa: "",
            colunaSelecionada: null,
            texto:null,
        }
    },

    methods: {
        async getNomesColunas() {
            try {
                const resp = await axios.get("http://localhost:3000/columns");
                this.NomesColunas = resp.data;

                //deixando a 1° opc selecionada
                this.colunaSelecionada = this.NomesColunas.length > 0 ? this.NomesColunas[0] : null;
            } catch (e) {
                console.log("Erro ao buscas as colunas: " + e);
            }
        },

        async adicionarTarefa() {
            try {
                const tarefa = {
                    id: String(Math.floor(Math.random() * 10000)),//id como string
                    title: this.tituloTarefa,
                    description: this.descTarefa,
                    columnId: String(this.colunaSelecionada.id) //columnId como string
                };

                await axios.post('http://localhost:3000/tasks', tarefa);

                this.tituloTarefa = null;
                this.descTarefa = null;
                this.$emit('clicou');
            } catch (erro) {
                this.texto = "Erro ao adicionar tarefa: " + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
            }
        }

        ,
    },


}
</script>