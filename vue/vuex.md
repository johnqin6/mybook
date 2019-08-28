# vuex的使用

## Vuex是什么？
  按[vuex官网](https://vuex.vuejs.org/zh/)的说法，vuex是专为vue.js开发的状态管理模式，它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。作用是在全局中集中管理数据,解决不同组件之间的数据共享和数据持久化。简单的说，在vue项目中，父子组件相互传值相对简单，而兄弟组件之间传值就比较麻烦，特别是大型项目中，相互之间传值更是麻烦，而vuex就是用来统一管理共享的状态和数据，使组件之间的通信更加方便管理。
vuex使用的示意图：
![image.png](https://upload-images.jianshu.io/upload_images/5617865-c3374e498681745f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


下面就是一个简单的vuex示例：
store文件夹index.js
```javaScript
import Vue from 'Vue';  
import Vuex from 'Vuex';  
Vue.use(Vuex);   
export default new Vuex.Store({  
  state: {},   
  getters: {},   
  mutations: {},   
  actions: {}  
});
```    
然后在main.js应用
```javaScript
import store from './store';
new Vue({
  el: '#app',
  router,
  store,
  render: h => h(App)
}) 
```
以上就是一个完整的Vuex结构，每一个Vuex应用就是一个store，在store中包含组件中的共享状态state和改变状态的方法（暂且称作方法）mutations。  
**vuex的核心就是state,getters,mutations,actions**

## 让我们了解vuex的每一部分
### 1. state
state就是项目需要，自己定义的数据结构，放一些公用的数据或状态
```javaScript
const state = {
  count: '',
  name: ''
}
```
定义好了，可以在不同的组件中进行访问，当然也可以直接通过访问修改，不过官方不建议这样使用     
组件中：

```javeScript
this.$store.state.count
this.$store.state.count = 3; // 官方不建议直接修改state的数据,推荐用mutations里的方法修改
```

### 2. getter
getter可以理解为vuex中计算属性，类似组件中计算属性computed
```javaScript
export default new Vuex.Store({  
  state: {
    count: 0,
  },   
  getters: {
    countAdd(state){
      return state.count++;
    }
  },   
  mutations: {},   
  actions: {}  
});
```
在组件中使用(在组件的计算属性computed中使用)有两种方法，
1. 直接使用（store.getter）
2. 使用vuex提供的辅助函数（语法糖）mapGetters
```javaScript
computed: {
  countAdd(){
    return this.$store.getters.countAdd;
  }
}
//或者使用辅助函数mapGetters【需先在组件中引入】
computed: {
  ...mapGetters(['countAdd']);
}
```
```html
<template>
  <div>
    <p>{{countAdd}}</p>
  </div>
</template>
```

### 3. mutation
官方定义：更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutations 非常类似于事件：每个 mutation 都有一 个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```javaScript
export default new Vuex.Store({  
  state: {
    count: 0,
  },   
  mutations: {  //mutations中的方法一般用常量（大写）
    ADDCOUNT(state){  //一个参数代表state
      state.count++; 
    },
    ADDNCOUNT(state,payload){  //payload代表调用传入的对象参数(数据)
      state.count += payload;
    }
  },    
});

//在组件中调用
methods: {
  countAdd(){
    //需要相应的type 调用 store.commit 方法，也就是在mutations中定义的方法名
    this.$store.commit('ADDCOUNT'); 
  },
  countAddN(){
    //this.$store.commit('ADDCOUNT',{n: 10}); 
    //或全写方式
    this.$store.commit({
      type: 'ADDCOUNT',
      n: 10
    });
  }
}
```
mutation也有用以简写的辅助函数mapMutations
```javascript
import { mapMutations } from 'vuex'
export default {
  methods: {
    ...mapMutations({
      add: 'increment' // 映射 this.add() 为 this.$store.commit('increment')
    })
  }
}
/*这样我们可以在vue文件里直接调用函数：this.add()而不用this.$store.commit('increment')这样写了，简化了很多。
需要注意：Mutations必须是同步函数。
如果我们需要异步操作，Mutations就不能满足我们需求了，这时候我们就需要Actions了*/
```
### 4. action
- Action 类似于 mutation，不同在于：
- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作

```javaScript
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    },
    changeCount(context, payload){
      context.commit('increment',payload)
    },
    //异步使用 
    getUserData(context, payload){
      return new Promise((resolve, reject) => {
        getUsersData(payload.param).then(res => {//接口方法
          resovle({code: 200, res});
        }) 
      })
    },
    //异步 async
    async getUserData(context, payload){
      let res = await getUsersData(payload.param);
      return new Promise((resolve, reject) => {
        resovle({code: 200, res});
      })
    },
  }
})
```

在vue页面里想用action，我们可以分发 Action，Action 通过 store.dispatch 方法触发：
```javascript
methods: {
  onIncrement(){
    this.$store.dispatch('increment');
    //载荷方式
    this.$store.dispatch('changeCount',{n: 10});
    //对象方式
    this.$store.dispatch({
      type: 'changeCount',
      n: 10
    });
  }
}
//actions的辅助函数mapActions的使用
methods:{
  ...mapActions{[
    "add":"increment "//函数命名不相同
    //  "increment ":"increment "//函数命名相同
  ]}
}
//调用：this.add()即可。相同时候调用：this.increment()
```
### 5. Modules
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，
store 对象就有可能变得相当臃肿。   
为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己
的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：  
moudle的使用   

```javaScript
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```
## 总结：
mutation 只管存，你给我（dispatch）我就存；action只管中间处理，处理完我就给你，
你怎么存我不管（所有的改变state状态的都是mutation 来操作）；Getter 我只管取，
我不改的（类似计算属性）。
