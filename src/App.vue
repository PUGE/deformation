<template>
  <div id="app" style="height: 100%; width: 100%; position: relative; margin: 10px;">
    <div style="height: 500px; width: 500px; margin: 20px; border: 1px solid red;">
      <template v-for="element in elements">
        <vue-draggable-resizable :resizable="true" :x="element.x" :y="element.y">
          <p><b>{{ element.id }}</b> - {{ element.text }}</p>
        </vue-draggable-resizable>
      </template>
    </div>
    <button @click.stop="change">Cambia posizione</button>
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
      elements: [
        {
          id: 'uno',
          text: 'I can be moved around freely within the parent',
          x: 0,
          y: 0
        },
        {
          id: 'due',
          text: 'My initial position is set to 0,0 but I appear below the previous element at 0, 200 and can be moved further down than the border of my parent',
          x: 100,
          y: 0
        },
        {
          id: 'tre',
          text: 'I appear below the lower end of my predecessor. If the size of one of them is changed, I move accordingly without changing my coordinates',
          x: 0,
          y: 100
        }
      ]
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
  .active:after {
    content:"";
    display: block;
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    box-sizing: border-box;
    border: 1px dashed;
  }
</style>
