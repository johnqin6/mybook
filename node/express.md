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
