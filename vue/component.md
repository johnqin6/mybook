# vue中的组件开发
组件化是长期开发过程中一个提炼精华的过程，目的主要是以下几点：
- 提高复用性
- 解耦
- 提升未来的开发效率

## 全局组件开发

### 编写组件
```html
<!--一般在src/components文件夹下放置全局(通用)组件-->
<template>
  <div class="t-header">
    {{ title }}
  </div>
</template>
<script>
export default {
  name: 't-header',
  // props: ['title'] // props 用于子组件接收父组件的传的值
  props: { // 也可以以对象的格式接收父组件传递的值，并对值的类型进行限定
    title: String, // String: 表示 该字段只能传递string类型的数据
    default: ''  // default: 默认值
  }
}
</script>
```

### 注册全局组件
注册全局组件有多种方式

- 全局组件依次引入注册的方式
1. 首先在src/components新建index.js文件
2. 在index.js文件中对组件进行依次引入注册   

```javascript
// index.js
import tHeader from './tHeader'
import tBody from './tBody'

const install = Vue => {
  Vue.component('tHeader', tHeader)
  Vue.component('tBody', tBody)
}

export default install
```
3. 在main.js中全局组件在vue中注册  

```javascript
import components from './components'

Vue.use(components) // 注册全局组件
```
4. 使用全局组件
```html
<!--app.vue-->
<template>
  <div class="app">
    <!--
      父组件中用属性赋值的方式传递数据给子组件 
      非字符串类型(布尔类型，数值类型,复杂类型)或动态数据可以使用动态赋值的方式
      如：<t-header :title="title"></t-header>
    -->
    <t-header title="头部"></t-header>
  </div>
</template>
```
- 遍历数组的方式注册全局组件 
与第一种方式步骤相似，只是将第二步和第三步整合了，并以数组的方式注册  

```javascript
// index.js
import Vue from 'vue' // 引入vue，可以直接在本文件完成注册，无需再在main.js进行操作

import tHeader from './tHeader'
import tBody from './tBody'

const components = [
  tHeader,
  tBody
]
// 遍历注册 注意点：需保证组件名称与组件文件名一直
components.forEach(component => Vue.component(component.name, component));
```

## 局部组件
局部组件和全局组件编写方式一样，只是注册方式和使用范围不同
- 全局组件可全局使用，即可以在任何地方都可不经再次注册使用
- 局部组件只能在引入该组件并进行局部组成的父组件中使用

### 编写局部组件
```html
<!--一般在对应模块下放置局部组件-->
<template>
  <div class="remmond-header">
    {{ title }}
  </div>
</template>
<script>
export default {
  name: 'remmond-header',
  // props: ['title'] // props 用于子组件接收父组件的传的值
  props: { // 也可以以对象的格式接收父组件传递的值，并对值的类型进行限定
    type: String, // String: 表示 该字段只能传递string类型的数据
    default: ''  // default: 默认值
  }
}
</script>
```
### 注册局部组件
局部组件是在使用时注册  

```html
<!--remmond.vue-->
<template>
  <div class="remmond">
    <!--使用组件-->
    <remmond-header title="头部"></remmond-header>
  </div>
</template>
<script>
// 引入组件
import remmondHeader from './remmondHeader'

export default {
  components: {  // 局部注册组件
    remmondHeader
  }
}
</script>
```
