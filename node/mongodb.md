# node中使用mongodb数据库

mongodb的安装及详细使用可参考mongodb模块讲解[传送门](../mongodb/index.md)  

## 使用mongodb 第三方包操作 mongodb数据库 

1. 安装  
> npm install --save mongoose    
2. 引入
> const mongoose = require('mongoose')    

3. 实例
```javascript
const mongoose = require('mongoose');

// 连接mongodb数据库
mongoose.connect('mongodb://localhost/test', { useNewUrlParser: true });

// 使用 
mongoose.Promise = global.Promise;  

// 创建一个模型，即设计数据库 - 设计集合结构
const Cat = mongoose.model('Cat', {name: String});

// 实例化一个 Cat
const Kitty = new Cat({ name: 'john'});

// 持久化保存 kitty 实例
Kitty.save(err => {
    if (err) return console.log(err)
    console.log(11);
})
```

### mongoose 对数据库的操作 

- 设计集合结构
```javascript
const Schema = mongoose.Schema;
// 设计集合结构  字段名称即属性名称  目的：保证数据的完整性，不要有脏数据
const userSchema = new Schema({
    username: {
        type: String,
        required: true   // 必有
    },
    password: {
        type: String,
        required: true
    },
    email: {
        type: String
    }
})

// 将文档发布为模型
// mongoose.model 方法就是将一个架构发布为model
// 第一个参数：传入一个大写名词单数字符串来表示你的数据库名称，mongoose会自动将大写名词转为小写复数的集合名称
// 第二个参数：架构 Schema
// 返回值：模型构造函数
const User = mongoose.model('User', userSchema)
```

- 使用 构造函数 并保存数据
```javascript
// 此时就可以使用该构造函数进行各种增删改查操作
const admin = new User({
    username: 'admin',
    password: '123456',
    email: 'admin@admin.com'
})

// 数据持久化 保存新增 
admin.save((err, result) => {
    if (err) return console.log('保存失败')
    console.log('保存成功', result)
})
```  

- 查询
```javascript
// 查询  
User.find((err, result) => {
    if (err) return console.log('查询失败')
    console.log(result)
})

// 按条件查询
User.find({
    username: 'admin'
}, (err, result) => {
    if (err) return console.log('查询失败')
    console.log(result)
})

// 查询单条
User.findOne({
    username: 'admin'
}, (err, result) => {
    if (err) return console.log('查询失败')
    console.log(result)
})
```  

- 删除数据  
```javascript
User.remove({
    username: 'admin'
}, (err, result) => {
    if (err) return console.log('删除失败')
    console.log('删除成功,' + result)
})

// 根据id删除
Student.findByIdAndDelete(id, (err, result) => {
    if (err) return res.send(err)
    res.redirect('/')
})
```  

- 更新数据
```javascript
// 更新数据
User.findByIdAndUpdate('5d0dd7bf93077b3d8428ff2f', {
    password: '123'
}, (err, result) => {
    if(err) return console.log('更新失败')
    console.log('更新成功:' + result)
})
```