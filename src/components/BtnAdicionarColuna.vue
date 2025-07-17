<template>
    <div>
        <button class="configButton" data-bs-toggle="modal" data-bs-target="#adicionarColunaModal">
            Adicionar Coluna
        </button>

        <div class="modal fade" id="adicionarColunaModal" tabindex="-1" aria-labelledby="exampleModalLabel"
            aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">Adicionar Coluna</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="row g-3 align-items-center">
                            <div class="col-auto">
                                <label for="titulo" class="col-form-label">Título da Coluna:</label>
                            </div>
                            <div class="col-auto">
                                <input type="text" id="titulo" class="form-control" v-model="novoNomeColuna">
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Cancelar</button>
                        <button type="button" class="configButton" @click="adicionarColuna()"
                            data-bs-dismiss="modal">Adicionar</button>
                    </div>
                </div>
            </div>
        </div>

        <div v-show="existente" ref="modalAviso" class="modal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">Aviso</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="alert alert-danger d-flex align-items-center" role="alert">
                            <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Danger:">
                                <use xlink:href="#exclamation-triangle-fill" />
                            </svg>
                            <div>
                                Já existe um card com este mesmo nome!
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="configButton" data-bs-dismiss="modal">Fechar</button>
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
    </div>
</template>

<style scoped>
.modal-body, .modal-header, .modal-footer{
  background: #1e1e2f;
}
</style>

<script>
import axios from "axios";
import * as bootstrap from 'bootstrap';


export default {
    name: "btnAdicionar",

    data() {
        return {
            colunas: [],
            novoNomeColuna: "",
            existente: true,
        };
    },

    methods: {
        async getNomesColunas() {
            try {
                const resp = await axios.get("http://localhost:3000/columns");
                this.colunas = resp.data;
            } catch (e) {
                console.log("Erro ao buscas as colunas: " + e);
            }
        },

        async adicionarColuna() {
            try {
                this.existente = false;
                await this.getNomesColunas();

                this.colunas.forEach((coluna) => {
                    if (coluna.title == this.novoNomeColuna) {
                        this.existente = true;
                        this.novoNomeColuna="";
                        this.$nextTick(()=> {
                            let modal = new bootstrap.Modal(this.$refs.modalAviso);
                            modal.show();
                        })
                        console.log("chegou aq")
                        return
                    }
                });

                let contador = 0;

                if (this.colunas.length > 0) {
                    contador = parseInt(this.colunas[this.colunas.length - 1].id) + 1;
                } else {
                    contador++
                }



                if (!this.existente) {
                    let novaColuna = {
                        id: contador.toString(),//transforma o id em string
                        title: this.novoNomeColuna,
                    };

                    await axios.post("http://localhost:3000/columns", novaColuna);
                    this.$emit("adicionadaNovaColuna");
                    this.novoNomeColuna = "";
                }
            } catch (erro) {
                console.log("Erro ao adicionar coluna: " + erro);
            }
        },
    },

    mounted() {
        this.getNomesColunas();
    },
};
</script>