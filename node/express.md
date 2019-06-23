# express 

 官网：expressjs.com [传送门](https://expressjs.com)  

## 安装 

> npm install express --save-dev   

## 使用 一个简单实例 

```javascript
const express = require('express');
//创建 app实例
const app = express();

//中间件
app.get('/', (req, res) => {
    res.send('hello express');
});

// 监听端口
app.listen(3000, () => {
    console.log('服务器开启，访问地址：http://localhost:3000');
});

```  
## 修改完代码 自动重启 

使用第三方命令行工具， `nodemon`来帮我们自动重启服务器    
安装：    
> npm install --global nodemon    
使用：    
> nodemon app.js;    

## 静态化文件 
 
- 设置路由标识符的方式静态化文件 
实例：

```javascript
//当以 /public/ 开头时， 去 ./public/ 目录中寻找对应资源
app.use('/public/', express.static('./public'));
// 也可以自定义路由标识, 此时 路由以 /a/ 也能在./public/ 目录中寻找对应资源
app.use('/a/', express.static('./public'));
```

- 省略第一个参数（路由标识符） 

```javascript
// 当省略第一个参数， 则可以通过 省略 /public 的方式访问
app.use(express.static('public'));
```
- 可以静态化多个目录

```javascript
//当以 /public/ 开头时， 去 ./public/ 目录中寻找对应资源
app.use('/public/', express.static('./public'));
// 也可以自定义路由标识, 此时 路由以 /a/ 也能在./public/ 目录中寻找对应资源
app.use('/static/', express.static('./static'));
```  
## express 的get请求如何拿到参数  
可以使用 req.query 拿到前端传的参数  

```javascript
app.get('/getData', (req, res) => {
    let query = req.query;
    res.send(query);
});
```  
## express 的post请求如何拿到参数  
可以使用 req.body 拿到前端传的参数, 但是需要对express配置一下`express.urlencoded({extended:false}`  

```javascript
//配置 express 使post请求可以拿到请求体的数据
app.use(express.urlencoded({extended: false}));
app.post('/postData', (req, res) => {
    let params = req.body;
    res.json(params);
});
``` 

## express 的重定向 res.redirect('重定向的路由') 

```javascript
//配置 express 使post请求可以拿到请求体的数据
app.use(express.urlencoded({extended: false}));
app.post('/postData', (req, res) => {
    let params = req.body;
    //重定向
    res.redirect('/');
});
``` 

## express 中使用session和cookie

express 框架，默认不支持 session和 cookie, 但我们可以使用第三方中间件: express-session 来解决

- 安装
> npm install express-session  
- 引入
> var session = require('express-session')  
- 配置使用
```javascript
var session = require('express-session')
// 配置好后就可以使用 req.session来访问和设置session
app.use(session({
    // 配置加密字符串，她会在原有的加密基础上和这个字符串拼起来加密
    // 目的是为了增加安全性，防止客户端恶意伪造
    secret: 'keyboard cat',
    resave: false,
    saveUninitialized: false   // 无论你是否使用session, 都会默认给你一个分配一把钥匙
}))   

// 使用 添加 session数据
req.session.user = user

// 获得session数据
req.session.user
```
**提示：默认session数据是内存存储的，服务器一旦重启就会丢失，真正的生产环境会把session进行持久化存储**

## express 中间件
- 中间件不关心请求路径和请求方法
- 任何请求都可以进入这个中间件
- 中间件可以有多个
- 中间件本身是一个方法，该方法接受三个三个参数
  + request 请求对象
  + response 响应对象
  + next 下一个中间件  
  + 一个请求进入中间件，如果不调用next(),请求将停留在当前中间件

例子：
```javascript
app.use(function (req, res, next) {
    console.log(111)
    next()  
})

app.use(function (req, res, next) {
    console.log(222)
    next()  
})

// 以 /xxx 开头的路径中间件
app.use('/a', function(req, res, next) {
    console.log(req.url)
})


app.use('/a', function(req, res, next) {
    fs.readyFile('./data/a.txt', function(err) {
        if (err) {
            next(err) //当调用next的时候，如果传递了参数，则将直接往后找到带有四个参数的应用级别的中间件
        }
    })
})
// 错误处理中间件
app.use(function (err, req, res, next) {
  res.status(500).send(err.message)
})
```