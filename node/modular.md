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

### 导出多个成员(必须在对象中)
```javascript
exports.a = 10;
exports.b = 'hello';
```
```javascript
var a = 10;
var b = 'hello';
function foo(val) {
  console.log(val);
}
module.exports = {
  a: a,
  b: b,
  foo: foo
}
``` 
### 导出单个成员 
```javascript
let a = 10;
module.exports = a;
```
### module.exports 和exports的区别

在Node中，每一个模块内部中都有一个自己的 module对象，该对象有一个成员叫：exports 也是一个对象，
需要导出成员时，就将导出成员挂载在module.exports上。
exports 是 module.exports的一个引用

## require 的加载规则 

- 优先从缓存加载
- 判断模块标识  
>  require('模块标识符');   
  + 路径形式的模块 （多为自定义模块） 如 ./ , ../ 的相对路径  
  + 核心模块的本质也是文件
    * 核心模块已经被编辑到二进制文件中，只要加载名称即可 
  + 第三方模块 
    * npm 下载，使用时就可以通过 require('包名') 来加载    
    * 寻找规则(过程)：先寻找当前目录的 node_modules 目录 
      --》没有，在依次向上寻找    
      --》找到后，在向 node_modules 目录中寻找对应模块目录的 package.json文件   
      --》在 package.json 文件中的 main 属性中， main 属性记录了 该模块的入口模块    
      --》最后加载使用这个第三方包      
      --》当没有 package.json 不存在，或 main属性没有指定入口文件，    
      --》node 会自动寻找该目录的index.js文件  
      --》如果一次查找到此盘根目录还找不到，就会报错，Can not find moudle xxx    
    * 实际上加载的还是文件   

## 什么是模块化   
模块化是一种处理复杂系统分解为更好的可管理模块的方式。   
简单地说，一个功能就是一个模块。  
具有的特点：
- 文件作用域
- 通信规则    

## npm 包管理器 
1. npm 官方网站 [传送门](https://npmjs.com)  
2. npm 命令行工具  
  - 常用命令 
    + 查看 npm 版本
    > npm --version  
    + 升级npm  
    > npm install --global npm  
    + npm 初始化 快速生成 package.json 文件
    > npm init   
    + npm 安装依赖 
      * 一次性将 dependencies 选项中的依赖全安装
      > npm install  或 npm i   
      * 只安装指定依赖  
      > npm install 包名   
      * 安装依赖并保存依赖到 dependencies 选项 项目依赖
      > npm install 包名 --save 
      * 安装依赖并保存依赖到 devdependencies 选项 开发依赖
      > npm install 包名 --save-dev 
    + 删除依赖
    > npm uninstall 包名 --save 或 npm remove 包名   

  - 安装淘宝镜像 cnpm 
  > npm install --global cnpm   

### package.json 包说明文件 
每一个项目都要有一个 package.json 文件