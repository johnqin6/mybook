# vue中axios的使用和封装

## 了解axios
axios是vue官方推荐使用的用于后台接口请求的库, 它基于promise的http库，可运行于浏览器端和node.js。
有很多优秀的特性，如：拦截请求和响应、取消请求、转换json、客户端防御XSRF等。

安装axios
> npm install axios 或 yarn add axios  

## vue中对axios的封装

### 为何对axios的封装

目的：帮助我们简化代码以及后期的更新维护

### 封装示例1  

```javascript
// http.js
/**
 * axios封装
 * 请求拦截，响应拦截，错误统一处理
*/
// 引入axios
import axios from 'axios'
// qs模块用来序列化post类型的数据
import qs from 'qs'
// vant的toast提示框组件
import { Toast } from 'vant'
import store from '@/store/index'

/**
 * 项目环境可能有开发环境、测试环境和生产环境。我们通过node的环境变量来匹配我们的默认的接口url前缀。 
 * axios.defaults.baseURL可以设置axios的默认请求地址就不多说了
*/
// 环境的切换
if (process.env.NODE_ENV === 'development') {
  axios.default.baseURL = '/api'
} else if (prcosee.env.NODE_ENV === 'debug') {
  axios.default.baseURL = ''
} else if (prcosee.env.NODE_ENV === 'production') {
  axios.default.baseURL = 'http://api.123dailu.com'
}

// 请求超时时间
axios.defaults.timeout = 10000

// 设置请求头
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8'

// 请求拦截器
axios.interceptors.request.use(
  config => {
    /**
     * 每次发送请求之前判断是否存在token,如果存在，则统一在http请求的header添加token,这样就不用
     * 每次请求都手动添加了，即使本地存储存在token, 也有可能token过期,所有在拦截器中要对返回状态进行判断
    */
    const token = store.state.token
    token && (config.headers.Authorization = token)
    return config
  },
  error => {
    return Promise.error(error)
  }
)

// 响应拦截器
axios.interceptors.response.use(
  response => {
    if (response.status === 200) {
      return Promise.resolve(response)
    } else {
      return Promise.reject(response)
    }
  },
  // 服务器状态不是200的情况
  error => {
    if (error.response.status) {
      switch (error.response.status) {
        /**
         * 401: 未登录
         * 未登录则跳转登录页面，并携带当前页面的路径
         * 在登陆成功后返回当前页面，这一步需要在登陆页操作
        */
        case 401:
          router.replace({
            path: '/login',
            query: { redirect: router.currentRout.fullPath }
          })
          break
        /**
         * 403 token过期
         * 登录过期对用户进行提示
         * 清除本地token和清空vuex中token对象
         * 跳转登录页面
        */
        case 403:
          Toast({
            message: '登录过期，请重新登录',
            duration: 1000,
            forbidClick: true
          })
          // 清除token
          localStorage.removeItem('token')
          store.commit('loginSucess', null)
          // 跳转登陆页面，并将要浏览的页面fullPath传过去, 登录成功后跳转需要访问的页面
          setTimeout(() => {
            router.replace({
              path: '/login',
              query: {
                redirect: router.currentRout.fullPath
              }
            })
          }, 1000)
          break
        // 请求不存在
        case 404:
          Toast({
            message: '网络请求不存在',
            duration: 1500,
            forbidClick: true
          })
          break
        // 其他错误，直接抛出错误提示
        default: 
          Toast({
            message: error.response.data.message,
            duration: 1500,
            forbidClick: true
          })
      }
      return Promise.reject(error.response)
    }
  }
)

/**
 * get方法，对应get请求
 * @param {String} url [请求的url地址]
 * @param {Object} url [请求时携带的参数]
*/
export function get (url, params) {
  return new Promise((resolve, reject) => {
    axios.get(url, {
      params: params
    }).then(res => {
      resolve(res.data)
    }).catch(err => {
      reject(err.data)
    })
  })
}

/**
 * post方法，对应post请求
 * @param {String} url [请求的url地址]
 * @param {Object} url [请求时携带的参数]
*/
export function post (url, params) {
  return new Promise((resolve, reject) => {
    // 使用node的qs模块序列化参数, 如不序列化，后台拿不到提交的数据
    axios.post(url, qs.stringify(params)).then(res => {
      resolve(res.data)
    }).catch(err => {
      erject(err.data)
    })
  })
}
```

## api的统一管理

```javascript
/**
 * api接口统一管理
*/
// 首先在api.js中引入封装好的get和post方法
import {get, post} from './http'

// 封装api方法, 例如地址编辑的方法`apiAddress` p是传回后台的数据
export const apiAddress = p => post('api/addressEdit', p)
```  

在页面中调用封装好的api接口

```javascript
import { apiAddress } from '@/request/api'
export default {
  created () {
    this.loadData()
  },
  methods: {
    loadData() {
      // 调用接口
      apiAddress({
        type: 0,
        address: '河南'
      }).then(res => {
        console.log(res)
      })
    }
  }
}
```

### 封装示例2

与示例1有以下不同：
- 优化axios封装，去除之前的get和post
- 断网情况的处理
- 更加模块化的api管理
- 接口域名有多个的情况
- api挂载到vue.prototype上省去引入的步骤  

代码示例

```javascript
// http.js
/**
* axios封装
* 请求拦截，响应拦截，错误统一处理
*/
import axios from 'axios'
import router from '../router'
import store from '../store/index'
import { Toast } from 'vant'
/**
* 提示函数
* 禁止点击蒙层，显示一秒后关闭
*/
const tip = msg => {
  Toast({
    message: msg,
    duration: 1000,
    forbidClick: true
  })
}

/**
* 跳转登陆页
* 携带当前页面路由，以其在登陆页完成登录后返回当前页面
*/
const toLogin = () => {
  router.replace({
    path: '/login',
    query: {
      redirect: router.currentRoute.fullPath
    }
  })
}

/**
* 请求失败后的错误统一处理
* @parma {Number} status 请求失败的状态码
*/
const errorHandle = (status, other) => {
  switch (status) {
    // 401: 未登陆状态，跳转登陆页
    case 401:
      toLogin()
      break
    // 403 token过期， 清除token并跳转登陆页
    case 403:
      tip('登录过期，请重新登录')
      localStorage.removeItem('token')
      store.commit('loginSuccess', null)
      setTimeout(() => {
        toLogin()
      }, 1000)
      break
    // 404请求不存在
    case 404:
      tip('请求的资源不存在')
      break
    default:
      console.log(other)
  }
}

// 创建axios实例
const instance = axios.create({ timeout: 1000 * 12})
// 设置post请求头
instance.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';

/**
* 请求拦截
* 每次请求前，如果存在token则在请求头中携带token
*/
instance.interceptors.request.use(
  config => {
    // 登录流程控制中，根据本地是否存在token判断用户的登录情况        
    // 但是即使token存在，也有可能token是过期的，所以在每次的请求头中携带token        
    // 后台根据携带的token判断用户的登录情况，并返回给我们对应的状态码        
    // 而后我们可以在响应拦截器中，根据状态码进行一些统一的操作。
    const token = store.state.token
    token && (config.headers.Authorization = token)
    return config
  },
  error => Promise.error(error)
)

// 响应拦截器
instance.interceptors.response.use(
  // 请求成功
  res => res.status === 200 ? Promise.resolve(res) : Promise.reject(res),
  // 请求失败
  error => {
    const { response } = error
    if (response) {
      // 请求已发出，但是不在2xx的范围
      errorHandle(response.status, response.data.message)
      return Promise.reject(response)
    } else {
      // 处理断网情况
      // eg: 请求超时或断网时，更新state的network状态
      // network状态在app.vue中控制着一个全局的断网提示组件的显示隐藏
      // 关于断网组件中刷新重新获取数据，会在断网组件中说明
      if (!window.navigator.onLine) {
        store.commit('changeNetwork', false)
      } else {
        return Promise.reject(error)
      }
    }
  }
)

export default instance
```
1. 去掉了之前get和post方法的封装，通过创建一个axios实例然后export default方法导出，这样使用起来更灵活一些。
2. 去掉了通过环境变量控制baseUrl的值。考虑到接口会有多个不同域名的情况，所以准备通过js变量来控制接口域名。这点具体在api里会介绍。  
3. 增加了请求超时，即断网状态的处理。说下思路，当断网时，通过更新vuex中network的状态来控制断网提示组件的显示隐藏。断网提示一般会有重新加载数据的操作，这步会在后面对应的地方介绍。  
4. 公用函数进行抽出，简化代码，尽量保证单一职责原则。 

### api统一管理优化
- 更加模块化
- 更方便多人开发，有效减少解决命名冲突
- 处理接口域名有多个情况

新建api文件夹，里面有index.js和base.js，以及多个根据模块划分的接口js文件.
index.js是api的出口，base.js管理接口域名, 其他js管理各个模块的接口。

```javascript
// index.js
// api接口的统一出口
import artice from '@/api/article'

export default {
  artice
}

// base.js
// 接口域名的管理
const base = {
  sq: 'https://demo.com/api',
  bd: 'http://localhost:3000/api'
}
export default base

// artice.js
/**
 * article模块接口列表
 */

import base from './base'; // 导入接口域名列表
import axios from '@/utils/http'; // 导入http中创建的axios实例
import qs from 'qs'; // 根据需求是否导入qs模块

const article = {    
    // 新闻列表    
    articleList () {        
        return axios.get(`${base.sq}/topics`);    
    },    
    // 新闻详情,演示    
    articleDetail (id, params) {        
        return axios.get(`${base.sq}/topic/${id}`, {            
            params: params        
        });    
    },
    // post提交    
    login (params) {        
        return axios.post(`${base.sq}/accesstoken`, qs.stringify(params));    
    }
    // 其他接口…………
}

export default article;
```

#### 为方便api的调用，可以将其挂载在vue原型上
```javascript
// main.js
...
import api from './api'

Vue.prototype.$api = api // 将api挂载在vue原型上
```

#### 页面中调用接口
```javascript
export default {
  methods: {
    loadData (id) {
      this.$api.article.articleDetail(id, {api: 123}).then(res => {

      })
    }
  }
}
```

#### 断网处理
```html
<template>  
    <div id="app">    
        <div v-if="!network">      
            <h3>我没网了</h3>      
            <div @click="onRefresh">刷新</div>      
        </div>    
        <router-view/>      
    </div>
</template>

<script>
  import { mapState } from 'vuex';
  export default {  
    name: 'App',  
    computed: {    
      ...mapState(['network'])  
    },  
    methods: {    
      // 通过跳转一个空页面再返回的方式来实现刷新当前页面数据的目的
      onRefresh () {      
        this.$router.replace('/refresh')    
      }  
    }
  }
</script>
```    
在断网的时候，来更新vue中network的状态，那么这里我们根据network的状态来判断是否需要加载这个断网组件。断网情况下，加载断网组件，不加载对应页面的组件。当点击刷新的时候，我们通过跳转refesh页面然后立即返回的方式来实现重新获取数据的操作。因此我们需要新建一个refresh.vue页面，并在其beforeRouteEnter钩子中再返回当前页面。

```javascript
// refresh.vue
beforeRouteEnter (to, from, next) {
  next(vm => {
    vm.$router.replace(from.fullPath)
  })
}
```

## 扩展知识点

### 请求拦截
我们在发送请求前可以进行一个请求的拦截，为什么要拦截呢，我们拦截请求是用来做什么的呢？比如，有些请求是需要用户登录之后才能访问的，或者post请求的时候，我们需要序列化我们提交的数据。这时候，我们可以在请求被发送之前进行一个拦截，从而进行我们想要的操作。

### 在请求拦截中添加token
token，一般是在登录完成之后，将用户的token通过localStorage或者cookie存在本地，然后用户每次在进入页面的时候（即在main.js中），会首先从本地存储中读取token，如果token存在说明用户已经登陆过，则更新vuex中的token状态。然后，在每次请求接口的时候，都会在请求的header中携带token，后台人员就可以根据你携带的token来判断你的登录是否过期，如果没有携带，则说明没有登录过。这时候或许有些小伙伴会有疑问了，就是每个请求都携带token，那么要是一个页面不需要用户登录就可以访问的怎么办呢？其实，你前端的请求可以携带token，但是后台可以选择不接收啊！

