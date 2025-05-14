<template>
    <div class="principal d-flex w-auto">
        <h2>{{ titulo }}</h2>
    </div>
</template>

<script>
export default{
    name:"Coluna",

    props:{
        titulo:String,
    }
}
</script>

<style scoped>
.principal{
    background: white;
}
</style>