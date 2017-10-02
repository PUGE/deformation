<p align="center"><img src="https://rawgit.com/mauricius/vue-draggable-resizable/master/docs/resources/logo.png" alt="logo"></p>
<h1 align="center">deformation</h1>


> 自由拖动缩放组件.

## 目录

* [Features](#features)
* [Demo](#demo)
* [Install and basic usage](#install-and-basic-usage)
  * [Props](#props)
  * [Events](#events)
* [Gotchas](#gotchas)
* [Contributing](#contributing)
* [License](#license)

### 特点

* 没有依赖包
* 可以拖动 位置 和 大小
* 可以设定大小调整范围
* 可以限制元素只能在父组件内拖动
* 可以自定义网格
* 可以限制元素只能水平 或 竖直拖动

### 实例

[样品展示](https://mauricius.github.io/vue-draggable-resizable/)

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

#### handles
类型: `Array`<br>
必要性: `false`<br>
默认值: `['tl', 'tm', 'tr', 'mr', 'br', 'bm', 'bl', 'ml']`

Define the array of handles to restrict the element resizing:
* `tl` - Top left
* `tm` - Top middle
* `tr` - Top right
* `mr` - Middle right
* `br` - Bottom right
* `bm` - Bottom middle
* `bl` - Bottom left
* `ml` - Middle left

```html
<Deformation :handles="['tm','bm','ml','mr']">
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

#### parent
类型: `Boolean`<br>
必要性: `false`<br>
默认值: `false`

限制元素移动的速度以及维度.

```html
<Deformation :parent="true">
```

#### maximize
类型: `Boolean`<br>
必要性: `false`<br>
默认值: `false`

If set to `true` allows the component to fill its parent when double-clicked.

```html
<Deformation :maximize="true">
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

```html
<Deformation @dragstop="onDragstop">
```

### 陷阱

Be careful to use appropriate values for `x`, `y`, `w`, `h`, `minh` and `minh` props when you want to restrict the component in its parent element.

### 特色

If `resizing`, `parent` and `maximize` props are `true` you can double-click on the element to make it fill the parent.

## 贡献

Any contribution to the code or any part of the documentation and any idea and/or suggestion are very welcome.

``` bash
# serve with hot reload at localhost:8080
npm run dev

# distribution build
npm run build

# build the docs into gh-pages
npm run docs

# run unit tests
npm run test

```

## License

[MIT license](LICENSE)
