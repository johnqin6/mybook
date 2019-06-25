# mongoose 使用(文档操作篇)  

## 文档的操作  

- 在集合中创建文档
```javascript
const mongoose = require('mongoose')
const Schema = mongoose.Schema
const UserSchema = new Schema({
  name: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    min: 0,
    max: 110
  },
  gender: {
    type: String,
    enum: {
      values: ['male', 'female'],
      message: '`{PATH}`是`{VALUE}`,您必须确定您的性别'
    }
  }
})
const User = mongoose.model('User', UserSchema)

// 添加一条记录
// 1. 创建一个模型实例
const user = new User({
  name: 'john',
  age: 20,
  gender: male
})
// 2. 将实例保存
User.save(err => {
  if (err) return console.log(err)
  console.log('保存成功')
})
```

- 文档的查询方法 
  + model.find({},callback) 
    * 参数1忽略，或为空对象，则查询所有
  + model.find({},field,callback)
    * 过滤查询，参数2：控制返回的属性，
      如: {'name': 1, 'age': 0} 查询结果只返回name,不返回age,_id默认返回
  + model.find({},null,{limit:20},callback)
    * 过滤查询，参数3：限制返回的结果数量为20，不足返回全部
  + model.findOne({}, callback) 
    * 查询找到的第一个参数
  + model.findById('obj._id', callback)
    * 根据文档的_id查询第一个文档  

```javascript
// 返回该集合的全部记录
User.find(date => { console.log(data)})
// 只返回该集合所有记录中的name属性
User.find({},{name: 1}, data => { console.log(data) })
// 返回前20条记录
User.find({},null,{limit: 20}, data => { console.log(data) })
// 只查询符合条件的第一条记录
User.findOne({name: 'john'}, data => { console.log(data) })
```  

- 文档的查询条件
  + 条件查询
    * `$lt` 小于
    * `$lte` 小于等于
    * `$gt` 大于
    * `$gte` 大于等于
    * `$ne`  不等于  
  示例：
  ```javascript
  // 查询 age 大于等于18小于等于30的文档
  User.find({
    'age': {
      $gte: 18,
      $lte: 30
    }
  })
  ```
  + 或查询 OR
    * `$in` 一个键对应多个键
    * `$nin` 同上取反，一个键不对应指定键
    * `$or`  多个条件匹配，可以嵌套$in使用
    * `$not` 同上取反，查询与特定模式不匹配的文档  
  示例： 
  ```javascript
  // 查询 age 等于20或21或30的文档 
  User.find({
    age: {
      $in: [20, 21, 30]
    }
  })

  // 查询 age等于18或name等于'john'的文档
  User.find({
    $or: [
      { age: 18 },
      { name: 'john'}
    ]
  })
  ```
  + 类型查询  
    null 能匹配自身和不存在的值，想要匹配的值为null, 就要通过`$exists`
    条件判定键值已经存在，`$exists`(表示是否存在的意思)   
  示例：
  ```javascript
  // 查询 age值为null的文档
  User.find({
    age: {
      $in: [null],
      $exists: true
    }
  })

  // 查询所有存在name属性的文档
  User.find({
    name: {
      $exists: true
    }
  })

  // 查询所有不存在phone属性的文档
  User.find({
    phone: {
      $exists: false
    }
  }, (err, dacs) => {})
  ```
  + 模糊查询 -- 正则表达式
    mongodb使用prel兼容的正则表达式库来匹配正则表达式  
  示例：   
  ```javascript
  // 查询name为john的文档，并忽略大小写 
  User.find({
    name: /john/i
  }, (err, docs) => {})
  // 查询匹配各种大小写组合
  User.find({
    name: /john?/i
  })
  // 查询name以john开头的文档
  User.find({
    name: /^john/
  })
  // 查询name以john结尾的文档
  User.find({
    name: /john$/
  })
  ```  
  + 查询数组
  示例：
  ```javascript
  // 查询 array(数组类型)键中有10的文档， array: [1,2,3,4,5,10] 会匹配到
  User.find({
    array: 10
  })
  // 查询 array(数组类型)键中下标5对应的值是10,  array : [1,2,3,4,5,10]  会匹配到
  User.find({
    'array[5]': 10
  })
  ```
  + `$all`匹配数组中的多个元素
  ```javascript
  // 查询数组中既有5又有10的文档
  User.find({
    'array': [5, 10]
  })
  ```
  + `$size`匹配数组的长度
  ```javascript
  // 查询array数组长度为3的文档
  User.find({
    'array': {
      $size: 3
    }
  })
  ```
  `$slice`查询子集合返回  
  ```javascript
  // 查询 匹配array数组的前十个元素
  User.find({
    'array': {
      $skice: 10
    }
  })
  // 查询 匹配array数组的第5个到第10个元素
  User.find({
    'array': {
      $skice: [5, 10]
    }
  })
  ```
  + where语句  
    用它可以执行任意javascript语句作为查询的一部分，如果返回true文档就作为结果返回
  ```javascript
  User.find({
    '$where': function() {
      for( var x in this) {
        // 这个函数的this就是文档
      }
      if (this.x !== null && this.y !== null) {
        return this.x + this.y === 10 ? true : false
      } else {
        return true
      }
    }
  })

  // 简化版
  User.find({
    '$where': 'this.x + this.y === 10'
  })
  User.find({
    '$where': 'function(){ return this.x + this.y === 10 }'
  })
  ``` 

- 文档的更新
  + Model.updata(conditions, updata, callback)
    * 参数1：查询条件，参数2：更新对象  
  + Model.findByOneAndUpdate(conditions, updata, callback)  
    * 只更新查找到的第一个文档 
  + Model.findByIdAndUpdate(id, update, callback)
    * 根据id查找更新文档  
示例： 
```
// 更新age大于等于30的文档的gender属性值为'female'
User.update({
  age: {
    $gte: 30
  }
},{gender: 'female'},err => {})  

// 更新name='john'的年龄为20
User.update({name: 'john'},{age: 20}, err => {})
```  

- 更新修改器 
  + `$inc` 增减修改器，只对数字有效 
  ```javascript
  // 找到 age=22的文档，修改文档的age自增1
  User.update({age: 22},{$inc: {age: 1}})
  ```
  + `$set`指定一个键，这个键不存在就创建它，它可以是任何Mongodb支持的类型
  ```javascript
  // 找到 age=22的文档，修改文档的age为'haha'
  User.update({age: 22}, {$set: {age: 'haha'}})
  ```
  + `$unset`同上取反，删除一个键
  ```javascript
  // 找到 age=22的文档，删除文档的age键
  User.update({age: 22}, {$unset: {age: 'haha'}})
  ```
  数组修改器   
  + `$push`给一个键push一个数组成员，键不存在就创建 
  ```javascript
  // 增加一个array键，类型为数组，有一个成员10
  User.update({age: 22}, {$push: {'array': 10}})
  ```
  + `$addToSet`向数组中添加一个元素，如果存在就不添加
  ```javascript
  User.update({age: 22},{$addToSet: {'array': 10}})
  ```
  + `$each`遍历数组，和$push修改器配合可以插入多个值
  ```javascript
  User.update({age: 22}, {$push: {$each: [1,2,3,4,5]}})  // array : [10,1,2,3,4,5]
  ```
  + `$pop`向数组尾部删除一个元素
  ```javascript
  User.update({age: 22}, {$pop: {array: 1}})
  // 执行后：array: [10,1,2,3,4] 将改为-1可以删除数组的首部元素
  ```
  `$pull`向数组中删除指定元素
  ```javascript
  User.update({age: 22}, {$pull: {array: 10}})
  // 执行后：array: [1,2,3,4] 匹配到array中的10后将其删除
  ```

- 删除文档 
  + Model.remove(conditions, callback) 
    * 删除符合条件的所有文档
  + Model.findOneAndRemove(conditions, callback)
    * 删除符合条件的第一条文档  
  + Model.findIdAndRemove(conditions, callback)
    * 根据文档id删除文档记录  
  + Model.deleteMany(conditions, callback) 
    * 删除符合条件的多条数据
  + Model.deleteOne(conditions, callback)
    * 删除符合条件的第一条数据  
  + Model.findOneAndDetele(conditions, callback)
    * 删除符合条件的第一条数据
  + Model.findIdAndDetele(conditions, callback)
    * 根据文档id删除文档记录