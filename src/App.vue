<template>
  <div id="app">
    <div style="height: 500px; width: 500px; margin: 20px; border: 1px solid red; position: relative;">
      <deformation :x="50" :y="50" :w="400" :h="400" :parent="true" @resizing="onResize" @dragging="onDrag">
        <p>Component</p>
      </deformation>
    </div>
  </div>
</template>

<script>
import Deformation from './components/vue-draggable-resizable'

export default {
  name: 'app',
  components: {
    'deformation': Deformation
  }
}
</script>

<style>
  .active {
    border: 1px dashed black;
  }
</style>
