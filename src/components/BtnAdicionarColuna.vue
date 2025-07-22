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
import axios from "axios";
import * as bootstrap from 'bootstrap';
import Mensagem from "./Mensagem.vue";


export default {
    name: "btnAdicionar",

    components: {
        Mensagem,
    },

    data() {
        return {
            colunas: [],
            novoNomeColuna: "",
            texto: "",
            colunaExistente:false
        };
    },

    methods: {
        async getNomesColunas() {
            try {
                const resp = await axios.get("http://localhost:3000/columns");
                this.colunas = resp.data;
            } catch (erro) {
                this.texto = "Erro ao carregar os dados: " + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
            };
        },

        async adicionarColuna() {
            try {
                await this.getNomesColunas();

                this.colunas.forEach((coluna) => {
                    if (coluna.title.toUpperCase() == this.novoNomeColuna.toUpperCase()) {
                        this.colunaExistente = true;
                        this.novoNomeColuna = null;
                        this.texto = "Nome de coluna ja existente!";
                        this.$nextTick(() => {
                            let modal = new bootstrap.Modal(this.$refs.modalAviso);
                            modal.show();
                        });
                        return
                    }
                });

                let contador = 0;

                if (this.colunas.length > 0) {
                    contador = parseInt(this.colunas[this.colunas.length - 1].id) + 1;
                } else {
                    contador++
                }

                if (!this.colunaExistente) {
                    let novaColuna = {
                        id: contador.toString(),//transforma o id em string
                        title: this.novoNomeColuna,
                    };

                    await axios.post("http://localhost:3000/columns", novaColuna);
                    this.$emit("adicionadaNovaColuna");
                    this.novoNomeColuna = "";
                }
            } catch (erro) {
                this.texto = "Erro adicionar nova coluna: " + erro;
                this.$nextTick(() => {
                    let modal = new bootstrap.Modal(this.$refs.modalAviso);
                    modal.show();
                });
            }
        },
    },

    mounted() {
        this.getNomesColunas();
    },
};
</script>