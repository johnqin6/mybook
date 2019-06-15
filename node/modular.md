# Node 中模块系统 

## node 中的javascript
  * ECMAScript (没有DOM,BOM)
  * 核心模块
  * 第三方模块
  * 用户自定义模块

## 核心模块
Node为javascript提供了很多服务器级别的API，这些API大多数都被包装到一个具名的核心模块中了  
例如文件操作的`fs`核心模块，http服务器构建的`http`模块，`path`路径模块，`os`操作系统模块。。。

只要是模块就需要引入 
```javascript
var fs = require('fs');
```  

## 用户自定义模块
  * require （方法） 引入
    作用：加载模块(包括核心模块，第三方模块，自定义模块)；
          拿到被加载的文件模块导出(exports)的接口对象     
    注意：自定义模块，必须使用相对路径，如同级需加 ./
  * exports（方法）  暴露   
    作用：将模块中东西暴露出去,例如变量，函数等，让其他文件能访问，调用   
  * Node 中没有全局作用域, 只有模块（文件）作用域 
    模块是封闭的，外部访问不到内部，内部也不能访问到外部  
    * 好处： 避免了变量命名冲突污染
    * 但某些情况下，模块之间也需要通信，可以将需要通信的变量或方法手动挂载在`exports`接口对象上
    * `exports`是一个空对象
    * 使用`require`引入模块内部的接口对象

  实例
  1. 创建一个a.js文件
  ```javascript
     console.log('hello a.js');
     function foo(a) {
         console.log(a);
     }
     //将foo函数暴露出去
     exports.foo = foo;
  ``` 
  2. 创建b.js文件并引入a.js文件
  ```javascript
     console.log('hello');
     var a = require('./a.js');
     console.log('end');
     //执行a.js文件的foo函数
     a.foo('你好！');
  ``` 

