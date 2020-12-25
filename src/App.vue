<template>
  <div id="app">
    <div class="loader" v-if="loader"><span>Загрузка...</span></div>
    <router-view/>
  </div>
</template>

<script lang="js">
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class App extends Vue {
  get loader() {
    return this.$store.state.loader;
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
* {
  padding: 0;
  margin: 0;
}
.loader {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  width: 100%;
  min-height: 100%;
  background-color: rgba(202, 202, 202, 0.9);
  z-index: 1000;
}
</style>
