<template>
  <div style="height: 500px; width: 500px; border: 1px solid red; position: relative;">
    <Deformation :w="100" :h="100" v-on:dragging="onDrag" v-on:resizing="onResize" :parent="true">
      <p>Hello! I'm a flexible component. You can drag me around and you can resize me.<br>
      X: {{ x }} / Y: {{ y }} - Width: {{ width }} / Height: {{ height }}</p>
    </Deformation>
  </div>
</template>

<script>
import Deformation from './components/vue-draggable-resizable'

export default {
  data: function () {
    return {
      width: 0,
      height: 0,
      x: 0,
      y: 0
    }
  },
  components: {
    Deformation
  },
  methods: {
    onResize: function (x, y, width, height) {
      this.x = x
      this.y = y
      this.width = width
      this.height = height
    },
    onDrag: function (x, y) {
      this.x = x
      this.y = y
    }
  }
}
</script>
