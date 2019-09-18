# 过滤器filters

Vue过滤器本质上是一个函数，他接受一个值，并处理它，然后返回处理过的值。    
过滤器可以用在两个地方：双花括号插值和v-bind表达式. 过滤器应改被添加在js表达式
的尾部，由“管道(|)”符号指示

```html
<!--双花括号插值-->
{{ message | capitalize}}
<!--v-bind表达式 -->
<div v-bind:id="rawId | formatId"></div>
```
- 局部过滤器  

```javascript
export default {
  filters: {
    capitalize (val) { // 英文单词首字母大写
      if (!val) return ;
      val = val.toString();
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
}
```
- 全局过滤器  

```javascript
// src/filters/myfilter.js
import Vue from 'vue'

Vue.filter('capitalize', val => {
  if (!val) return ;
  val = val.toString();
  return value.charAt(0).toUpperCase() + value.slice(1)
})

// main.js
import '@/filters/myFilter'
```

过滤器函数总接收表达式的值 (之前的操作链的结果) 作为第一个参数。在上述例子中，capitalize 过滤器函数将会收到 message 的值作为第一个参数。

过滤器可以串联："\{\{ message | filterA | filterB \}\}"

在这个例子中，filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中。

过滤器是 JavaScript 函数，因此可以接收参数：'\{\{ message | filterA('arg1', arg2) \}\}'

这里，filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数。
