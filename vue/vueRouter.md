# vue-router 路由知识点总结
[官方传送门](https://router.vuejs.org/zh/)    
vue Router 是vue.js官方的路由管理器。它和vue.js的核心深度集成，让构建单页面应用变得易如反掌，
也是我们项目开发中必备技能之一。 

## 一、初始化项目集成vue-router  
- 如已安装vue-cli3，在创建项目时会有提示是否安装vue-router的选项，选中敲空格即可集成安装
- 若创建项目时，未集成安装，也可手动安装 `yarn add vue-router`  
- 安装好vue-router后，可以在src文件夹建一个router.js文件或router文件夹来统一管理项目中的路由

## 二、使用vue-router 
### 配置简单的路由
1. 首先在src文件夹下创建一个`router.js`文件，然后在其中构建路由
```javascript
// router.js
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/home'

Vue.use(Router)
export default new Router({
    // 构建路由数组
    routes: [ // 注意这里是routes, 而不是routers
      {
        path: '/home',  // 路由地址即浏览器输入框域名后跟的路径
        name: 'home', // 路由名称，可用于路由跳转
        component: Home // 路由对应的组件，即输入路由要展示的组件
      }
    ]
})
```
2. 在main.js中引入和注册路由
```javascript
// main.js
...
import router from './router'

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```
3. 在app.vue中配置路由出口`<router-view></router-view>`,这一步很重要，没有出口，组件将不会显示
```html
<!-- app.vue -->
<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>
```
到这一步，一个简单的路由配置已经配完了，此时就可在浏览器输入`localhost:8080/#/home`,愉快的访问了，
如果有其他组件，就可以继续在`router.js`中配置。

### 配置嵌套路由
vue-router不仅可以配置简单的一层(一级)路由，也可以配置多层(多级)路由，即嵌套路由。
1. 首先我们将我们刚才在router.js配置的简单路由进行一些修改。
```javascript
// router.js
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/home'
import Recommend from './views/recommend'

Vue.use(Router)
export default new Router({
    // 构建路由数组
    routes: [ // 注意这里是routes, 而不是routers
      {
        path: '/home', 
        name: 'home',
        component: Home,
        children: [ // children: 用来配置子路由，可以配置多个子路由
          {
            path: 'recommend',
            name: 'recommend',
            component: Recommend
          }
        ]
      }
    ]
})
```
2. 在home组件中添加子路由出口`<router-view></router-view>` 
```html
<template>
  <div class="home">
    <h2>这是home组件</h2>
    <!-- 这是子路由出口 -->
    <router-view></router-view>
  </div>
</template>
```
在home配置好子路由出口后，我们就可以在浏览器中输入`localhost:8080/#/home/recommend`,来展示
嵌套在home组件下的子组件了
嵌套路由特点：
- 会在父路由后加上一个子路由的路径，如`localhost:8080/#/home/recommend`
- 嵌套路由展示的页面会子组件嵌套在父组件中，并随子路由的切换而改变。
  + 例如：页面的tab列表切换，侧边菜单都是使用嵌套路由实现的。

### 路由的跳转
vue-router中路由跳转既可以使用vue-router提供的`<router-link></router-link>`标签来跳转,
也可以使用js进行跳转(vue-router提供的方法$router.push和$router.replace)

#### 使用`<router-link></router-link>`标签路由跳转
说明:router-link其实就是封装的a标签

- 静态路由跳转
```html
<template>
  <div id="app">
    <p>导航 ：
      <!-- router-link 过传入 `to` 属性指定链接 -->
      <router-link to="/">首页</router-link> |
      <router-link to="/home">Home页面</router-link> |
      <router-link to="/home/recommend">-recommend界面</router-link> |
    </p>
    <router-view></router-view>
  </div>
</template>
```
- 动态路由跳转和参数传递
1. 首先需要在`router.js`中配置动态参数
```javascript
// router.js
export default new Router({
    // 构建路由数组
    routes: [ // 注意这里是routes, 而不是routers
      {
        // path: '/recommendDetail/:id', // 动态路径参数 以冒号开头
        path: '/recommendDetail/:id?', // 在参数后加?，表示参数可有可无能匹配的
        name: 'recommendDetail',
        component: RecommendDetail,
      }
    ]
})
```
2. 在`router-link`配置跳转参数
```html
<template>
  <div id="app">
    <p>导航 ：
      <!-- router-link 过传入 `to` 属性指定链接 -->
      <!-- 命名路由 即使用路由名称传参 -->
      <router-link to="{ name: 'recommendDetail', params: { id: '11'}}">
        -recommendDetail界面
      </router-link> |
      <!-- 当参数为`/:id?`的形式时，id为空也可以匹配到该路由 -->
      <router-link to="{ name: 'recommendDetail', params: { id: ''}}">
        -recommendDetail界面
      </router-link> |
      <!-- 使用this.$route可以拿到路由数据(路由名称，参数，meta等数据) 注意是`$route`不是`$router`-->
      <p>{{ $route }}</p>
    </p>
    <router-view></router-view>
  </div>
</template>
```

#### 使用编程式路由跳转，即使用`$router.push()或$router.replace`
- 相关api
  + $router.push(path): 相当于点击路由链接（可以返回到当前路由界面）
  + $router.replace(path): 用新路由替换当前路由(不可以返回到当前路由界面)
  + $router.back(): 请求(返回)上一个记录路由, 也就是路由后退一步
  + $router.go(-1): 请求(返回)上一个记录路由, 也就是路由后退一步
  + $router.go(1): 请求下一个记录路由，也就是路由前进一步  

1. 我们还是使用在`router.js`中配置的动态参数
```javascript
// router.js
export default new Router({
    // 构建路由数组
    routes: [ // 注意这里是routes, 而不是routers
      {
        // path: '/recommendDetail/:id', // 动态路径参数 以冒号开头
        path: '/recommendDetail/:id?', // 在参数后加?，表示参数可有可无能匹配的
        // path: '/recommendDetail?id', // 在参数前加?, 相当于'recommendDetail?id=11'的形式传参
        name: 'recommendDetail',
        component: RecommendDetail,
      }
    ]
})
```
2. 此时我们无需在组件结构中调用，而是在js中调用
```javascript
export default {
  methods: {
    toRecommendDetail(id) {
      this.$router.push({
        // path: '/recommendDetail', // 可以使用路径的方式
        name: '/recommendDetail', // 也可以使用路由名称的方式
        // query: { // query: 用来配置参数，匹配'/recommendDetail?id'形式的路由
        //   id: '11'
        // }
        params: { // params: 用来配置参数，匹配'/recommendDetail/:id?'形式的路由
          id: '11'
        }
      })
    }
  }
}
```

### 路由重定向
vue-router也可以进行路由重定向
```javascript
// router.js
const router = new VueRouter({
  routes: [
    { path: '/', redirect: '/home' }, // redirect属性用来指定重定向到的路由
    { path: '/', redirect: { name: 'home' } }, // 也可以使用命名路由重定向
    { path: '/home', name: 'home', component: Home}
  ]
})
export default router
```
路由重定向后，输入路由后会指向重定向的路由, 如上例：输入'/', 会打开'/home'的路由页面 

### 路由守卫
#### 什么是路由守卫
通过路由守卫可以刷新或进入的路由界面进行权限控制，相当于vue全局的中间件

#### 全局守卫
任何一个路由进入都可以先拦截,然后根据添加跳转不同的路由。
```javascript
// router.js
const router = new VueRouter({......})
router.beforeEach((to, from, next) => {
  /**
   * 参数解析
   * to: 即将进入的路由对象
   * from: 即将离开的路由对象
   * next: next() 执行下一步操作, 可传参，
   *       + 不传参，直接执行下一步操作 next()
   *       + 参数为：false, 终止导航，重定向即将离开的路由 next(false)
   *       + 参数为路由：跳转指定路由，next('/')
   *       + 参数为`error`, 终止导航，并将错误抛给router.onError(),  next(error)
  */
  let isLogin = localStorage('isLogin')
  if (to.path !== 'login') { // 验证是否登录
    if (isLogin) {
      next()
    } else {
      next('/login?redirect='+ to.path)
    }
  } else { // 不需要验证的路由
    next()
  }
})
```
#### 局部守卫
只控制某单个组件的路由在`routes`数组里做控制,和全局一样需要`beforeEach`
```javascript
// router.js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      // beforeEnter, 路由的生命周期，在进入路由之前进行拦截，并进行特定操作
      beforeEnter: (to, from, next) => { 
        //...
      }
    }
  ]
})
```
#### 路由的生命周期
- beforeRouteEnter: 进入之前调用
- beforeRouteUpdate: 路由参数变化时
- beforeRouteLeave: 路由离开时 
这些路由的生命周期可以在组件中使用

### 路由懒加载
即用即加载，用于提高性能
使用方法：改变引入组件的方法, 进入路由时再引入组件
```javascript
// router.js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: () => import('./views/foo')
    }
  ]
})
```

### $router和$route的区别
- $router
router是VueRouter的一个对象，通过Vue.use(VueRouter)和VueRouter构造函数得到一个router的实例对象,这个对象中是一个全局的对象，他包含了所有的路由包含了许多关键的对象和属性
- $route
route是一个跳转的路由对象，每一个路由都会有一个route对象，是一个局部的对象可以获取对应的name,path,params,query等
