# vue知识点总结

## 了解vue  
vue.js 是前端三大框架之一，是一套用于构建用户界面的渐进式框架。

### 特点：
- 是一套用于构建用户界面的渐进式框架, 只关注视图层，可以和其他类库整合
- 引入MVVM设计模式
- 简单、轻量、快捷、易学
- 虚拟DOM系统, 用户无需操作DOM元素，用户可以专注于业务逻辑，摆脱繁杂的dom操作
- 双向数据绑定, 数据驱动视图的变化
- 组件系统，方便复用，减少重复代码的编写

### 扩展知识点
- 框架和库的区别
  + 框架：是一套完整的解决方案，对项目的侵入性较大，项目如需更换框架，就需重构整个项目
  + 库：提供某一个功能，对项目侵入较小，在项目中也容易切换库进行开发

- MVVM设计思想的理解
MVVM是前端视图层的分层开发思想，主要把每一个页面分层M/V/VM, 其中VM是MVVM思想的核心，
他是M和V之间的调度者。VM提供了数据的双向绑定.
  + M: 数据层
  + V：视图层，即页面的结构
  + VM: 数据层和视图层之间的调度者

## vue知识点总结   

### vue中的双向绑定
vue中用v-model绑定的数据，具有双向绑定的特性，即数据变化，视图随之变化，视图变化，数据也跟着变化。
```html
<template>
  <div>
    <input type="text" v-model="message">
    {{ message }}
  </div>
</template>
<script>
export default {
  data() {
    return {
      message: 'hello vue.js'
    }
  }
}
</script>
```
#### vue中的data
- vue中的data对象是初始化响应式数据的，只有保存在data对象的数据，视图才会对其产生'响应'
  因为data中的属性会被加入到vue的响应系统中.   
- 在项目中data中的数据要用return返回数据
  + 不使用return 包裹的数据会在项目的全局可见，会造成变量污染
  + 使用return包裹的数据变量只在当前组件中生效，不会影响其他组件

### vue中的模板语法
模板语法即插值语法（就是使用“Mustache”语法 (双大括号) 的文本插值）  

使用示例
```html
<template>
  <div>
    <span>hello {{msg}}</span>

    <!-- 模板语法可以使用js表达式 -->
    {{ num + 1}}
    {{ ok ? 'YES' : 'NO' }}
    {{ message.split('').reverse().join('') }}

    <!-- 不能使用的例子 -->
    <!-- 这是语句，不是表达式 -->
    {{ var a = 1 }}
    <!-- 流控制也不会生效，请使用三元表达式 -->
    {{ if (ok) { return message } }}
  </div>
</template>
<script>
export default {
  data() {
    return {
      msg: 'vue.js',
      num: 1
    }
  }
}
</script>
```

#### 常用的文本展示相关指令
- v-once  只渲染元素和组件一次
- v-text  更新元素的文本内容(textContent), 效果与{{msg}}相同
- v-html  更新元素的innerHTML。html标签会被解析
- v-clock  这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕
- v-pre   可以用来显示原始 Mustache 标签

v-clock使用方法： 
```html
<style>
[v-cloak] {
  display: none;
}
</style>
<template>
  <div v-cloak>
    {{ message }}
  </div>
</template>
```

### vue中的条件语句
- v-if 根据指定条件判断是否渲染某块内容
- v-else 与v-if相对应，相反的条件渲染内容
- v-else-if 多条件判断渲染内容 
- v-show 根据条件显示或隐藏某块内容  

```html
<template>
  <div>
    <!-- 单个v-if示例 -->
    <p v-if="isShow">hello wrold</p>
    <button @click="isShow = !isShow"></button>

    <!-- v-if和v-else结合的示例 -->
    <p v-if="isShow">hello wrold</p>
    <p v-else>你好</p>
    <button @click="isShow = !isShow"></button>

    <!-- v-if和v-else-if结合的示例 -->
    <p v-if="val === 1">1</p>
    <p v-else-if="val === 2">2</p>
    <p v-else>3</p>
    <input type="text" v-model="val">

    <!-- 单个v-show示例 -->
    <p v-show="isShow">hello wrold</p>
    <button @click="isShow = !isShow"></button>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        isShow: false,
        val: 1
      }
    }
  }
</script>
```

#### v-if和v-show的对比
- v-if会对渲染的内容和组件进行重建和销毁
- v-if是惰性的，条件为假时不会渲染内容，只有为真时才会渲染内容
- v-show只是对css样式的显示或隐藏, 不管条件真假都会渲染到页面
- 一般来说，v-if有较高的切换开销，v-show有较高的初始渲染开销, 所以切换频繁的使用v-show比较好,
  运行时渲染条件很少变化的适合使用v-if   

### 列表渲染 v-for
v-for指令可以基于一个数组来渲染一个列表

```html
<template>
  <div>
    <!-- 数组渲染 -->
    <div v-for="(item, index) in list" :key="index">
      {{item.name}} --> {{index}}
    <div>

    <!-- 对象渲染 -->
    <div v-for="(key, val, index) in obj" :key="index">
      {{key}}: {{val}} --> {{index}}
    <div>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        list: [
          { name: 'john', id: 1 },
          { name: 'john1', id: 2 },
          { name: 'john2', id: 3 },
        ],
        obj: {
          name: 'john',
          age: 21
        }
      }
    }
  }
</script>
```
