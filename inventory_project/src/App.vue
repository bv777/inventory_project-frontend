<template>
  <div id="app">
    <CurrentInventoryTable/>
  </div>
</template>

<script>
import CurrentInventoryTable from './components/CurrentInventoryTable.vue'
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
// import './custom.scss'

import Vue from 'vue'
Vue.use(BootstrapVue)
Vue.use(IconsPlugin)

export default {
  name: 'App',
  components: {
    CurrentInventoryTable,
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
  margin-top: 60px;
  font-size: large;
}
</style>
