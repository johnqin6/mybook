# JavaScript standard 代码规范  

1. **使用两个空格**进行缩进 
```javascript
function hello (name) {
  console.log('hi', name);
}
```  
2. 储蓄要专一的情况外，**字符串统一使用单引号**
```javascript
console.log('hello world');
```  
3. 不要定义未使用的变量 
```javascript
function myFunction () {
  var result = something(); // x avoid
}
``` 
4. 关键字后面加空格
```javascript
if (condition) {}
``` 
5. 函数声明时括号与函数名间加空格
```javascript
function name (arg) {}
``` 
6. 始终使用===替代== 
例外：obj == null 可以用来检查null || underfined.
```javascript
if (name === 'john') {}
if (name !== 'tom') {}
``` 
7. 字符串拼接操作符之间要留空格
```javascript
var x = 2; 
var message = 'hello' + name + '!';
``` 
8. 逗号之间加空格
```javascript
var list = [1, 2, 3, 4];
``` 
9. else关键字要与花括号保持在同一行
```javascript
if (condition) {

} else {

}
``` 
10. 多行if语句的括号不能省
```javascript
if (options.quiet !== true) console.log('done');
if (options.quiet !== true) {
    console.log('done');
}
``` 
11. 不要丢掉异常处理中的err参数
```javascript
run(function (err) {
    if (err) throe err;
    window.alert('done');
})
``` 
12. 使用浏览器全局变量是加上`window.`前缀
```javascript
window.alert('hi')
``` 
13. 不允许有连续多行空行
```javascript
var value = 'hello world'
console.log(value)
``` 
14. 对于三元运算符？和：与他们负责的代码处于同一行
```javascript
var location = env.development ? 'localhost' : 'www.api.com'

var location = env.development
    ? 'localhost'
    : 'www.api.com'
``` 
15. 每个var关键字单独声明一个变量
```javascript
// ✓ ok
var silent = true;
var verbose = true;

// ✗ avoid
var silent = true, verbose = true
``` 
16. 条件语句中赋值语句是用括号包起来，这样使得代码更加清晰可读，而不会认为是将条件判断语句的全等号（===）错写成了等号（=）
```javascript
if ((m = text.match(expr))) {

}
``` 
17. 单行代码块两边加空格
```javascript
function foo () { retun true }
``` 
18. 对于变量和函数名统一使用驼峰命名法
```javascript
var myVar = 'hello'
function myFunction () {}
``` 
19. 不允许有多余的行末逗号，始终将逗号置于行末
```javascript
var obj = {
    foo: 'foo',
    bar: 'bar'
}
``` 
20. 点号操作符与属性需在同一行
```javascript
var value = 'hello world'
console.log(value)
``` 
21. 文件末尾留一空行 
22. 函数调用时标识符与括号间不留间隔
```javascript
console.log(value)
``` 
23. 键值对当中冒号与值之间要留空白
```javascript
var obj = {
    foo: 'foo',
    bar: 'bar'
}
``` 
24. 构造函数要以大写字母开头
```javascript
function Animal () {}
var dog = new Animal()
``` 
25. 无参的构造函数调用时要带上括号
```javascript
function Animal () {}
var dog = new Animal()
``` 
26. 对象中定义了存值器，一定要对应的定义取值器
```javascript
var person = {
    set name (value) {
        this._name = value
    },
    get name () {
        return this._name
    }
}
``` 
27. 子类的构造器中一定要调用 super
```javascript
class Dog extends Namal {
    constructor () {
        super()
    }
}
``` 
28. 使用数组字面量不要使用构造器
```javascript
var nums = new Array(1, 2, 3); // ✗ avoid
var nums = [1, 2, 3];  //✓ ok
``` 
29. 避免使用arguments.callee和arguments.caller。
```javascript
function foo (n) {
  if (n <= 0) return
 
  arguments.callee(n - 1)   // ✗ avoid
}
function foo (n) {
  if (n <= 0) return 
  foo (n - 1)
}
``` 
30. 避免对类名重新赋值
```javascript
class Dog {}
Dog = 'ff' // ✗ avoid
``` 
31. 避免修改使用const声明的变量
```javascript
const score = 100
score = 125  
```
32. 避免使用常量作为条件表达式的条件（循环语句除外）。
```javascript
if (false) {    // ✗ avoid
  // ...
}
 
if (x === 0) {  // ✓ ok
  // ...
}
 
while (true) {  // ✓ ok
  // ...
} 
``` 
33. 正则中不要使用控制符
```javascript
var pattern = /\xlf/ // ✗ avoid
var pattern = /\x20/ // ✓ ok
```
34. 不要使用 debugger
```javascript
function sum (a, b) {
  debugger      // ✗ avoid
  return a + b
}
```
35. 不要对变量使用delete操作
```javascript
var name
delete name
```
36. 不要定义冗余的函数参数。
```javascript
function sum (a, b, a) {  // ✗ avoid
  // ...
}
 
function sum (a, b, c) {  // ✓ ok
  // ...
}
```  
37. 类中不要定义冗余的属性
```javascript
class Dog {
  bark () {}
  bark () {}  // ✗ avoid
}
``` 
38. 对象字面量中不要定义重复的属性
```javascript
var user = {
  name: 'john',
  name: 'helo'  // ✗ avoid
}
``` 
39. switch 语句中不要定义重复的case分支
```javascript
switch (id) {
  case 1:
  case 1:  // ✗ avoid
}
``` 
40. 同一个模块有多个导入时一次性写完
```javascript
import { myFun1, myFun2 } from 'module'
``` 
41. 正则中不要使用空字符
```javascript
const myRegex = /^abc[]/      // ✗ avoid
const myRegex = /^abc[a-z]/   // ✓ ok
``` 
42. 不要解构空值
```javascript
const { a: {} } = foo    // ✗ avoid
const { a: { b } } = foo   // ✓ ok
``` 
43. 不要使用eval() 
```javascript
eval("var result = user." + propName) // ✗ avoid
var result = user[propName]   // ✓ ok
```
44. catch中不要对错误重新赋值
```javascript
try {
  // ...
} catch (e) {
  e = 'new value'             // ✗ avoid
} 
try {
  // ...
} catch (e) {
  const newVal = 'new value'  // ✓ ok
}
```
45. 不要扩展原生对象
```javascript
Object.prototype.age = 21   // ✗ avoid
``` 
46. 避免多余的函数上下文绑定
```javascript
const name = function () {
  getName()
}.bind(user)  // ✗ avoid

const name = function () {
  this.getName()
}.bind(user) // ✓ ok
```  
47. 避免不必要的布尔转换
```javascript
const result = true 
if (!!result) {} // ✗ avoid

const result = true 
if (result) {} // ✓ ok
```  
48. 不要使用多余的括号包裹函数
```javascript
const myfunc = (function () {}) // ✗ avoid

const myFunc = function () {} // ✓ ok
```  
49. switch 一定要使用break 来将条件分支正常中断
```javascript
switch (filter) {
  case 1:
    doSomething()    // ✗ avoid
  case 2:
    doSomethingElse()
}
 
switch (filter) {
  case 1:
    doSomething()
    break           // ✓ ok
  case 2:
    doSomethingElse()
}
 
switch (filter) {
  case 1:
    doSomething()
    // fallthrough  // ✓ ok
  case 2:
    doSomethingElse()
}
```  
50. 不要省去小数点前面的0
```javascript
const discount = .5      // ✗ avoid
const discount = 0.5     // ✓ ok
```
51. 避免对声明过的函数重新赋值
```javascript
function myFunc () {}     
myFunc = myOtherFunc   // ✗ avoid
```
52. 不要对全局只读对象重新赋值
```javascript
window = {}   // ✗ avoid
```
53. 注意隐式的eval() 
```javascript
setTimeout("alert('hello world')")      // ✗ avoid
setTimeout(function () { alert('hello wrold') })    // ✓ ok
```
54. 嵌套的代码块中禁止在定义函数
```javascript
if (authent) {
  function setAuthent() {}
}      // ✗ avoid
```
55. 不要向RegExp构造函数传入非法的正则表达式
```javascript
RegExp('[a-z')    // ✗ avoid
RegExp('[a-z]')   // ✓ ok
```
56. 不要使用非法的空白符
```javascript
function myFunc () /*<NBSP>*/{}   // ✗ avoid
```
57. 禁止使用 __iterator__。
```javascript
Foo.prototype.__iterator__ = function () {}   // ✗ avoid
```
58. 外部变量不要与对象属性重名。
```javascript
var score = 100
function game () {
  score: while (true) {      // ✗ avoid
    score -= 10
    if (score > 0) continue score
    break
  }
}
```
59. 不要使用标签语句
```javascript
label:
  while (true) {
    break label     // ✗ avoid
  }
``` 
60. 不要混合使用空格与制表符作为缩进
61. 除了缩进，不要使用多个空格。
62. 不要使用多行字符串 
```javascript
const message = 'Hello \
                 world'     // ✗ avoid
``` 
63. new构造对象实例后需要赋值给变量
```javascript
new Character()                     // ✗ avoid
const character = new Character()   // ✓ ok
``` 
64. 禁止使用 Function 构造器。
```javascript
var sum = new Function('a', 'b', 'return a + b')    // ✗ avoid
``` 
64. 禁止使用 Object  构造器。
```javascript
let config = new Object()   // ✗ avoid
``` 
65. 禁止使用 new require。
```javascript
const myModule = new require('my-module')    // ✗ avoid
``` 
66. 禁止使用 Symbol 构造器。
```javascript
const foo = new Symbol('foo')   // ✗ avoid
``` 
67. 禁止使用原始包装器。
```javascript
const message = new String('hello')   // ✗ avoid
``` 
68. 不要将全局对象的属性作为函数调用。
```javascript
const math = Math()   // ✗ avoid
``` 
69. 不要使用八进制字面量
```javascript
const num = 042     // ✗ avoid
const num = '042'   // ✓ ok
``` 
70. 字符串字面量中也不要使用八进制转义字符。
```javascript
const copyright = 'Copyright \251'  // ✗ avoid
``` 
71. 使用__dirname和__filename时尽量避免使用字符串拼接
```javascript
const pathToFile = __dirname + '/app.js'  // ✗ avoid
const pathToFile = path.join(__dirname, 'app.js') // ✓ ok
``` 
72. 使用getPrototypeOf来替代__proto__
```javascript
const foo = obj.__proto__               // ✗ avoid
const foo = Object.getPrototypeOf(obj)  // ✓ ok
``` 
73. 不要重复声明变量。
74. 正则中避免使用多个空格。
```javascript
const regexp = /test   value/   // ✗ avoid
 
const regexp = /test {3}value/  // ✓ ok
const regexp = /test value/     // ✓ ok
``` 
75 return 语句中的赋值必须有括号包裹
```javascript
function sum (a, b) {
  return result = a + b;
}   // ✗ avoid
 
function sum (a, b) {
  return (result = a + b)
}  // ✓ ok
``` 
76. 避免将变量赋值给自己。
```javascript
name = name   // ✗ avoid
``` 
77. 避免将变量与自己进行比较操作。
```javascript
if (score === score) {}   // ✗ avoid
``` 
78. 避免使用逗号操作符
```javascript
if (doSomething(), !!test) {}   // ✗ avoid
``` 
79. 不要随意更改关键字的值。
```javascript
let undefined = 'value'     // ✗ avoid
``` 
80. 禁止使用稀疏数组（Sparse arrays）
```javascript
let fruits = ['apple',, 'orange']       // ✗ avoid
``` 
81. 不要使用制表符
82. 正确使用 ES6 中的字符串模板
```javascript
const message = `Hello ${name}`   // ✓ ok
``` 
83. 使用this前请确定super()已调用
```javascript
class Dog extends Animal {
  constructor () {
    this.legs = 4     // ✗ avoid
    super()
  }
}
```  
84. 用 throw 抛错时，抛出 Error 对象而不是字符串
```javascript
throw 'error'               // ✗ avoid
throw new Error('error')    // ✓ ok
```  
85. 行末不留空格。
86. 不要使用 undefined 来初始化变量。
```javascript
let name = undefined    // ✗ avoid
 
let name
name = 'value'          // ✓ ok
```  
87. 循环语句中注意更新循环变量。
88. 如果有更好的方式，尽量不要使用三元表达式
```javascript
let score = val ? val : 0     // ✗ avoid
let score = val || 0          // ✓ ok
```  
89. return，throw，continue 和 break 后不要再跟代码
90. finally 代码块中不要再改变程序执行流程
```javascript
try {
  // ...
} catch (e) {
  // ...
} finally {
  return 42     // ✗ avoid
}
```  
91. 关系运算符的左值不要做取反操作
```javascript
if (!key in obj) {}       // ✗ avoid
``` 
92. 避免不必要的 .call() 和 .apply()
```javascript
sum.call(null, 1, 2, 3)   // ✗ avoid
``` 
93. 避免使用不必要的计算值作对象属性
```javascript
const user = { ['name']: 'John Doe' }   // ✗ avoid
const user = { name: 'John Doe' }       // ✓ ok
``` 
94. 禁止多余的构造器
```javascript
class Car {
  constructor () {      // ✗ avoid
  }
}
``` 
95. 禁止不必要的转义
```javascript
let message = 'Hell\o'  // ✗ avoid
``` 
96. import, export 和解构操作中，禁止赋值到同名变量
```javascript
import { config as config } from './config'     // ✗ avoid
import { config } from './config'
``` 
97. 属性前面不要加空格
```javascript
user .name      // ✗ avoid
user.name       // ✓ ok
```
98. 禁止使用 with
99. 对象属性换行时注意统一代码风格
```javascript
const user = {
  name: 'Jane Doe', age: 30,
  username: 'jdoe86'            // ✗ avoid
}
 
const user = { name: 'Jane Doe', age: 30, username: 'jdoe86' }    // ✓ ok
 
const user = {
  name: 'Jane Doe',
  age: 30,
  username: 'jdoe86'
}      
```
100. 代码块中避免多余留白
101. 展开运算符与它的表达式间不要留空白
```javascript
fn(... args)    // ✗ avoid
fn(...args)     // ✓ ok
```
102. 遇到分号时空格要后留前不留
```javascript
for (let i = 0 ;i < items.length ;i++) {...}    // ✗ avoid
for (let i = 0; i < items.length; i++) {...}    // ✓ ok
```
103. 代码块首尾留空格
```javascript
if (admin){...}     // ✗ avoid
if (admin) {...}    // ✓ ok
```
104. 圆括号间不留空格
```javascript
getName( name )     // ✗ avoid
getName(name)       // ✓ ok
```
105. 一元运算符后面跟一个空格
```javascript
typeof!admin        // ✗ avoid
typeof !admin        // ✓ ok
```
106. 注释首尾留空格
```javascript
//comment           // ✗ avoid
// comment          // ✓ ok
 
/*comment*/         // ✗ avoid
/* comment */       // ✓ ok
```
107. 模板字符串中变量前后不加空格
```javascript
const message = `Hello, ${ name }`    // ✗ avoid
const message = `Hello, ${name}`      // ✓ ok
```
108. 检查 NaN 的正确姿势是使用 isNaN()
```javascript
if (price === NaN) { }      // ✗ avoid
if (isNaN(price)) { }       // ✓ ok
```
109. 用合法的字符串跟 typeof 进行比较操作
```javascript
typeof name === 'undefimed'     // ✗ avoid
typeof name === 'undefined'     // ✓ ok
```
110. 自调用匿名函数 (IIFEs) 使用括号包裹
```javascript
const getName = function () { }()     // ✗ avoid
 
const getName = (function () { }())   // ✓ ok
const getName = (function () { })()   // ✓ ok
```
111. yield * 中的 * 前后都要有空格
```javascript
yield * increment()
```
112. 请书写优雅的条件语句（avoid Yoda conditions）
```javascript
if (42 === age) { }    // ✗ avoid
if (age === 42) { }    // ✓ ok
``` 
113. 不要使用分号
```javascript
window.alert('hi')   // ✓ ok
window.alert('hi');  // ✗ avoid
``` 
114. 不要使用 (, [, or ` 等作为一行的开始。在没有分号的情况下代码压缩后会导致报错，而坚持这一规范则可避免出错
```javascript
// ✓ ok
;(function () {
  window.alert('ok')
}())
 
// ✗ avoid
(function () {
  window.alert('ok')
}())
// ✓ ok
;[1, 2, 3].forEach(bar)
 
// ✗ avoid
[1, 2, 3].forEach(bar)
// ✓ ok
;`hello`.indexOf('o')
 
// ✗ avoid
`hello`.indexOf('o')
``` 