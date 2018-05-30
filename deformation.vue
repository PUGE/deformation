<template>
  <div class="vdr" id="vdr" :class="{ draggable, resizable, active , dragging, resizing}" @mousedown.stop="elmDown" tabindex="0" @keydown.stop="keydown($event)" :style="style">
    <!-- 如果可改变大小为真 -->
    <template v-if="resizable && !disable">
      <!-- 待优化 -->
      <div class="handle handle-ml" @mousedown.stop.prevent="handleDown('ml')"></div>
      <div class="handle handle-mr" @mousedown.stop.prevent="handleDown('mr')"></div>
      <div class="handle handle-tm" @mousedown.stop.prevent="handleDown('tm')"></div>
      <div class="handle handle-bm" @mousedown.stop.prevent="handleDown('bm')"></div>
      <div class="handle handle-br" @mousedown.stop.prevent="handleDown('br')">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 17 17"><defs></defs><g id="图层_2" data-name="图层 2"><g id="图层_2-2" data-name="图层 2"><rect class="cls-1" x="2.63" y="-0.2" width="1" height="6.65" transform="translate(-1.3 3.13) rotate(-45)"/><path class="cls-1" d="M.5,5.78a.5.5,0,0,1-.5-.5V0H5.28a.5.5,0,0,1,.5.5.5.5,0,0,1-.5.5H1V5.28A.5.5,0,0,1,.5,5.78Z"/><path class="cls-1" d="M16.3,5.78a.5.5,0,0,1-.5-.5V1H11.52a.5.5,0,0,1,0-1H16.8V5.28A.5.5,0,0,1,16.3,5.78Z"/><path class="cls-1" d="M5.28,16.8H0V11.52a.5.5,0,0,1,1,0V15.8H5.28a.5.5,0,0,1,.5.5A.5.5,0,0,1,5.28,16.8Z"/><path class="cls-1" d="M16.22,16.72a.52.52,0,0,1-.35-.14l-4.7-4.7a.5.5,0,0,1,.71-.71l4.7,4.7a.51.51,0,0,1,0,.71A.54.54,0,0,1,16.22,16.72Z"/><path class="cls-1" d="M17,17H11.72a.5.5,0,1,1,0-1H16V11.72a.5.5,0,1,1,1,0Z"/></g></g></svg>
      </div>
    </template>
    <slot></slot>
  </div>
</template>

<script>
  export default {
    replace: true,
    name: 'deformation',
    data () {
      return {
        // 是否支持passive
        passiveSupported: false,
        parentX: 0,
        parentW: 9999,
        parentY: 0,
        parentH: 9999,
        mouseX: 0,
        mouseY: 0,
        lastMouseX: 0,
        lastMouseY: 0,
        mouseOffX: 0,
        mouseOffY: 0,
        elmX: 0,
        elmY: 0,
        elmW: 0,
        elmH: 0
      }
    },
    props: {
      draggable: { // 是否可被拖动
        type: Boolean, default: true
      },
      resizable: { // 是否可改变大小
        type: Boolean, default: true
      },
      w: { // 宽度
        type: Number,
        default: 200,
        validator: (val) => {
          return (typeof val === 'number' && val > 0)
        }
      },
      h: { // 高度
        type: Number,
        default: 200,
        validator: (val) => {
          return (typeof val === 'number' && val > 0)
        }
      },
      minw: { // 最小宽度
        type: Number,
        default: 50,
        validator: function (val) {
          return val > 0
        }
      },
      minh: { // 最小高度
        type: Number,
        default: 50,
        validator: function (val) {
          return val > 0
        }
      },
      x: { // 距父元素左上角X轴偏移量
        type: Number,
        default: 0
      },
      y: { // 距父元素左上角Y轴偏移量
        type: Number,
        default: 0
      },
      zoom: {
        type: Number,
        default: 1
      },
      axis: { // 定义组件可以拖动的轴线
        type: String,
        default: 'both',
        validator: function (val) {
          return ['x', 'y', 'both'].indexOf(val) !== -1
        }
      },
      grid: {
        type: Array,
        default: () => {
          return [1, 1]
        }
      },
      restrain: { // 约束组件大小
        type: Number,
        default: 0
      },
      parent: {
        type: Boolean, default: false
      },
      disable: {
        type: Boolean, default: false
      }
    },
    mounted () {
      // 初始化控件宽高
      if (this.minw > this.w) this.width = this.minw
      if (this.minh > this.h) this.height = this.minh
      // 判断是否只能在父级元素中拖动
      if (this.parent) {
        this.parentW = parseInt(this.$el.parentNode.clientWidth, 10)
        this.parentH = parseInt(this.$el.parentNode.clientHeight, 10)

        if (this.w > this.parentW) this.width = this.parentW
        if (this.h > this.parentH) this.height = this.parentH
        if ((this.x + this.width) > this.parentW) this.left = parentW - this.width
        if ((this.y + this.height) > this.parentH) this.top = parentH - this.height
      }
      // 判断浏览器是否支持passive
      try {
        Object.defineProperty({}, "passive", {
          get: function() {
            this.passiveSupported = true;
          }
        });
      } catch(err) {}
      this.$emit('resizing', this.left, this.top, this.width, this.height)
    },
    data () {
      return {
        top: this.y,
        left: this.x,
        width: this.w,
        height: this.h,
        resizing: false,
        dragging: false,
        active: false,
        handle: null
      }
    },
    methods: {
      elmDown (e) { // 组件被按下事件
        // 阻止默认事件
        e.preventDefault()
        const passiveSupported = this.passiveSupported
        // 判断是否支持键盘微调
        if (this.disable || !this.resizable) return
        const target = e.target || e.srcElement
        // 确保事件发生在组件内部
        if (this.$el.contains(target)) {
          if (!this.active) {
            this.lastMouseX = e.pageX || e.clientX + document.documentElement.scrollLeft
            this.lastMouseY = e.pageY || e.clientY + document.documentElement.scrollTop
            document.documentElement.addEventListener('mousemove', this.handleMove, passiveSupported ? { passive: true } :true)
            document.documentElement.addEventListener('mousedown', this.deselect, passiveSupported ? { passive: true } :true)
            document.documentElement.addEventListener('mouseup', this.handleUp, passiveSupported ? { passive: true } :true)
            this.active = true
            this.$emit('activated')
          }
          this.elmX = parseInt(this.$el.style.left)
          this.elmY = parseInt(this.$el.style.top)
          this.elmW = this.$el.offsetWidth || this.$el.clientWidth
          this.elmH = this.$el.offsetHeight || this.$el.clientHeight
          if (this.draggable) {
            this.dragging = true
          }
        }
      },
      deselect (e) { // 取消选择事件
        this.mouseX = e.pageX || e.clientX + document.documentElement.scrollLeft
        this.mouseY = e.pageY || e.clientY + document.documentElement.scrollTop
        this.lastMouseX = this.mouseX
        this.lastMouseY = this.mouseY
        
        const target = e.target || e.srcElement
        const regex = new RegExp('handle-([trmbl]{2})', '')
        if (!this.$el.contains(target) && !regex.test(target.className)) {
          if (this.active) {
            document.documentElement.removeEventListener('mousemove', this.handleMove, true)
            document.documentElement.removeEventListener('mousedown', this.deselect, true)
            document.documentElement.removeEventListener('mouseup', this.handleUp, true)
            this.active = false
            this.$emit('deactivated')
          }
        }
      },
      handleDown (handle) { // 拖动点按下事件
        // 将handle设置为当前元素
        this.handle = handle
        this.resizing = true
      },
      handleMove (e) {
        this.mouseX = e.pageX || e.clientX + document.documentElement.scrollLeft
        this.mouseY = e.pageY || e.clientY + document.documentElement.scrollTop
        // diffX =  当前鼠标位置 - 上次鼠标位置 + ？？
        let diffX = (this.mouseX - this.lastMouseX + this.mouseOffX) / this.zoom
        let diffY = (this.mouseY - this.lastMouseY + this.mouseOffY) / this.zoom
        this.mouseOffX = this.mouseOffY = 0
        this.lastMouseX = this.mouseX
        this.lastMouseY = this.mouseY
        let dX = diffX
        let dY = diffY
        if (this.resizing) {
          if (this.handle.indexOf('t') >= 0) {
            if (this.elmH - dY < this.minh) this.mouseOffY = (dY - (diffY = this.elmH - this.minh))
            else if (this.elmY + dY < this.parentY) this.mouseOffY = (dY - (diffY = this.parentY - this.elmY))
            this.elmY += diffY
            this.elmH -= diffY
          }
          if (this.handle.indexOf('b') >= 0) {
            if (this.elmH + dY < this.minh) this.mouseOffY = (dY - (diffY = this.minh - this.elmH))
            else if (this.elmY + this.elmH + dY > this.parentH) this.mouseOffY = (dY - (diffY = this.parentH - this.elmY - this.elmH))
            this.elmH += diffY
          }
          if (this.handle.indexOf('l') >= 0) {
            if (this.elmW - dX < this.minw) this.mouseOffX = (dX - (diffX = this.elmW - this.minw))
            else if (this.elmX + dX < this.parentX) this.mouseOffX = (dX - (diffX = this.parentX - this.elmX))
            this.elmX += diffX
            this.elmW -= diffX
          }
          if (this.handle.indexOf('r') >= 0) {
            if (this.elmW + dX < this.minw) this.mouseOffX = (dX - (diffX = this.minw - this.elmW))
            else if (this.elmX + this.elmW + dX > this.parentW) this.mouseOffX = (dX - (diffX = this.parentW - this.elmX - this.elmW))
            this.elmW += diffX
          }
          this.left = (Math.round(this.elmX / this.grid[0]) * this.grid[0])
          this.top = (Math.round(this.elmY / this.grid[1]) * this.grid[1])
          this.width = (Math.round(this.elmW / this.grid[0]) * this.grid[0])
          this.height = (Math.round(this.elmH / this.grid[1]) * this.grid[1])
          this.$emit('resizing', this.left, this.top, this.width, this.height)
        } else if (this.dragging) {
          if (this.parent) {
            if (this.elmX + dX < this.parentX) this.mouseOffX = (dX - (diffX = this.parentX - this.elmX))
            else if (this.elmX + this.elmW + dX > this.parentW) this.mouseOffX = (dX - (diffX = this.parentW - this.elmX - this.elmW))
            if (this.elmY + dY < this.parentY) this.mouseOffY = (dY - (diffY = this.parentY - this.elmY))
            else if (this.elmY + this.elmH + dY > this.parentH) this.mouseOffY = (dY - (diffY = this.parentH - this.elmY - this.elmH))
          }
          this.elmX += diffX
          this.elmY += diffY
          if (this.axis === 'x' || this.axis === 'both') {
            this.left = (Math.round(this.elmX / this.grid[0]) * this.grid[0])
          }
          if (this.axis === 'y' || this.axis === 'both') {
            this.top = (Math.round(this.elmY / this.grid[1]) * this.grid[1])
          }
          this.$emit('dragging', this.left, this.top)
        }
      },
      getRestrain (num) {
        const restrain = this.restrain
        return (num / restrain).toFixed(0) * restrain
      },
      handleUp (e) {
        this.handle = null
        // 约束
        const restrain = this.restrain
        if (restrain && restrain > 0) {
          this.left = this.getRestrain(this.left)
          this.top = this.getRestrain(this.top)
          this.width = this.getRestrain(this.width)
          this.height = this.getRestrain(this.height)
        }
        if (this.resizing) {
          this.resizing = false
          this.$emit('resizestop', this.left, this.top, this.width, this.height)
        }
        if (this.dragging) {
          this.dragging = false
          // 如果位置没有改变 不向外发送位置改变事件
          if (this.left !== this.elmX || this.top !== this.elmY) {
            this.$emit('dragstop', this.left, this.top)
          }
        }
        this.elmX = this.left
        this.elmY = this.top
      },
      keydown (event) {
        // 判断是否支持键盘微调
        if (this.disable || !this.resizable) return
        // 微调位置
        switch (event.key) {
          case 'ArrowUp': this.top--; this.$emit('dragstop', this.left, this.top); break
          case 'ArrowLeft': this.left--; this.$emit('dragstop', this.left, this.top);  break
          case 'ArrowDown': this.top++; this.$emit('dragstop', this.left, this.top);  break
          case 'ArrowRight': this.left++; this.$emit('dragstop', this.left, this.top);  break
          default: this.$emit('keydown', event);
        }
      }
    },
    watch: {
      x (newVal) {
        this.left = newVal
      },
      y (newVal) {
        this.top = newVal
      },
      w (newVal) {
        this.width = newVal
      },
      h (newVal) {
        this.height = newVal
      }
    },
    computed: {
      style () {
        const w = this.width === 0? 'auto': this.width + 'px'
        const h = this.height === 0? 'auto': this.height + 'px'
        return {
          top: this.top + 'px',
          left: this.left + 'px',
          width: w,
          height: h
        }
      }
    }
  }
</script>

<style scoped>
  .vdr {
    padding: 0;
    position: absolute;
    user-select: none;
    border-color: #45DBF7;
  }
  .draggable:hover {
    cursor: move;
  }
  .handle {
    display: none;
    position: absolute;
    z-index: 999;
  }
  svg {
    fill: #45DBF7;
  }
  .handle-br {
    bottom: 0;
    right: 0;
    cursor: se-resize;
    width: 25px;
    height: 25px;
    padding: 5px;
  }
 .handle-ml {
    top: 0;
    left: -5px;
    cursor: w-resize;
    position: absolute;
    height: 100%;
    width: 5px;
  }
  .handle-ml:hover {
    background: linear-gradient(to right, rgba(255, 255, 255, 0), #3F51B5);
  }
  .handle-mr {
    top: 0;
    right: -5px;
    cursor: w-resize;
    position: absolute;
    height: 100%;
    width: 5px;
  }
  .handle-mr:hover {
    background: linear-gradient(to right, #3F51B5, rgba(255, 255, 255, 0));
  }
  .handle-tm {
    position: absolute;
    width: 100%;
    height: 5px;
    top: -5px;
    left: 0;
    cursor: n-resize;
  }
  .handle-tm:hover {
    background: linear-gradient(rgba(255, 255, 255, 0), #3F51B5);
  }
  .handle-bm {
    position: absolute;
    width: 100%;
    height: 5px;
    bottom: -5px;
    left: 0;
    cursor: s-resize;
  }
  .handle-bm:hover {
    background: linear-gradient(#3F51B5, rgba(255, 255, 255, 0));
  }
  .active .handle {
    display: block;
  }
  .active {
    padding: 0;
    background-color: rgba(150, 150, 150, 0.3);
  }
  .icon {
    text-align: center;
    line-height: 25px;
    color: white;
    font-size: 22px;
    overflow: hidden;
  }
</style>
