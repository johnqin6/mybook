# 实现Apache功能 

1. 创建http服务器 
```javascript
var http = require('http');

//创建一个服务器实例
var server = http.createServer();

//监听请求
server.on('request', function(req, res){

});

//绑定端口，开启服务器
server.listen(3000, function(){
    console.log('服务器开启，访问http://localhost:3000');
});
```
2. 创建www文件夹保存静态文件
> mkdir www    
 
3. 获取当前文件夹的路径和拼接路径
  - 获取当前文件夹的路径的方法有三种
    1. __dirname：返回运行文件所在的目录
    2. resolve('./')：当前命令所在的目录
    3. process.cwd()：当前命令所在的目录  
  - 拼接路径的方法 `path.join()`
    使用平台特定的分隔符(/)作为定界符将所有给定的 path 片段连接在一起，然后规范化生成的路径
实例：
```javascript
//引入path 模块
var path = require('path');
var pathname = __dirname;
var pathname1 = path.resolve('./');
var pathname2 = process.cwd();

var pathName = path.join(pathname,'/www/index.html');
``` 
4. 实现apache 静态文件夹功能
   即输入`www`下指定路径返回相应的页面
```javascript
var http = require('http');
var path = require('path');
var fs = require('fs');

//创建一个服务器实例
var server = http.createServer();

//监听请求
server.on('request', function(req, res){
    var url = req.url;
    //获取当前文件路径 
    var pathname = path.resolve(__dirname);
    //拼接路径
    var filePath = path.join(pathname,'/www', url);
    if(url === '/') {
        filePath = path.join(pathname, '/www/index.html');
    }
    fs.readFile(filePath, function(err, data) {
        if(err) {
            res.end('404');
        }
        res.end(data);
    });
});

//绑定端口，开启服务器
server.listen(3000, function(){
    console.log('服务器开启，访问http://localhost:3000');
});
```  
5. 实现目录列表
  1. 得到 www 文件夹下的文件夹名和文件名
     fs.readdir  
  2. 将得到的文件名和目录名替换到 index.html 中
     模板引擎  
