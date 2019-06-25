# mongoose使用进阶  

## 索引
索引可以加快查询速度 

```javascript
// 设计Schema模型时创建索引
const ArticlesSchema = new Schema({
  title: {
    ...
    index: true
  }
}, {collection: 'articles'})

// 创建唯一索引
const ArticlesSchema = new Schema({
  title: {
    ...
    index: true,
    unique
  }
}, {collection: 'articles'}) 

// 统一建索引
ArticlesSchema.index({ name: 1});
// 1 表示正序， -1表示逆序  

// 复合索引
ArticlesSchema.index({ name: 1, by: -1 })
ArticlesSchema.index({ name: 1, by: -1}, {unique: true})
```
注：当应用启动时，mongoose会自动为Schema中每一个定义了索引的调用ensureIndex, 确保生成索引，并在所有的
ensureIndex调用成功或出现错误时，在model上发出一个'index'事件。开发环境用这个很好，但生产环境不要使用这个。  
下面的方法禁用`ensureIndex`  
```javascript
mongoose.connect('mongodb://localhost/test', { config: { autoIndex: false}}) //推荐
mongoose.createConnect('mongodb://localhost/test', { config: { autoIndex: false}}) //不推荐
userSchema.set('autoIndex', false)  // 推荐
new Schema({...}, {autoIndex: false})  // 不推荐
```
注：对于添加的每一条索引，每次写操作（插入、更新、删除）都将耗费更多的时间。这是因为，当数据发生变化时，不仅要更新文档，还要更新集合上的所有索引。因此，mongodb限制每个集合最多有64个索引。通常，在一个特定的集合上，不应该拥有两个以上的索引。   

## 联表查询 
mysql使用join方法进行联表查询，mongoose中没有join方法，但提供了另一种更方便快捷的方法——Population.

```javascript
// 定义两个model
const User = mongoose.model('User', {
  username: String,
  password: String
})
const News = mongoose.model('News', {
  title: String,
  author: {
    type: mongoose.Schema.ObjectId,
    ref: 'User'
  }
})

// 添加数据
User.create({username: 'ck', password: '12345'}, (err, data) => {
  News.create({ title: 'title', author: data}, () => {})
})

// 查询
News.findOne().populate('author', 'username').exec((err, doc) => {
  console.log('====',err,doc) 
  // ==== null { _id:  5a41d2e16f78a43c5873fe03,
  //    title: 'title',
  //    author: { _id: 5a41d2e16f78a43c5873fe02, username: 'ck'},
  //    _v: 0
  //}
})
```  

## 虚拟属性（针对实例即文档）
虚拟属性是文档属性，可以获取和设置但不能保存到mongoDB,用于格式化或组合化字段，从而
制定者可以组成一个单一值存储多个值。  
```javascript
const personSchema = new Schema({
  name: {
    first: String,
    last: String
  }
})

const Person = mongoose.model('Person', personSchema)

const person = new Person({
  name: { first: 'john', last: 'tom'}
})

// 打印全名
console.log(person.name.first + ' ' + person.name.last) // john tom
```
如果我们在person定义虚拟属性的getter,就不需每次都写出这个字符串的拼接   
```javascript
personSchema.virtual('name.full').get(() => {
  return this.name.first + ' ' + this.name.last
})

console.log(person.name.full) // john tom
```

## 中间件
中间件（也称为前置钩子和后置钩子）是异步函数执行过程中传递的控制的函数，中间件是在schema级别上指定的。  
mongoose4.0 有两种类型的中间件：文档(document)中间件和查询(query)中间件。   
文档(document)中间件支持一下文档方法
  + init
  + validate
  + save
  + remove
查询(query)中间件支持模型和查询方法
  + count
  + find
  + findOneRemove
  + update  

文档中间件和查询中间件支持前置（pre）和后置(post)钩子    
- pre(前置钩子) 
有两种前置钩子，串行(serial)和并行(parallel)  
  + serial 
  串行中间件是一个接一个执行，只有前一个中间件里调用next()函数，后一个中间件才会执行  
  ```javascript
  const schema = new Schema({...})
  schema.pre('save', next => { next() })
  ```
  + parallel
  直到完成每个中间件才会去执行钩子方法里的操作  
  ```javascript
  const schema = new Schema({...})
  schema.pre('save', true, (next, done) => {
    next()
    setTimeout(done, 100)
  })
  ```
  错误处理：如果任何一个中间件调用next或done一个类型错误的参数，则流被中断，并将错误传递给回调  

- 后置钩子(post) 
后置中间件被执行后，钩子的方法和所有的前置钩子都已完成，后置钩子是一种为这些方法注册传统事件侦听器方式，
可看作是一种操作后的提示   
```javascript
schema.post('init', doc => {
  console.log(doc._id)
})
``` 

## 插件
我们是可以通过插件形式来拓展Schema的功能。

比如文章，当我们修改文章时，一般都会添加一个最后编辑时间，虽然我们可以每次修改时都手动更新，但是我们可以通过插件来自动更新    
```javascript
// modules/common/plugins.js
module.exports = {
  lastModified(schema, options) {
    schema.add({ lastMod: Date})
    schema.pre('save', next => {
      this.lastMod = new Date
      next()
    })
  }
}

// modules/articles/articles.model.js
const plugins = require('../common/plugins')
ArticlesSchema.plugin(plugins.lastModified)
```
当你点击页面的update按钮时，你会发现修改的文章文档已经添加了lastMod字段，并且每次修改都会更新。 

plugin()方法也可以传递第二个参数，用于传递额外参数：  
```javascript
ArtclesSchema.plugin(plugins.lastModified, {index: true})

module.exports = {
  lastModified(schema, options) {
    console.log(options.index)
  }
}
```

- 全局pligins  
mongoose单独有一个plugin()功能为每一个schema注册插件  
```javascript
const plugins = require('../common/plugins')
mongoose.plugin(plugins.lastModified)
```
