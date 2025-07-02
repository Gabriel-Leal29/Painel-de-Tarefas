<template>
    <div>
        <button class="btn btn-primary ms-5" data-bs-toggle="modal" data-bs-target="#exampleModal">+</button>

        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">Modal title</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="row g-3 align-items-center">
                            <div class="col-auto">
                                <label for="titulo" class="col-form-label">Título da Tarefa:</label>
                            </div>
                            <div class="col-auto">
                                <input type="text" id="titulo" class="form-control" v-model="tituloTarefa">
                            </div>
                        </div>
                        <label for="nome" class="form-label">Descrição:</label>
                        <input type="text" class="form-control" id="nome" aria-describedby="emailHelp"
                            v-model="descTarefa">
                        <label for="select">Selecione a coluna:</label>
                        <select name="select" class="form-select" v-model="colunaSelecionada">
                            <option :value="coluna" v-for="(coluna, id) in NomesColunas" :key="id">{{ coluna.title }}
                            </option>
                            <!--:value=coluna -: passo o objeto dentro do array pelo valor para acessar o id-->
                        </select>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Fechar</button>
                        <button type="button" class="btn btn-primary" @click="adicionarTarefa()"
                            data-bs-dismiss="modal">Adicionar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: "btnAdicionar",

    data() {
        return {
            NomesColunas: [],
            tituloTarefa: "",
            descTarefa: "",
            colunaSelecionada: null,
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
                    id: Math.floor(Math.random() * 1000),
                    title: this.tituloTarefa,
                    description: this.descTarefa,
                    columnId: this.colunaSelecionada.id
                }

                await axios.post('http://localhost:3000/tasks', tarefa);

                this.tituloTarefa = null;
                this.descTarefa = null;

                this.$emit('clicou');
            } catch (erro) {
                console.log("Erro ao adicionar tarefa: " + e);
            }
        },
    },

    mounted() {
        this.getNomesColunas();
    },


}
</script>