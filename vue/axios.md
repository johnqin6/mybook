# axios请求

## axios介绍
基于promise用于浏览器和node.js的http客户端    

特点：   
- 支持浏览器和node.js
- 支持promise
- 能拦截请求和响应
- 能转换请求和响应数据
- 能取消请求
- 自动转换json数据
- 浏览器端支持防止CSRF(跨站请求伪造)

## 安装
- npm 安装
> npm install axios   

- cdn引入
> <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

## axios的使用

### 示例
- 发起一个get请求
```javascript
axios.get('/user?id=12345')
  .then(function(response){
    console.log(response)
  })
  .catch(function(error){
    console.log(error)
  })

// 以参数的形式传递
axios.get('/user',{
  params: {
    id: 12345
  }
})
  .then(function(response){
    console.log(response)
  })
  .catch(function(error){
    console.log(error)
  })
```

- 发起一个post请求
```javascript
axios.post('/user', {
  firstName: 'zhang',
  lastName: 'san'
}).then(res => {
  console.log(res)
}).catch(err => {
  console.log(err)
})
```
- 同时发起多个请求
```javascript
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // Both requests are now complete
  }));
```

### axios api 
可通过导入相关配置发起请求
- axios(config)
```javascript
// 发起get请求(get是默认的请求方法)
axios('/user/123')
// 发起一个post请求
axios({
  method: 'post',
  url: '/user/123',
  data: {
    firstName: 'zhang',
    lastName: 'san'
  }
})
// 获取远程图片
axios({
  method: 'get',
  url: 'http://bit.ly/2mTM3nY',
  responseType: 'stream'
}).then(res => {
  res.data.pipe(fs.createWriteStram('ada_Lovelace.jpg'))
})
```





### 请求方法别名
- axios.request(config)
- axios.get(url[, config])
- axios.post(url[, data[, config]])
- axios.delete(url[, config])
- axios.head(url[, config])
- axios.options(url[, config])
- axios.put(url[, data[, config]])
- axios.patch(url[, data[, config]])

**注释**
当使用以上别名方法时，url, method和data等属性无需在config中重复声明

### 处理同时发起多个请求的辅助函数
- axios.all(iterable)
- axios.spread(callback)

### 创建一个通用实例
axios.creat([config])
```javascript
var instance = axios.create({
  baseURL: 'http://localhost:3000/api',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
})
```
#### 相关实例方法
- axios#request(config)
- axios#get(url[, config])
- axios#delete(url[, config])
- axios#head(url[, config])
- axios#options(url[, config])
- axios#post(url[, data[, config]])
- axios#put(url[, data[, config]])
- axios#patch(url[, data[, config]])
注：额外声明的配置将与实例配置合并

#### 请求配置项
在请求配置项中只有url是必填的，默认的请求方法是get
```javascript
const config = {
  // url 请求接口地址
  url: '/user',
  // method 请求方法
  method: 'get',
  // 如url 不是绝对路径，baseURL会和url拼接作为请求的接口地址
  // 用来区分不同环境，建议使用
  baseURL: 'https://demo.com/api',
  // 用于请求之前对请求数据进行操作
  // 只用当请求方法为‘PUT’，‘POST’和‘PATCH’时可用
  // 最后一个函数需return出相应数据
  // 可以修改headers
  transformRequest: [function (data, headers) {
    // 可以对data做任何操作
    return data;
  }],

  // 用于对相应数据进行处理
  // 它会通过then或者catch
  transformResponse: [function (data) {
    // 可以对data做任何操作
    return data;
  }],
  // 请求头部
  headers: {'X-Requested-With': 'XMLHttpReqest'},
  // URL参数(必须是纯对象或url参数对象)
  params: {
    id: 123
  },
  // 是一个可选的函数负责序列化`params`
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // 请求体数据
  // 只有当请求方法为'PUT', 'POST',和'PATCH'时可用
  // 当没有设置`transformRequest`时，必须是以下几种格式
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - Browser only: FormData, File, Blob
  // - Node only: Stream, Buffer
  data: {
    firstName: 'Fred'
  },

  // 请求超时时间（毫秒）
  timeout: 1000,

  // 是否携带cookie信息
  withCredentials: false, // default

  // 统一处理request让测试更加容易
  // 返回一个promise并提供一个可用的response
  // (see lib/adapters/README.md).
  adapter: function (config) {
    /* ... */
  },

  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },
  // 响应格式
  // 可选项 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // 默认值是json

  // `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

  // 处理上传进度事件
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // 处理下载进度事件
  onDownloadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // 设置http响应内容的最大长度
  maxContentLength: 2000,

  // 定义可获得的http响应状态码
  // return true、设置为null或者undefined，promise将resolved,否则将rejected
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // `maxRedirects` defines the maximum number of redirects to follow in node.js.
  // If set to 0, no redirects will be followed.
  // 最大重定向次数？没用过不清楚
  maxRedirects: 5, // default
   httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' defines the hostname and port of the proxy server
  // Use `false` to disable proxies, ignoring environment variables.
  // `auth` indicates that HTTP Basic auth should be used to connect to the proxy, and
  // supplies credentials.
  // This will set an `Proxy-Authorization` header, overwriting any existing
  // `Proxy-Authorization` custom headers you have set using `headers`.
  // 代理
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` specifies a cancel token that can be used to cancel the request
  // (see Cancellation section below for details)
  // 用于取消请求？又是一个不知道怎么用的配置项
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

### 响应组成
```javascript
{
  // 服务端返回的数据
  data: {},
  // 服务端返回的状态码
  status: 200,
  // 服务端返回的状态信息
  statusText: 'OK',
  // 响应头
  headers: {},
  // axios请求配置
  config: {},
  // 请求
  request: {}
}
```

### 默认配置
全局修改axios默认配置
```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```
实例默认配置
```javascript
// 创建实例时修改配置
var instance = axios.create({
  baseURL: 'https://api.example.com'
});

// 实例创建之后修改配置
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

### 配置优先级
配置项通过一定的规则合并，request config > instance.defaults > 系统默认，优先级高的覆盖优先级低的  

```javascript
// 创建一个实例，这时的超时时间为系统默认的 0
var instance = axios.create();
// 通过instance.defaults重新设置超时时间为2.5s，因为优先级比系统默认高
instance.defaults.timeout = 2500;
// 通过request config重新设置超时时间为5s，因为优先级比instance.defaults和系统默认都高
instance.get('/longRequest', {
  timeout: 5000
});
```

## 拦截器
在then和catch之前拦截请求和响应  
```javascript
// 添加一个请求拦截器
axios.interceptors.request.use(config => {
  return config
}, error => {
  return Promise.reject(error)
})

// 添加一个响应拦截器
axios.interceptors.response.use(response => {
  return response
}, error => {
  return Promise.reject(error)
})

// 移除拦截器
var myIntercepttor = axios.interceptors.reqest.user(() => {})
axios.interceptors.request.eject(myIntercepttor)

//为axios实例添加一个拦截器
var instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```
