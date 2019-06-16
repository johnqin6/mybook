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
