<template>
    <main class="d-flex justify-content-center">
        <section class="painel d-flex p-4 mt-4 rounded">
                <Coluna/>

                <Coluna/>

                <Coluna/>
        </section>
    </main>
</template>

<style>
main{
    background: #D3D3D3;
    height: 100vh;
}

.painel{
    background: white;
    gap:40px;
    width:80%;
    height:80%;
}


</style>

<script>
import Coluna from "./Coluna.vue"

export default{
    name: "Main",

    components:{
        Coluna,
    }
}

</script>