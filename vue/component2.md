# vue组件的通信

组件之间通信是vue中组件开发中经常需要使用的功能,通常组件之间通信分为以下几种：
- 父子组件之间通信
- 兄弟组件之间通信
- 多层级组件通信(从父组件-> 子组件 -> 孙子组件)

## 父子组件之间通信 
### 方法一：`props` `$emit` `v-on`
- 父组件向子组件通信步骤:
  + 父组件通过动态赋值(`:title="title"`)的方式将数组传递给子组件
  + 子组件通过props来接收父组件传递的值，同时也可以通过props对父组件传递的数组进行限定
- 子组件向父组件通信步骤：
  + 子组件使用`$emit('fn', param)`自定义事件的形式将数据广播出去
  + 父组件使用`@fn="fn"`来接收子组件传递的数据  

接下来我们通过一个父子组件通信的综合例子来了解父子组件通信的具体使用

```html
<!--父组件-->
<template>
  <div class="parent">
    <!--属性赋值的方式传递数据，除string类型的数据，其他类型数据(布尔类型，数值类型，数组类型，对象类型，函数类型等)以及data中的数据都需动态赋值(v-bind:content="content")-->
    <child 
      title="标题" 
      :content="content" 
      :num="1" 
      :isShow="true" 
      :data="data"
      :getData="getData"
      @clear="clearContent"
      :dataList="dataList"></child>
      <!--父组件通过`v-on:自定义事件名`或`@自定义事件名`来触发的方法接收子组件的数据-->
  </div>
</template>
<script>
export default {
  name: 'parent',
  data () {
    return {
      content: '内容',
      dataList: [
        { id: 1001, name: 'john' },
        { id: 1002, name: 'tom' },
      ],
      data: {}
    }
  },
  methods: {
    getData (val) { // 父组件可以通过传递函数的方式将某些操作在子组件运行
      console.log(val)
    },
    clearContent(val) {
      console.log(val); // 唯一参数val是子组件传递的数据
      this.content = val; // 通过更改父组件的值(content)来间接改变子组件展示的数据(content)
    }
  }
}
</script>

<!--子组件-->
<template>
  <div class="child">
    <p class="title">{{ title }}</p>
    <p class="content">{{ content }}</p>
    <button @click="clearContent"></button>
  </div>
</template>
<script>
export default {
  name: 'child',
  props: { // 子组件用props来接收父组件的数据，props接收的数据可直接this打点(如this.title)使用
    title: {
      type: String, // type: 限制父组件传递数据的类型
      required: true, // required: 该数据是否必须
      default: '' // 默认值
    },
    content: {
      type: String,
      default: ''
    },
    isShow: {
      type: Boolean,
      default: false
    },
    num: {
      type: Number
    },
    dataList: {
      type: Array,
      default: () => []
    },
    getData: {
      type: Function
    }
  },
  // props: ['title', 'content'] // 也可以使用数组的方式接收，但数组方式无法限制传值
  methods: {
    clearContent() {
      // 子组件使用$emit通知父组件，并将数据传递给父组件
      // 参数1：自定义事件名，参数2：传递给父组件的数据
      this.$emit('clear', '')
    }
  }
}
</script>
```

## 方法二：中央事件总线`Bus`进行通信 
通过`props`和`$emit`进行组件之间通信虽然方便，但也存在一些问题：
- 多层嵌套组件通信比较麻烦，例如孙子组件给爷爷组件通信，需要通过父组件来衔接，这样容易使组件耦合
- 使用`props`和`$emit`无法实现兄弟组件的通信
基于这些问题，vue提供了中央事件总线，也就是Bus的方式来进行通信。  
Bus的通信原理：其实就是通过一个公共的vue实例媒介来统一管理，需要通信的可以引入Bus，通过分别触发
和监听Bus事件来实现组件之间的通信和传递数据    

1. 首先我们需要建一个vue实例作为总线：
Bus不放在vue原型的方式, 这种方式每次使用Bus都要单独引入Bus
```javascript
// Bus.js
import Vue from 'vue'
export default new Vue
```
Bus作为vue原型的方式，此方式无需每次引入Bus,可直接使用原型上的Bus
```javascript
// plugins/bus.js
import Vue from 'vue'
export default {
  install(_vue, options) {
    _vue.prototyoe.$bus = new Vue()
  }
}
// plugins/index.js
import Vue from 'vue'
import Bus from './bus.js'

const plugins = [Bus]
plugins.forEach(plugin => Vue.use(plugin))
```

2. 使用Bus
```html
<!-- componentA.vue-->
<template>
  <div>
    <b>组件A：</b><button @click="handleBus">传递数值给需要的组件</button>
  </div>
</template>

<script>
  // 非原型方式需要引入Bus
  import Bus from './bus.js' 
  export default {
    methods: {
      handleBus () {
        // Bus.$emit('someBusMessage','来自ComponentA的数据')
        // 原型方式可直接使用，无需引入Bus
        this.$bus.$emit('someBusMessage','来自ComponentA的数据')
      }
    }
  }
</script>

<!-- componentB.vue-->
<template>
  <div>
    <b>组件B：</b><button @click="handleBus">接收组件A的数据</button>
    <p>{{message}}</p>
  </div>
</template>

<script>
  // 非原型方式需要引入Bus
  import Bus from './bus.js' 
  export default {
    data() {
      return {
        message: ''
      }
    },
    created () {
      // Bus.$on('someBusMessage', data => {
      //   this.message = data
      // })
      // 或原型形式
      this.$bus.$on('someBusMessage', data => {
        this.message = data
      })
    },
    beforeDestory () {
      // 手动销毁$on事件，防止多次触发
      // Bus.$off('someBusMessage', this.someBusMessage)
      this.bus.$off('someBusMessage', this.someBusMessage)
    }
  }
</script>
```

## 方法三： `$parent` / `$children` & `$refs`

- `$parent` / `$children`: 指定已创建的实例，在两者之间建立父子关系，子实例可以
  用`this.$parent`访问父实例，子实例被推入父实例的`$children`中
- `$refs`: 一个对象，持有注册过`ref`特性的所以DOM元素和组件实例。`ref`被用来给元
素或子组件注册引用信息。引用信息将注册到父组件的`$refs`对象上
- 如果在普通的DOM元素上使用，引用指向的就是DOM元素，在子组件上，引用就指向组件

例子：
```html
<!--父组件-->
<template>
  <div class="parent">
    <child ref="child"></child>
    <p>{{msg}}</p>
    <button @click="getChildData">显示child传过来的数据</button>
  </div>
</template>
<script>
import child from './child'

export default {
  data() {
    return {
      msg: '',
      content: '父组件数据' 
    }
  },
  components: {
    child
  },
  methods: {
    getChildData () {
      // 使用this.$refs.child拿到标识的组件的数据
      let childData = this.$refs.child
      this.msg = childData.msg
    }
  }
}
</script>

<!--子组件-->
<template>
  <div class="child">
    <p>{{title}} 我的父组件是{{content}}</p>
  </div>
</template>
<script>
export default {
  data() {
    return {
      msg: '子组件信息'
    }
  },
  mounted() {
    // 从父组件里取content数据
    this.content = this.$parent.content
  }
}
</script>
```

## 方法四: 发布订阅模式
[官方GitHub](https://github.com/mroderick/PubSubJS)     
1. 通过`npm i pubsub --save`的方式引入pubsub库  
2. 创建`event-type.js`定义Symbol类型的数据并导出  
```javascript
// event-types.js
export const MY_TOPIC = Symbol('MY_TOPIC')
```
3. 在需要发布(传递事件)的地方引入`pubsub` 和 `event=type.js`,并且通过以下方式发布事件
```javascript
import PubSub from 'pubsub'
import { MY_TOPIC } from './event-types.js'
PubSub.publish(MY_TOPIC, 'world');
```  
4. 在需要订阅(接受事件)的地方引入pubsub和 event=type.js,并且通过以下方式接受事件   
```javascript
import PubSub from 'pubsub'
import { MY_TOPIC } from './event-types.js'
PubSub.subscribe(MY_TOPIC, function (msg, data) {
	console.log(data)
});
```

## 方法五：vuex
[官方传送门](https://vuex.vuejs.org/zh/)  

Vuex是Vue官方推荐的一种解决组件中通信的完美解决方案，也是大型项目中必不可少的方案,
本篇暂不讲解，另有专门章节进行讲解，请移步[传送门](./vuex.md)
