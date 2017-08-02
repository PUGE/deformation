<template>
  <div id="app">
    <div style="height: 500px; width: 500px; margin: 20px; border: 1px solid red; position: relative;">
      <vue-draggable-resizable :resizable="true" :x="0" :y="0" style="border: 1px solid">
        <input type="text">
        <textarea name="text" cols="30" rows="10"></textarea>
        <input type="radio" name="radio" value="1"> 1
        <input type="radio" name="radio" value="2"> 2
        <input type="radio" name="radio" value="3"> 3
        <input type="checkbox" value="on">
      </vue-draggable-resizable>
    </div>
    <button @click="change">Cambia posizione</button>
  </div>
</template>

<script>
import VueDraggableResizable from './components/vue-draggable-resizable'

export default {
  name: 'app',
  components: {
    'vue-draggable-resizable': VueDraggableResizable
  },
  data () {
    return {

    }
  },
  methods: {
    change () {
      this.x = Math.floor(Math.random() * (200 + 1)) + 0
      this.y = Math.floor(Math.random() * (200 + 1)) + 0
    }
  }
}
</script>

<style>
  .active {
    background-color: #ccc;
    opacity: 0.7;
  }
</style>
