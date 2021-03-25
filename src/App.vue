<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <h1>Vue Select</h1>
  <p>Vue 3 + TypeScript + Vite</p>
  <div id="container">
    <Select
      v-bind:label="selectLabel1"
      v-bind:options="selectOptions1"
    />
    <p></p>
    <Select
      v-bind:label="selectLabel2"
      v-bind:options="selectOptions2"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Select from './components/Select.vue'

export default defineComponent({
  name: 'App',
  components: {
    Select
  },
  data() {
    return {
      selectLabel1: 'select1',
      selectOptions1: [
        {text: 'test a', value: 1, selected: true},
        {text: 'test b', value: 2},
      ],
      selectLabel2: 'select2',
      selectOptions2: [
        {text: 'test c', value: 3},
        {text: 'test d', value: 4, selected: true},
        {text: 'test e', value: 5},
        {text: 'test f', value: 6},
        {text: 'test g', value: 7},
      ],
    }
  }
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#container {
  width: 400px;
  margin: 0 auto;
}
</style>
