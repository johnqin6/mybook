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

## 封装ajax 

```javascript
/**
 * get请求
 * @param {请求地址} url 
 * @param {请求参数} data
 * @param {回调函数} callback 
 */
function get (url, data, callback) {
    // 创建异步对象
    let xhr = new XMLHttpRequest()
    // 判断是否有参
    if(data) {
        url += '?';
        for (let key in data) {
            url += key + "=" + data[key] + '&'
        }
        url = url.slice(0, -1)
    }
    // 设置请求的url参数，参1：请求类型，参2：请求地址
    xhr.open('get', url)
    // 发送请求
    xhr.send()
    // 注册事件，监听请求状态
    xhr.onreadystartchange = function () {
        if(xhr.readyState == 4 && xhr.status == 200) {
            console.log(xhr.responseText)
            // 返回响应的内容 
            callback(xhr.responseText)
        }
    }
}

get('/', function (data) {
    console.log(data)
})

/**
 * post 请求
 * @param {*} url 
 * @param {*} data 
 * @param {*} callback 
 */
function post (url, data, callback) {
  // 创建异步对象
  let xhr = new XMLHttpRequest()
  // 设置请求的url参数，参1：请求类型，参2：请求地址
  xhr.open('post', url)
  // 设置请求头部
  xhr.setRequsetHeader('Content-type', "application/x-www-form-urlencoded")
  let paramStr = ''
  if(data) {
      for (let key in data) {
          paramStr += key + '=' + data[key] + '&'
      }
      paramStr = paramStr.slice(0, -1)
  }
  // 发送请求
  xhr.send(paramStr)
  // 监听请求事件
  xhr.onreadystartchange = function () {
      if(xhr.readyState == 4 && xhr.status == 200) {
          callback(xhr.responseText)
      }
  }
}

```