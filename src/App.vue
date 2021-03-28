<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <h1>Vue Select</h1>
  <p>Vue 3 + TypeScript + Vite</p>
  <div id="container">
    <Select
      v-bind:label="selectLabel1"
      v-bind:options="selectOptions1"
      @change="onSelect"
    />
    <Select
      v-bind:label="selectLabel2"
      v-bind:options="selectOptions2"
      @change="onSelect"
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
      selectLabel1: 'selection 1',
      selectOptions1: [
        {text: 'option a', value: 1, selected: true},
        {text: 'option b', value: 2},
        {text: 'option c', value: 3},
      ],
      selectLabel2: 'selection 2',
      selectOptions2: [
        {text: 'option c', value: 3},
        {text: 'option d', value: 4, selected: true},
        {text: 'option e', value: 5},
        {text: 'option f', value: 6},
        {text: 'option g', value: 7},
      ],
    }
  },
  methods: {
    onSelect(value: any) {
      console.log('on select');
      console.log('seleted:', value);
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
.select {
  margin: 1em 0;
}
</style>
