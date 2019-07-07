# 聚合管道（aggregate）

## 什么是聚合管道(aggregation pipeline)  
英文文档中是aggregation pipeline，直译为聚合管道，它可以对数据文档进行变换和组合。聚合管道是基于数据流概念，数据进入管道经过一个或多个stage，每个stage对数据进行操作（筛选，投射，分组，排序，限制或跳过）后输出最终结果。


## 管道操作符介绍     
mongoDB有4类操作符用于文档的操作，例如find查询里面会用到的gte，in等。操作符以$开头，分为查询操作符，更新操作符，管道操作符，查询修饰符4大类。其中管道操作符是用于聚合管道中的操作符。


## 管道操作符的分类  

1. 阶段操作符（stage operators）
2. 表达式操作符(expression operators)  
3. 累加器（accumulators）  

[mongodb官网文档](https://docs.mongodb.com/manual/reference/operator/aggregation/#accumulators)

- 阶段操作符(stage Operators)   
阶段操作符是使用于db.collection.aggregate方法里面，数组参数中的第一层   

> db.collection.aggregate([{阶段操作符：表述},{阶段操作符：表述}])    

- 表达式操作符（Expression Operators）    
表达式操作符主要用于在管道中构建表达式时使用，使用类似于函数那样需要参数，主要用于$project操作符中，用于构建表达式，
使用方法一般如下     
方法1：     
> { `operator: [ argument1, argument2]` } 

方法2：       
> { `operator: argument` }      

- 累加器 （Accumulators）

累加器本来只能使用与groud下，但是版本3.2或以上，部分累加器还能使用于project。当在group中使用时，累加器是针对每个分组使用的；当在project中使用时，累加器则是针对每个字面量起作用   

## 常用阶段操作符

|操作符| 简述|  
|-----|------|   
|$project| 投射操作符，用于重构每一个文档的字段，可以提取字段，重命名字段，甚至可以对原有字段进行操作后新增字段|  
|$match| 匹配操作符，用于对文档集合进行筛选|   
|$group| 分组操作符，用于对文档集合进行分组|   
|$unwind| 拆分操作符，用于将数组中的每一个值拆分为单独的文档|   
|$sort| 排序操作符， 用于根据一个或多个字段对文档进行排序|  
|$limit| 限制操作符， 用于限制返回文档的数量|   
|$skip| 跳过操作符，用于跳过指定数量的文档|   
|$lookup| 连接操作符，用于连接同一个数据库中另一个集合，并获取指定的文档，类似populate|   
|$count| 统计操作符，用于统计文档的数量| 

### 阶段操作符详解   
假设有一个保存用户的集合Users，一个文章的集合Articles，数据大致如下：   
users:     
```javascript
[
    { name: 'John', age: 16, sex: male, city: guangzhou, _id: 1, ...},
    { name: 'Rose', age: 18, sex: female, city: beijing, _id: 2, ...},
    { name: 'Jack', age: 29, sex: male, city: guangzhou, _id: 3, ...},
    { name: 'Allen', age: 18, sex: female, city: beijing, _id: 4, ...},
    { name: 'Cruz', age: 22, sex: male, city: guangzhou, _id: 5, ...},
    { name: 'Peter', age: 18, sex: male, city: guangzhou, _id: 6, ...},
    { name: 'Kelly', age: 23, sex: female, city: shanghai, _id: 7, ...},
    ...
]
```

articles:        
```javascript
[
    { title: 'this is article A', author: 'John', _id: 1, ... },
    { title: 'this is article B', author: 'Jack', _id: 2, ... },
    { title: 'this is article C', author: 'Rose', _id: 3, ... },
    { title: 'this is article D', author: 'John', _id: 4, ... },
    { title: 'this is article E', author: 'John', _id: 5, ... },
    ...
]
```  

#### $match 匹配操作符 对文档集合进行筛选    
用法：   
> { `$match: { <query> }` }     
示例：   
查询用户年龄是18岁的用户     
```javascript
db.users.aggregate([{ $match: { age: "18"} }])
```  

#### $project 投射操作符 
说明：    
重构每一个文档的字段，可提取字段，重命名字段，甚至可对原字段进行操作后新增字段     

用法：   
> { $project: { <specification(s)> } }     
specofication的规则    

| 规则| 描述 |   
|----|-------|  
|字段名：1 or true| 选择返回的字段 |    
|_id: 0 or false| 不返回_id(默认返回)|     
|字段名：表达式| 使用表达式，可以用于重命名字段，或对其值进行操作，或新增字段|   
|字段名：0 or false| 选择需要不返回什么字段，注意：当使用这种用法时，就不要用上面的方法|  

示例1：   
+ 用户集合投射用户姓名
+ 不返回_id      
```javascript
db.users.aggregate([{ $project: { name: 1} }])
``` 

示例2：     
+ 将_id重命名为userid  
+ 不返回id     
```javascript
db.users.aggregate([{ $project: { userid: '$_id', _id: 0} }])
``` 

示例3：
+ 返回新字段username,并使用表达式让它的值为name的大写     
```javascript
db.users.aggregate([
  { 
    $project: {
      name: 1,
      username: { $toUpper: '$name' },
      _id: 0
    } 
  }
])
```   

**注：关于管道表达式：最简单的“project”表达式是包含和排除字段(如: { name: 1 })，以及字段名称fieldname(如: { userId: '_id' })。除此以外，还可以使用表达式操作符(如:toUpper)构成更丰富的表达式，将多个字面量和变量组合在一起使用，得到更多有意思的值**

#### $sort 排序操作符   
说明：    
根据一个或多个字段对文档进行排序     
用法：     
> { $sort: `{ <field1>: <sort order>, <field2>: <sort order> ...}` }    

示例：   
+ users集合按照年龄age从低到高排序   
```javascript
db.users.aggregate([{ $sort: { age: 1 } }])
```   

#### $limit 限制操作符  
说明：   
限制返回的文档的数量    
用法：     
> { $limit: `<positive integer>` }       
示例：     
+ 返回5篇article     
```javascript
db.articles.aggregate([{ $limit: 5 }])
```    

#### $skip 跳过操作符  
说明：  
跳过指定数量的文档   
用法：   
> { $skip: `<positive integer>` }     
示例：   
+ 跳过1个文档     
```javascript
db.articles.aggregate([{ $skip: 1 }])
```  
 
#### $count 统计操作符  
说明：    
统计文档的数量   
用法：    
> { $sount: `<string>` }     
示例：    
+ 统计文章的总数，以totalArticle返回    
```javascript
db.articles.aggregate([{ totalArticle: 1 }])
```    

#### $group 分组操作符  
说明：    
对文档集合进行分组    
用法：    
> { $group: `{ _id: <expression>,<field1>: { <accumulator1>: <expression1> }, ...}`}      
**注：_id是必须的，用于分组的依据条件**    
示例：    
+ 将用户(users)按性别（sex）分组    
> db.users.aggregate([{ $group: { _id: '$sex'} }])    
返回结果：     
```javascript
[
  { _id: 'male' },
  { _id: 'female' }
]
```    

进阶示例：    
+ 将用户(users)按性别（sex）分组   
+ 分组后使用计算各自性别的平均年龄  
+ 统计不同的性别的人数，并以count返回   
```javascript  
db.users.aggregate([
  {
    $group: {
      _id: '$sex',
      avgAge: { $avg: '$age' },  // 求平均值
      count: { $num: 1 }  // 汇总
    }
  }
])
```  

返回结果：    
```javascript   
[
  { _id: 'male', avgAge: <男性平均年龄>, count: <男性人数> },
  { _id: 'female', avgAge: <女性平均年龄>, count: <女性人数> }
]
```

#### $unwind 拆分操作符  
说明：   
将数组中的每一个值拆分为单独的文档    
用法：    
> { $unwind: `<field path> `}    
3.2+版本的用法：   
```s
{
  $unwind:
    {
      path: <field path>,
      includeArrayIndex: <string>,
      preserveNullAndEmptyArrays: <boolean>
    }
}
```  

| 字段| 类型 | 描述|    
|----|-----|------|   
|path| string| 必填，数组的字段名，指定需要拆分的字段|   
|includeArrayIndex| string| 可选，定义返回的字段名，返回的值是拆分前值在原数组的位置|   
|preserveNullAndEmptyArrays| boolean| 可选，配置在path的值为空或缺失的情况下是否拆分， 默认false|    

示例：   
```javascript
// 原数据
{ title: 'this is article A', author: 'John', _id: 1, comments: ['a', 'b', 'c']}  

db.articles.aggregate([{ $unwind: '$comments' }])  

// 结果 
[
    { title: 'this is article A', author: 'John', _id: 1, comments: 'a'},
    { title: 'this is article A', author: 'John', _id: 1, comments: 'b'},
    { title: 'this is article A', author: 'John', _id: 1, comments: 'c'},
]
``` 

进阶示例：     
```javascript
// 原始数据
[
    { title: 'this is article A', author: 'John', _id: 1, comments: ['a', 'b', 'c'] }
    { title: 'this is article B', author: 'Jack', _id: 2 },
    { title: 'this is article C', author: 'Amy', _id: 3, comments: [] },
    { title: 'this is article D', author: 'Lam', _id: 4, comments: null },
]

// 操作  
db.articles.aggregate([
  {
    $unwind: {
      path: '$comments',
      includeArrayIndex: 'arrayIndex',
    }
  }
])

// 结果  
[
    { title: 'this is article A', author: 'John', _id: 1, comments: 'a', arrayIndex: NumberLong(0) },
    { title: 'this is article A', author: 'John', _id: 1, comments: 'b', arrayIndex: NumberLong(1) },
    { title: 'this is article A', author: 'John', _id: 1, comments: 'c', arrayIndex: NumberLong(2) },
]

// 操作
db.articles.aggregate([
    { 
        $unwind: {
            path: '$comments',
            preserveNullAndEmptyArrays: true,
        }
    }
]);

// 结果  
[
    { title: 'this is article A', author: 'John', _id: 1, comments: 'a' },
    { title: 'this is article A', author: 'John', _id: 1, comments: 'b' },
    { title: 'this is article A', author: 'John', _id: 1, comments: 'c' },
    { title: 'this is article B', author: 'Jack', _id: 2 },
    { title: 'this is article C', author: 'Amy', _id: 3 },
    { title: 'this is article C', author: 'Amy', _id: 3, comments: null }
]
```  

#### $lookup 连接操作符  
说明：   
连接同一个数据库中另一个集合，并获取指定的文档，类似于populate    

用法：    
```javascript  
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}
```  

| 字段| 描述 |     
|-----|------|    
|form| 需要关联的集合名|   
|localField| 本集合中需要查找的字段|     
|foreignField| 另外一个集合中需要关联的字段|    
|as| 输出的字段名|    

示例：      
+ ariticles中的author关联到user表
+ authoer字段返回详细的用户的信息

```javascript
db.articles.aggregate([
  {
    $lookup:
      {
        from: "users",
        localField: "author",
        foreignField: "name",
        as: "author"
      }
  }
])
```   

#### 综合示例   
找出发表文章最多的5位作者，按发表文章排序，显示他的发表文章的总次数，和他自己的信息    
+ 文章按照作者分组,统计次数
+ 按照次数从高到低排序
+ 截取头5名
+ 关联用户信息
+ 不输出文章_id   

操作：   
```javascript
db.articles.aggregate([
  {
    $group: {
      _id: '$author',
      count: { $sum: 1 },
    }
  },
  {
    $sort: { count: -1 }
  },
  {
    $skip: 5
  },
  {
    $lookup: {
      from: 'users',
      localField: 'author',
      foreignField: 'name',
      as: 'author'
    }
  },
  {
    $project: {
      _id: 0
    }
  }
])
```
