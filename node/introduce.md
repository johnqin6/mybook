# node.js 介绍

## node是什么？

* node.js 是一个基于 Chrome V8 引擎的 javascript 运行环境（和浏览器一样，用于解析和执行javascript代码）。
    与浏览器javascript的不同处：
    * 浏览器的组成：
        1. ECMAScript
        2. BOM
        3. DOM
    * Node.js中的javascript:
        1. 有ECMAScript，没有BOM、DOM, 所以不能使用BOM,DOM对象，如（window,document都不能使用）
        2. 为javascript提供了一些服务器级别的操作API
            * 例如文件读写
            * 网络服务的构建
            * http服务器
            * 。。。

* node.js 使用了一个事件驱动、非阻塞式I/O的模型(异步)， 使其轻量又高效。

* npm(node.js的包生态系统) 是世界上最大的开源生态系统，绝大多数javascript相关的包都存放在npm上， 目的是方便开发者去下载使用。

## 为什么学习Node.js
* 企业需求
  * 具有服务端开发经验更好
  * front-end (前端)
  * back-end (后端)
  * 全栈工程师
  * 基本的网站开发能力
    * 服务端
    * 前端
    * 运营部署

## Node.js 能做什么？

  * web服务器后台
  * 命令行工具
    * npm(node)
    * git(c语言)
    * hexo(node) 。。。  
    
  对于前端来说，使用node最多的是命令行工具
   * 主要使用第三方的包
   * webpack
   * gulp
   * npm 
  简单的命令行操作
  * cd 进入指定目录，如 cd texts, cd ./texts(进入同级的texts目录) cd ../ (返回上一层目录)
  * dir 与cd相同，进入目录，也能查看目录列表
  * ls  查看目录列表
  * mkdir 创建文件夹
  * rm 删除文件  

## 学习资源
  * 《深入浅出Node.js》 作者：朴灵， 偏理论，偏底层原理，无实战
  * 《Node.js 权威指南》 API讲解，无实战
  * javascript 标准参考教程(alpha): http://javascript.ruanyifeng.com/
  * Node入门：http://www.nodebeginner.org/index-zh-cn.html
  * 官方API文档：https://nodejs.org/dist/latest-v6.x/docs/api/
  * 中文文档（比较旧）：http://www.nodeclass.com/api/node.html
  * cnode社区：http://cnodejs.org
  * cnode-新手入门：http://cnodejs.org/getsetart  

## 学习过程中涉及的知识点

  * B/S编程模型
    * Brower - Server
    * back-end
    * 任何服务器端技术都是B/S编程模型，与语言无关
    * 各种服务器端语言都是学习B/S编程模型的工具
  * 模块化编程
    * Require.js
    * Seajs  @import('文件路径') 用于帮助javascript引用文件
    * Node.js 可以像 @import('文件路径') 用于帮助javascript引用文件
  * Node常用API
  * 异步编程
    * 回调函数
    * Promise
    * async
    * generator函数
  * Express开发框架
  * ECMAScript6
  * Mongodb数据库

