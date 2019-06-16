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
