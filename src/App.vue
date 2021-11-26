<template>
    <div id="app">
        <graph-component />
    </div>
</template>
<script>
import GraphComponent from '@/components/GraphComponent.vue'
export default {
    components: {
        GraphComponent
    }
}
</script>
<style lang="scss">
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
</style>
