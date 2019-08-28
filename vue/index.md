# vue知识点总结

## 了解vue  
vue.js 是前端三大框架之一，是一套用于构建用户界面的渐进式框架。
[官方网址](https://cn.vuejs.org/index.html)

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
### vue生命周期
- beforeCreate 实例初始化后，数据观测(data observer)和 event/watcher 事件配置之前被调用
- create 实例创建完成后被调用，此时数据(数据观测-data observer, 属性和方法的运算，watch/event事件回调)已初始化,但挂载阶段还未开始。    
- beforeMount 在挂载开始之前被调用, 相关的render函数首次被调用
- mounted el被新创建的vm.$el替换，并挂载到实例上后调用该钩子
- beforeUpdate 数据更新时调用，发生在虚拟dom重新渲染和打补丁之前。可在此钩子进一步更改状态，不会触发重渲染过程
- updated 由于数据更改导致的虚拟dom重新渲染和打补丁，在此之后调用
- beforeDestroy  实例销毁之前调用，在这一步，实例仍可用
- destroyed  vue实例销毁后调用。调用后，vue实例的所有东西都会解绑定，所有事件监听被移除，所有子实例被销毁。该钩子在服务器端渲染期间不被调用
- activated keep-alive 组件激活时调用, 该钩子在服务器端渲染期间不被调用
- deactivated keep-alive 组件停用时调用, 该钩子在服务器端渲染期间不被调用

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

### class与style绑定

#### 动态绑定类名
在vue中我们可以动态绑定类名, 方便我们已特定的条件切换class。
```html
<!-- 动态绑定类名 -->
<div v-bind:class="{'active': isActive }"></div>
<!-- 以语法糖:代替v-bind的方式 动态绑定类名 -->
<div :class="{'active': isActive }"></div>
<!-- 动态绑定多个类名 -->
<div class="static" :class="{'active': isActive, 'text-danger': hasError }"></div>
<!-- 将动态绑定类名的数据对象摘出内联样式 -->
<div :class="classObject"></div>
<script>
export default {
  data() {
    return {
      classObject: {
        active: true,
        'text-danger': false
      }
    }
  }
}
// 或放在计算属性中
export default {
  data() {
    return {
      isActive: true,
      error: null
    }
  },
  computed: {
    classObject() {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
}
</script>
<!-- 数据语法 -->
<div :class="[activeClass, errorClass]"></div>
<script>
 export default {
   data() {
     return {
       activeClass: 'active',
       errorClass: 'text-danger'
     }
   }
 }
</script>
<!-- 三元表达式的方式来切换class -->
<div :class="[isActive ? activeClass : '', errorClass]"></div>
<!-- 在数组中使用对象语法 -->
<div :class="[{activeClass: isActive}, errorClass]"></div>
```  

##### 案例解析
- vue中用`v-bind`来动态绑定属性(动态绑定后，属性值可随数据的变化而变化), 也可以使用语法糖`:`来简写
  + 例如：`<img :src="imgSrc" />`   
- vue中动态绑定的方式有多种(以对象的形式，数组的形式，三元表达式等),可随实际情况选取不同的方式

#### 动态绑定内联样式v-bind:style
vue不仅能动态绑定类名，还能动态绑定内联样式
```html
<!-- 对样式单独绑定 -->
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">hello</div>
<!-- 直接绑定一个样式对象 -->
<div :style="styleObject">hello</div>

<script>
export default {
  data() {
    return {
      activeColor: 'red',
      fontSize: 30,
      styleObject: {
        color: 'red',
        fontSize: '13px'
      }
    }
  }
}
</script>

<!-- v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上 -->
<div :style="[baseStyles, oveidingStyles]"></div>
```

### 计算属性computed  
在计算属性内注册的内容会随内部值的变化而变化，并将数据缓存起来
```html
<p>姓：<input type="text" v-model="firstName"/></p>
<p>名：<input type="text" v-model="lastName"/></p>
<p>{{ username }}</p>
<script>
export default {
  data() {
    return {
      firstName: '',
      lastName: ''
    }
  },
  computed: {
    username() {
      console.log('计算了一次') 
      return this.firstName + ' ' + this.lastName;
    }
  }
}
</script>
```
username的值会随firstName和lastName的值变化而变化
同时会将username缓存起来，只要firstName和lastName的值不变化,
就不会再次计算

### 监听器 water()

```html
<p>姓：<input type="text" v-model="firstName"/></p>
<p>名：<input type="text" v-model="lastName"/></p>
<p>{{ username }}</p>
<script>
export default {
  data() {
    return {
      firstName: '',
      lastName: '',
      username: ''
    }
  },
  methods: {
    firstName(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.username = this.firstName + ' ' + this.lastName;
      }
    },
    lastName(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.username = this.firstName + ' ' + this.lastName;
      }
    }
  }
}
</script>
```
使用watch监听fullName的依赖值，当不是fullName依赖的内容改变时，console.log不会执行，只有当fullName依赖的内容发生改变时，console.log才会被执行。

虽然watch也能实现数据缓存，性能也不错，但相比computed来说，复杂了很多，所以如果一个功能既可以通过computed实现，methods实现，watch实现，优先推荐computed来实现，因为用这种方法写代码，既简洁，性能又不错。

### 事件 methods
methods内的方法也可以监听数据的变化, 但是methods的方法没有缓存机制, 一但数据有变化就会执行。
当computed属性和methods都可以实现，应该优先使用计算属性(computed)
```html
<p>姓：<input type="text" v-model="firstName"/></p>
<p>名：<input type="text" v-model="lastName"/></p>
<p>{{ username() }}</p>
<script>
export default {
  data() {
    return {
      firstName: '',
      lastName: ''
    }
  },
  methods: {
    username() {
      console.log('计算了一次') 
      return this.firstName + ' ' + this.lastName;
    }
  }
}
</script>
```

### 事件处理和事件修饰符
vue中使用`v-on`指令来进行事件监听, js的事件方法大部分都可以使用
```html
<!-- vue中用 v-on 监听的事件如无参数无需加() -->
<button v-on:click="add">加1</button>
<!-- vue中可以用语法糖@来代替v-on -->
<button @click="add">加1</button>
<p>{{num}}</p>
<script>
export default {
  data() {
    return {
      num: 0
    }
  },
  methods: {
    // vue中的方法需要写在methods里面
    add() {
      this.num = this.num + 1;
    }
  }
}
</script>
```

#### 修饰符
- 事件修饰符
  + .stop: 阻止冒泡
  + .prevent: 阻止默认事件
  + .once: 事件只执行一次
  + .capture: 使用事件捕获模式，即由外向内执行
  + .self: 只在 event.target 是当前元素自身时触发处理函数
  + .passive: 滚动事件的默认行为 (即滚动行为) 将会立即触发
    * `<div v-on:scroll.passive="onScroll">...</div>` 
    * .passive和.prevent不能同时使用, 因为.prevent会被忽略，且浏览器会报出警告
    * .passive 修饰符能提高移动端性能
  + 修饰符可串联使用: @click.stop.prevent   

**使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 v-on:click.prevent.self 会阻止所有的点击，而 v-on:click.self.prevent 只会阻止对元素自身的点击。**
- 按键修饰符
  + .enter
  + .tab
  + .delete (捕获“删除”和“退格”键)
  + .esc
  + .space
  + .up
  + .down
  + .left
  + .right
```html
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```
- 系统修饰符
可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器
  + .ctrl
  + .alt
  + .shift
  + .meta  

```html
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

- 鼠标按键修饰符
  + .left
  + .right
  + .middle
