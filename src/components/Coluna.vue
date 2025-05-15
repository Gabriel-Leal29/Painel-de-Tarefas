<template>
    <div class="principal w-auto col-auto">
        <h2>{{ titulo }}</h2>
        <p v-for="tarefa in tarefas" :key="tarefa.id">
            {{ tarefa.description }}
        </p>
    </div>
</template>

<script>
export default{
    name:"Coluna",

    props:{
        titulo:String,
        tarefas:Array,
    },

    data(){
        return{
        }
    }
}
</script>

<style scoped>
.principal{
    background: white;
}
</style>