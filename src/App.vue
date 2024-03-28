<script>
  import Map from './components/Map.vue';
  import Panel from './components/Panel.vue';

  export default{
    name: "App",
    components: {
      Map,
      Panel
    },
    data(){
      return {
        details: []
      }
    },
    methods: {
      infos(event){
        this.details = event
        console.log(this.details);
      }
    }
  }
</script>

<template>
    <Panel :details="details"></Panel>
    <Map @flight="infos"/>
</template>

<style scoped>
</style>
