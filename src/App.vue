<template>
  <h1>节点编辑器</h1>
  <div id="app">
      <ControlPanel class="subFrame"/>
  </div>
</template>

<script>
import ControlPanel from "./components/ControlPanel.vue"
export default {
  name: 'App',
  components: {
    ControlPanel
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}
#h3{
  height: 5px;
}

</style>
