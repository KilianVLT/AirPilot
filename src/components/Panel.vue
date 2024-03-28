<template>
    <div id="panel" v-if="details.length > 0">
        <p>{{ "En provenance de " + details[2]}}</p>
    </div>
</template>

<script>

    export default {
        name: "Panel",
        props: {
            details: []
        },
    }
</script>

<style scoped>
    #panel{
        width: 15vw;
    }
</style>
