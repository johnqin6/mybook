# aggregate聚合管道续(累加器)   

累加器本来只能使用与groud下，但是版本3.2或以上，部分累加器还能使用于project。当在group中使用时，累加器是针对每个分组使用的；当在project中使用时，累加器则是针对每个字面量起作用。  

## 常用累加器  

|操作符| 简述  |    
|-----|-------|     
|$num| 求和操作符，可用于group或project |   
|$avg| 求平均值操作符，可用于group或project |   
|$first| 返回分组的第一个值  只能用于group |    
|$last| 返回分组的最后一个值  只能用于group |  
|$max| 返回分组的最大值，可用于group或project |
|$min| 返回分组的最小值，可用于group或project |

### $sum 求和操作符  
用法：   
- $group下使用   
> { $sum: `<expression>`}    

- $project下使用：   
```javascript
{ $sum: <expression> }
    or
{ $sum: [ <expression1>, <expression2> ... ]  }
```  

说明：    

|例子|  值| 结果 |   
|`{ $sum: <field> }`| 只含数字| 所有值的和|    
|`{ $sum: <field> }`| 含数字和非数字| 所有数字值的和|   
|`{ $sum: <field> }`| 非数字或不存在| 0|     

### $svg 求均值操作符  

用法：   
- $group下使用   
> { $avg: `<expression>`}    

- $project下使用：   
```javascript
{ $avg: <expression> }
    or
{ $avg: [ <expression1>, <expression2> ... ]  }
```  

### $first 求分组的第一个值   
用法：   
```
{ $first: <expression> }
```

例子：  
```javascript
// 数据
[
  { _id: 1, name: 'kate', class: 'a' },
  { _id: 2, name: 'jack', class: 'a' },
  { _id: 3, name: 'kent', class: 'b' },
]

// 操作
db.collection.aggregate([
  {
    $group: {
      _id: '$class',
      firstPersonName: { $first: '$name' }
    }
  }
])

// 结果
[
    { _id: 'a', firstPersonName: 'kate' },
    { _id: 'b', firstPersonName: 'kent' },
]
```

### $last 求分组的最后一个值 
用法与$first相同  

### $max 求最大值操作符

用法：   
- $group下使用     
```
{$max: <expression> }
```  

- $project下使用    
```javascript
{ $max: <expression> }
    or
{ $max: [ <expression1>, <expression2> ... ]  }
```

### $min 求最小值操作符
用法与$max相同 


### 综合示例  

数据  
```javascript
// 数据
[
    { _id: 1, name: 'kate', score: 80, class: 'a', subject: 'A' },
    { _id: 2, name: 'kate', score: 60, class: 'a', subject: 'B' },
    { _id: 3, name: 'jack', score: 90, class: 'a', subject: 'A' },
    { _id: 4, name: 'jack', score: 60, class: 'a', subject: 'B' },
    { _id: 5, name: 'nick', score: 80, class: 'b', subject: 'A' },
    { _id: 6, name: 'nick', score: 90, class: 'b', subject: 'B' },
    { _id: 7, name: 'kent', score: 50, class: 'b', subject: 'A' },
    { _id: 7, name: 'kent', score: 30, class: 'b', subject: 'B' },
]
```
示例1：   
+ 获取各科目的最高分
+ 获取各科目的最低分
+ 获取各科目的平均分  
```javascript
db.collections.aggregate([
  {
    $group: {
      _id: '$subject',
      maxScore: { $max: '$score' },
      minScore: { $min: '$score' },
      avgScore: { $avg: '$score' }
    }
  }
])

// 结果
[
    { _id: 'A', maxScore: 90, minScore: 50, avgScore: 75 },
    { _id: 'B', maxScore: 90, minScore: 30, avgScore: 60 },
]
```

示例2：   
+ 获取每人的总分
+ 从高到低排序  

```javascript
db.collection.aggregate([
  {
    $group: {
      _id: '$name',
      totalScore: { $sum: '$score' }
    }
  },
  {
    $sort: { totalScore: -1 }
  },
  {
    $project: {
      _id: 0,
      name: '$_id',
      totalScore: 1
    }
  }
])

// 结果
[
    { name: 'nick', totalScore: 170 },
    { name: 'jack', totalScore: 150 },
    { name: 'kate', totalScore: 140 },
    { name: 'kent', totalScore: 80 }
]
```