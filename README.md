<h1 align="center">deformation</h1>


> 自由拖动缩放组件.

### 特点

* 没有依赖包
* 可以拖动 位置 和 大小
* 可以设定大小调整范围
* 可以限制元素只能在父组件内拖动
* 可以自定义网格
* 可以限制元素只能水平 或 竖直拖动
* 方向键可以微调坐标

### 实例

---

## 安装 及 使用

```bash
$ npm install --save deformation
```


注册组件

```js
import Vue from 'vue'
import deformation from 'deformation'

Vue.component('Deformation', deformation)
```

使用组件

```vue
<template>
  <div style="height: 500px; width: 500px; border: 1px solid red; position: relative;">
    <Deformation :w="100" :h="100" v-on:dragging="onDrag" v-on:resizing="onResize" :parent="true">
      <p>Hello! I'm a flexible component. You can drag me around and you can resize me.<br>
      X: {{ x }} / Y: {{ y }} - Width: {{ width }} / Height: {{ height }}</p>
    </Deformation>
  </div>
</template>

<script>
import Deformation from 'deformation'

export default {
  data: function () {
    return {
      width: 0,
      height: 0,
      x: 0,
      y: 0
    }
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
```

### 参数

#### draggable
类型: `Boolean`<br>
必要性: `false`<br>
默认值: `true`

定义组件是否可以拖动.

```html
<Deformation :draggable="false">
```

#### resizable
类型: `Boolean`<br>
必要性: `false`<br>
默认值: `true`

定义组件是否可以调整大小.

```html
<Deformation :resizable="false">
```

#### w
类型: `Number`<br>
必要性: `false`<br>
默认值: `200`

定义组件的初始宽度.

```html
<Deformation :w="200">
```

#### h
类型: `Number`<br>
必要性: `false`<br>
默认值: `200`

定义组件的初始高度.

```html
<Deformation :h="200">
```

#### minw
类型: `Number`<br>
必要性: `false`<br>
默认值: `50`

定义组件的最小宽度.

```html
<Deformation :minw="50">
```

#### minh
类型: `Number`<br>
必要性: `false`<br>
默认值: `50`

定义组件的最小高度.

```html
<Deformation :minh="50">
```

#### x
类型: `Number`<br>
必要性: `false`<br>
默认值: `0`

定义组件初始横轴坐标.

```html
<Deformation :x="0">
```

#### y
类型: `Number`<br>
必要性: `false`<br>
默认值: `0`

定义组件初始纵轴坐标.

```html
<Deformation :y="0">
```

#### axis
类型: `String`<br>
必要性: `false`<br>
默认值: `both`

定义组件可以拖动的轴线. 有效值为: `x`, `y`, `both`.

```html
<Deformation :axis="x">
```

#### grid
类型: `Array`<br>
必要性: `false`<br>
默认值: `[1,1]`

定义组件网格.

```html
<Deformation :grid="[1,1]">
```

#### restrain
类型: `Number`<br>
必要性: `false`<br>
默认值: `0`

约束元素宽高只能是restrain的倍数.

```html
<Deformation :restrain="100">
```

---

#### parent
类型: `Boolean`<br>
必要性: `false`<br>
默认值: `false`

限制元素只能在父元素内拖动

```html
<Deformation :parent="true">
```

---

### Events

#### activated

必要性: `false`<br>
Parameters: `-`

组件被初始化事件.

```html
<Deformation @activated="onActivated">
```

#### deactivated

必要性: `false`<br>
Parameters: `-`

组件被销毁事件.

```html
<Deformation @deactivated="onDeactivated">
```

#### resizing

必要性: `false`<br>
Parameters:
* `left` 组件 X 轴坐标
* `top` 组件 Y 轴坐标
* `width` 组件宽度
* `height` 组件高度

组件大小改变事件.

```html
<Deformation @resizing="onResizing">
```

#### resizestop

必要性: `false`<br>
Parameters:
* `left` 组件 X 轴坐标
* `top` 组件 Y 轴坐标
* `width` 组件宽度
* `height` 组件高度

组件大小改变结束事件.

```html
<Deformation @resizestop="onResizstop">
```

#### dragging

必要性: `false`<br>
Parameters:
* `left` 组件 X 轴坐标
* `top` 组件 Y 轴坐标

组件拖动事件.

```html
<Deformation @dragging="onDragging">
```

#### dragstop

必要性: `false`<br>
Parameters:
* `left` 组件 X 轴坐标
* `top` 组件 Y 轴坐标

组件拖动结束事件.

#### keydown

必要性: `false`<br>
Parameters:
* `event` 键值

按键事件.

```html
<Deformation @dragstop="onDragstop">
```
