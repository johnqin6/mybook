# 代码风格 

## 社区规定的两种代码风格规范

- [javascript standard Style](https://standardjs.com/) 
- [Airbnb Javascript Style](https://www.jianshu.com/p/87c10daab4f4) 

## 当你采用无分号的代码风格，只需要注意以下情况就不会出问题
 当代码以`(`,`[`,'`'开头是，需要在前边补加分号，避免代码报错  
 ```javascript
;(function(){
    console.log('hello')
})
 ```