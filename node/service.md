# 搭建 node 服务器  

## 服务器要干嘛？
  * 提供服务：对 数据的服务
  * 发请求
  * 接受请求
  * 处理请求
  * 发送响应（反馈）  

## Node如何搭建一个简单的服务器

1. 加载 node 的http模块
模块是Node.js 应用程序的基本组成部分，文件和模块是一一对应的。换言之，一个 Node.js 文件就是一个模块，这个文件可能是JavaScript 代码、JSON 或者编译过的C/C++ 扩展。  

> var http = require('http');   
2. 创建一个服务器（server）实例  
> var server = http.createServer();    
3. 监听客户端请求
```javascript
//当客户端请求过来，会自动触发服务器的require请求事件，然后执行回调
// 回调函数接受两个参数 request请求,response响应
// request 请求对象： 用来获取客户端的一些请求信息，例如请求路径
// response 响应对象：用来响应客户端信息

server.on('require', function(request, response) {
    console.log('收到客户端请求');
    console.log('请求路径为：' + request.url);
    //用响应对象(response)的方法：write 向客户端发送响应数据
    //注意：write可使用多次，但需使用end方法结束响应，否则客户端会一直等待
    response.write('hello wrold');
    //也可以直接用end发送请求
    response.end('响应结束');
});
```
4. 绑定端口号，启动服务器  

```javascript
server.listen(3000, function(){
    console.log('服务器已开启');
});
```  
### 补充知识点：端口号    
端口号主要有两种
1. 物理端口
   物理端口指的是物理存在的端口，如ADSL Modem、集线器、交换机、路由器上用 于连接其他网络设备的接口，如RJ-45端口、SC端口等等
2. 逻辑端口
   逻辑端口是指逻辑意义上用于区分服务的端口，如TCP/IP协议中的服务端口，端口号的范围从0到65535，比如用于浏览网页服务的80端口，用于FTP服务的21端口等

5. 根据不同的请求路径发送不同的响应

```javascript
server.on('request', function(req, res) {
    //1. 获取请求路径
    var url = req.url;
    //2. 根据不同请求返回不同的响应
    if(url === '/'){
        res.end('index page');
    }else if(url === '/login'){
        res.end('login page');
    }else{
        res.end('404');
    }
})
``` 
6. 返回json数据
   响应内容只能是二进制和字符串
   解决方法：使用json的parse()和stringify()来进行相互转换

```javascript
server.on('request', function(req, res) {
    var jsonData = [
        {
            'name': '小明',
            'age': 19
        },
        {
            'name': '小张',
            'age': 19
        },
        {
            'name': '小李',
            'age': 19
        }
    ];
    //将json数据转为字符串
    var jsonStr = JSON.stringify(jsonData);
    res.send(jsonStr);
    
});
``` 
6. 乱码
原因：服务器默认发送的数据是 utf8编码的内容
        但是浏览器不知道是utf-8编码的内容
        浏览器在不知道服务器响应的内容编码的情况下，会默认按照当前操作系统的默认编码去解析
解决方法：告诉浏览器发送的内容是什么编码
> res.setHeader('Content-type','text/plain; chartset=utf-8');  
实测：效果不好，因为响应返回的数据使用gzip进行了压缩，来减少体积，加快响应


6. 解决乱码问题  
  1. 处理回显乱码 
  设置返回的html页面的编码格式为utf-8
  > res.write("<head><meta charset='utf-8'></head>");   
  2. 处理传参乱码
  querystring.escape(str) 和querystring.unescape(str)   
  * escape可是传入的字符串进行编码    
  * unescape方法可将含有%的字符串进行解码  

  ``` javascript
    //编码
    var strcode = querystring.escape("name='墨白'");
    var str = querystring.unescape(strcode);

  ```
  3. 与编码相关的代码
  ```javscript
    var encoding = require('encoding');
    var iconv = require('iconv-lite');

    console.log(encoding.convert("%E7%9A%84%E5%A3%AB%E9%80%9F%E9%80%92", "utf-8"));
    console.log(iconv.decode('%E7%9A%84%E5%A3%AB%E9%80%9F%E9%80%92', 'utf-8'));
  ```
 
7. Content-type 
* 不同的资源对应不同的 Content-Type，
* 图片资源不需要指定编码
* http://tool.oschina.net/commons  查询不同资源对应Content-Type 
* 通过网络发送文件
  - 发送的并不是文件，本质上来讲发送的是文件的内容
  - 当浏览器收到服务器响应内容后，就会根据你的Content-Type 进行对应解析
