# 开发留言本过程中的注意点
 
- 服务器渲染异步请求问题
  浏览器收到HTML 响应内容之后，就开始从上到下依次解析，当解析的过程中，
  如果发现：`link`、`script`、`img`、`iframe`、`video`、`audio`等
  具有src 或者 link 的 href 属性标签的时候，浏览器会自动发起一个新的请求。

  + 将所有的html文件放在views目录中
  + 将所有静态资源放在public 目录中，方便统一处理  

## 结合express 和 art-template 开发留言本 
- 安装 express, art-template, express-art-template 三个依赖， 
> npm install --save art-template    
> npm install --save express-art-template    

实例：
```javascript
const express = require('express');
const app = new express(); 
// 配置
app.engine('html', require('express-art-template'));
//静态化文件夹
app.use('/public/', express.static('./public'));
// 可以修改默认的 views 视图渲染存储目录
app.set('views', 'views');

app.get('/', (req, res) => {
    //使用
    res.render('demo.html', {
        name: 'john'
    });
});

app.listen(3000, () => {
    console.log('服务器开启, 访问地址：http://localhost:3000');
});
```