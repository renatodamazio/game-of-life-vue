<template>
  <div id="app">
    <Life></Life>
  </div>
</template>

<script>
import Vue from 'vue'
import Life from './components/Life.vue'

import './assets/styles.css'

export default {
  name: 'app',
  components: {
    Life
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
