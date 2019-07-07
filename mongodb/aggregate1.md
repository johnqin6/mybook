# aggregate聚合管道续(表达式操作符) 

## 表达式操作符  
表达式操作符主要用于在管道中构建表达式时使用，使用类似于函数那样需要参数，主要用于$project操作符中，用于构建表达式，使用方法一般如下：

方法1：    
> { `<operator>: [ <argument1>, <argument2> ... ]` }     

方法2：    
> { `<operator>: <argument>` }    

### 表达式操作符分类   
+ 布尔值操作符（Boolean Operators） 
+ 集合操作符（Set Operators）
+ 比较操作符（Comparison Operators）
+ 数字操作符（Arithmetic Operators）
+ 字符串操作符（String Operators）
+ 文本搜索操作符（Text Search Operators）
+ 数组操作符（Array Operators） 
+ 变量操作符（Variable Operators）
+ 字面量操作符（Literal Operators）
+ 数字操作符（Arithmetic Operators）
+ 日期操作符（Date Operators）
+ 条件操作符（Conditional Operators）
+ 数据类型操作符（Data Type Operators）  

### 常用表达式操作符  

- 布尔值操作符   
|操作符| 简述 |     
|----|--------|    
|$and| 逻辑与操作符，用法：`{ $and: [ <expression1>, <expression2>,...]}`|      
|$or| 逻辑或操作符，用法：`{ $or: [ <expression1>, <expression2>,...]}`|    
|$not| 取反操作符，用法：`{ $not: [ <expression>]}` |     

示例： 

|例子| 结果|   
|-----|----|   
| `{$and: [1, "green"]}`| true|   
| `{$and: []}`| true|    
| `{$and: [[null], [false],[0]]}`| true|   
| `{$and: [null, true]}`| false|   
| `{$and: [0, true]}`| false|    
| `{$or: [true, false]}`| true|    
| `{$or: [[false], false]}`| true|   
| `{$or: [null, 0, undefined]}`| false|   
| `{$or: []}`| false|   
| `{$not: [true]}`| false|   
| `{$not: [[false]]}`| false|  
| `{$not: [false]}`| true|   
| `{$not: [null]}`| true|  
| `{$not: [0]}`| true|    

#### 比较操作符  

|操作符| 简述|    
|-----|------|   
|$cmp| 比较操作符，比较表达式中两个值的大小，如果第一个值小于第二个值则返回-1，相等返回0，大于返回1。用法`{ $cmp: [ <expression1>, <expression2> ] }`|      
|$eq| 比较是否相等，用法:`{ $eq: [ <expression1>, <expression2> ] }`|      
|$gt| 大于,用法: `{ $gt: [ <expression1>, <expression2>]}`|    
|$gte| 大于等于,用法: `{ $gte: [ <expression1>, <expression2>]}`|   
|$lt| 小于,用法: `{ $lt: [ <expression1>, <expression2>]}`|    
|$lte| 大于等于,用法: `{ $lte: [ <expression1>, <expression2>]}`|    
|$ne| 不等于,用法: `{ $ne: [ <expression1>, <expression2>]}`|    

示例：   
```javascript
db.collection.aggreagte([
  {
    $project: {
      name: 1,
      score: 1,
      cmp60: { $cmp: ['$score', 60] }, // 比较是否大于60
      eq100: { $eq: ['$score', 100] },  // 是否等于100
      gt80: { $gt: ['$score', 80] },   // 是否大于80
      gte80: { $gte: ['$score', 80] },  // 是否大于等于80
      lt80: { $lt: ['$score', 80] },    // 是否小于80
      lte80: { $lte: ['$score', 80] },  // 是否小于等于80
      ne100: { $ne: ['$score', 100] },  // 是否不等于100
      _id: 0
    }
  }
])
```

#### 数学操作符  

|操作符| 简述|   
|-----|-------|     
|$abs| 求绝对值操作符，用法：`{ $abs: <number>} `|   
|$add| 求和操作符，用法：`{ $add: [ <express1>, <express2>]} `|     
|$ceil| 进一取整操作符，用法：`{ $ceil: <number>} `|     
|$divide| 求商操作符，用法：`{ $divide: [ <express1>, <express2>]} `|     
|$subtract| 求差操作符，用法：`{ $subtract: [ <express1>, <express2>]} `|     
|$multiply| 求积操作符，用法：`{ $multiply: [ <express1>, <express2>]} `|     
|$mod| 求余操作符，用法：`{ $mod: [ <express1>, <express2>]} `|     

示例：  

|例子| 结果|  
|----| ----|   
|`{ $abs: -1 }`| 1|  
|`{ $abs: 1 }`| 1|  
|`{ $abs: null }`| null|   
|`{ $ceil: 1 }`| 1|    
|`{ $ceil: 7.80 }`| 8|   
|`{ $ceil: -2.8 }`| -2| 
|`{ $add: [1, 1] }`| 2|  
|`{ $subtract: [10, 8] }`| 2|       
|`{ $multiply: [2, 5] }`| 10|   
|`{ $divide: [40, 8] }`| 5|  
|`{ $mob: [80, 7] }`| 3|  
|`{ $mob: [80, 8] }`| 0|    

**注意：$add将一个日期类型和数字类型相加会变成日期类型。 这样的话，当数据库存储的是时间戳但是需要又想对其使用日期操作符的话，就可以通过这样的方法，先让其变成日期类型，然后再使用日期操作符，用法参考：`{ $add: [ new Date(0), '$ts' ] }`**   

#### 字符串操作符  

|操作符| 简述 |  
|-----|-------|   
|$concat| 连接操作符，用法：`{ $concat: [ <expression1>, <expression2> ]}`|  
|$split| 切割操作符，用法：`{ $split: [ <string expression>, <delimiter> ]}`|  
|$toLower| 转小写，用法：`{ $toLower: <expression> }`|  
|$toUpper| 字符串转大写，用法：`{ $toUpper: <expression> ]}`|  
|$substr| 返回子字符串，v3.4+不建议使用，建议使用substrCP, 因为v3.4版本+ 使用相当于substrBytes, 用法：`{ $substr: [ <string>, <start>,<length> ]}`|     
|$substrBytes| 用于根据UTF-8下的字节位置返回子字符串（起始位置为0），用法：`{ $substrBytes: [ <string expression>, <byte index> , <byte count>]}`|    
|$substrCP| 用于根据UTF-8下的Code Point位置返回子字符串（起始位置为0），用法：`{ $substrCP: [ <string expression>, <code point index>,<code point count> ]}`|    

示例：  

|例子| 结果|  
|----| ----|   
|`{ $concat: ['item', '-', 'a'] }`| item-a |  
|`{ $split: ['june-15-2013', '-'] }`| ['june', '15', '2013'] |  
|`{ $split: ['banana split', 'a'] }`| [ "b", "n", "n", " split" ]|   
|`{ $toLower: 'ITEM' }`| 'item'|    
|`{ $toUpper: 'item' }`| 'ITEM'|   
|`{ $toUpper: null }`| ''|     
|`{ $substrBytes: ['abcde', 1, 2] }`| 'bc'|    
|`{ $substrBytes: ['汉字hanzi', 0, 3] }`| '汉'|       
|`{ $substrCP: ['abcde', 1, 2] }`| 'bc'|   
|`{ $substrCP: ['汉字hanzi', 0, 3] }`| '汉字h'|    

#### 日期操作符   

|操作符| 简述 |  
|-----|-------|   
|$dayOfYear| 返回一年中的一天，值在1和366（闰年）之间,用法：`{ $dayOfYear: <expression> }`|  
|$dayOfMonth| 返回一月中的一天，值在1和31之间,，用法：`{ $dayOfMonth: <expression> }`|  
|$dayOfWeek| 返回一周中的一天，值在1和7之间,，用法：`{ $dayOfWeek: <expression> }` |  
|$year| 返回年份， 用法：`{ $year: <expression> }` |     
|$month| 返回月份，1~12， 用法：`{ $month: <expression> }` |     
|$week| 返回周，0~53， 用法：`{ $week: <expression> }` |  
|$dateToString| 返回日期的字符串, 用法：`{ $dateTostring: { format: <formatString>, date: <dateExpression>}}`|    
|$hour| 返回时，0-23 用法：`{ $hour: <expression> }` |  
|$minute| 返回分，0-59 用法：`{ $minute: <expression> }` |    
|$second| 返回秒，0-59 用法：`{ $second: <expression> }` |  
|$millisecond| 返回毫秒，0-999 用法：`{ $millisecond: <expression> }` |   
  
例子：  

```javascript
// 数据  
{ "_id" : 1, "item" : "abc", "price" : 10, "quantity" : 2, "date" : ISODate("2014-01-01T08:15:39.736Z") }   

// 操作  
db.collection.aggregate([
  {
    $project: {
      year: { $year: '$date'},  //年
      month: { $month: '$date'}, // 月
      day: { $dayOfMonth: '$date'}, // 日
      hour: { $hour: '$date'},  // 时
      minutes: { $minute: '$date'},  // 分
      seconds: { $second: '$date'},  // 秒
      milliseconds: { $millisecond: '$date'},  // 毫秒
      dayOfYear: { $dayOfYear: '$date'},  // 一年中的某一天
      dayOfWeek: { $dayOfWeek: '$date'},  // 一周的某一天
      week: { $week: '$date'},  // 第几周
      yearMonthDayUTC: { $dateToString: { format: "%Y-%m-%d", date: "date"} },  //"2014-01-01"
      time: { $dateToString: { format: '%H:%M%S:%L', date: '$date'}}  // "08:15:39:736"
    }
  }
])

// 结果
{
  "_id" : 1,
  "year" : 2014,
  "month" : 1,
  "day" : 1,
  "hour" : 8,
  "minutes" : 15,
  "seconds" : 39,
  "milliseconds" : 736,
  "dayOfYear" : 1,
  "dayOfWeek" : 4,
  "week" : 0,
  "yearMonthDayUTC" : "2014-01-01", 
  "time" : "08:15:39:736"
}
``` 

#### 条件操作符

|操作符| 简述|  
|-----|--------|    
|$cond| 用法：`{ $cond: [ <boolean-expression>, <true-case>, <false-case> ] }` 或者 v2.6+还支持`{ $cond: { if: <boolean-expression>, then: <true-case>, else: <false-case-> } }`|    
|$ifNull| 用法：`{ $ifNull: [ <expression>, <replacement-expression-if-null> ] }`|   

示例：  

```javascript
// 数据
{ "_id" : 1, "name" : "a",  score: 80 }
{ "_id" : 2, "name" : "b",  score: 69 }
{ "_id" : 3, "name" : "c",  score: 53 }
{ "_id" : 3, "name" : null,  score: 70 } 

// 操作
db.collection.aggregate([
  {
    $project: {
      _id: 0,
      score: 1,
      pass: {
        $cond: [ { $gte: ['$score', 60] }, 1, 0]
      },
      description: { $ifNull: [ '$name': 'Unspecified' ] }
    }
  }
])

// 结果
{ "name" : "a",  score: 80, pass: 1 }
{ "name" : "b",  score: 69, pass: 1 }
{ "name" : "c",  score: 53, pass: 0 }
{ "name" : "Unspecified",  score: 70, pass: 1 }
```
