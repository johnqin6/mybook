# Node.js 安装起步  

## 安装
  * 查看当前Node环境的版本号
  * 下载：https://nodejs.org/en/download/
  * 安装
  * 确定Node环境是否安装成功，查看版本号：
    > node --version     
  * 环境变量   

## Node.js 执行脚本代码

  * 新建一个文件, 进入一个文件夹    
  > type nul>test.js     
  * 在test.js 写入 
  ```javascript
    var foo = 'hell world';
    console.log(foo);
  ```
  * 在cmd命令行输入  
  > node test.js  

## Node.js 的文件操作
  * 浏览器javascript不能进行文件操作，Node.js 提供了文件操作的API
  * Node 中使用fs这个核心模块来操作文件系统
  * 使用fs 模块需事先用require引入fs模块  
  ```javascript
    var fs = require('fs');
    /***
     * 读取文件
     * param url 文件路径
     * param 回调函数 
     * error : 读取成功 -》error 就是错误对象，否则为null
     * data: 读取成功就是读到的数据，否则为null
     */
    fs.readFile('./test.txt', function(data){
        if(err) throw err;
        console.log(data.toString());  // toString() 将二进制转为utf-8字符
    });

    /**
    * params1: url 文件路径
    * params2: 写入的信息
    * param 回调函数 
    */
    fs.writeFile('./test.txt','大家哈，写一个信息了', function(err){
        console.log(err,'文件写入成功');
    });
  ```    


