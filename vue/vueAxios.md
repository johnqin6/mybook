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

## 扩展知识点

### 请求拦截
我们在发送请求前可以进行一个请求的拦截，为什么要拦截呢，我们拦截请求是用来做什么的呢？比如，有些请求是需要用户登录之后才能访问的，或者post请求的时候，我们需要序列化我们提交的数据。这时候，我们可以在请求被发送之前进行一个拦截，从而进行我们想要的操作。

### 在请求拦截中添加token
token，一般是在登录完成之后，将用户的token通过localStorage或者cookie存在本地，然后用户每次在进入页面的时候（即在main.js中），会首先从本地存储中读取token，如果token存在说明用户已经登陆过，则更新vuex中的token状态。然后，在每次请求接口的时候，都会在请求的header中携带token，后台人员就可以根据你携带的token来判断你的登录是否过期，如果没有携带，则说明没有登录过。这时候或许有些小伙伴会有疑问了，就是每个请求都携带token，那么要是一个页面不需要用户登录就可以访问的怎么办呢？其实，你前端的请求可以携带token，但是后台可以选择不接收啊！

