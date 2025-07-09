<template>
    <div>
        <button class="btn btn-primary ms-5" data-bs-toggle="modal" data-bs-target="#adicionarColunaModal">
            Adicionar Coluna
        </button>

        <div class="modal fade" id="adicionarColunaModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
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
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Fechar</button>
                        <button type="button" class="btn btn-primary" @click="adicionarColuna()"
                            data-bs-dismiss="modal">Adicionar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    name: "btnAdicionar",

    data() {
        return {
            colunas: [],
            novoNomeColuna: "",
            existente: false,
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
                        return
                    }
                });

                let contador= 0;

                if(this.colunas.length > 0){
                    contador = parseInt(this.colunas[this.colunas.length-1].id) + 1;
                }else{
                    contador++
                }

                

                if (!this.existente) {
                    let novaColuna = {
                        id: contador.toString(),//transforma o id em string
                        title: this.novoNomeColuna,
                    };

                    await axios.post("http://localhost:3000/columns", novaColuna);
                    this.$emit("adicionadaNovaColuna");
                    this.novoNomeColuna="";
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