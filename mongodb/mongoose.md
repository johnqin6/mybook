# mongoose 的使用

## mongoose是什么
mongoose是node.js提供连接mongodb的一个库，类似于jquery和js的关系，它对mongodb的一些原生方法
进行了封装和优化。简单地说，mongoose就是对node环境中mongodb数据库操作的封装，一个对象模型工具，
将数据库中的数据转化为javascript对象供我们使用。  

## mongoose的安装和连接数据库
- 安装
> npm install mongoose --save   
- 连接数据库服务器及监听数据库
```javascript
// 连接数据库服务器
mongoose.connect('mongodb://localhost/test', { useMongoClient: true})
// 获得数据库服务器对象
const db = mongoose.connection
// 监听数据库服务器错误信息
db.on('error', console.error.bind(console, 'connection error:'))
// 监听数据库服务器开启成功
db.once('open', () => {
  console.log('成功连接数据库')
  if (success) {
    success()
  }
})
```

- 设置数据库权限
```
> user test
> db.createUser({
  user: user,
  pwd: pwd,
  roles: [
    {
      role: "readWrite ...", // 具体权限
      db: ""  // database
    }
  ]
})
```
在node中连接数据库(已设连接权限)
> mongoose.connect('mongodb://admin:password@localhost/test')

## Schema 
Mongoose的一切都源于一个Schema。Schema是一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，
也就是说它无法直接操作数据库。但是Schema以一种映射的方式定义了集合的结构，每一个schema都映射mongodb
的集合(collection)并定义了集合中的文档及属性的结构，它还定义了文档实例方法、静态模型方法、复合索引和
文档中被称为中间件的生命周期钩子。   

示例：  
```javascript
// 定义一个Schema
const mongoose = require('mongoose')
const TestSchema = new mongoose.Schema({
  name: {
    type: String
  },
  age: {
    type: Number, // 类型为Number, 默认为0
    default: 0
  },
  time: {
    type: Date,
    default: Date.now
  },
  email: {
    type: String,
    default: ''
  }
})
```

### SchemaType 支持的类型有：
- String  字符串
- Number  数字
- Date    日期
- Buffer  二进制
- Boolean 布尔值
- Mixed   混合类型
- ObjectId 对象id
- Array   数组

### 在SchemaType上定义的验证器  
- 验证器规则
  + 验证器是在SchemaType上定义的
  + 验证是中间件。Mongoose验证器作为`pre('save')`前置钩子在某个模式下默认执行  
  + 可以手动使用`document`运行验证，`validate(callback)`或`doc.validateSync()` 
  + 验证程序不运行在未定义的值上，除了`required`验证器。
  + 验证异步递归，当你调用`Model#save`,子文档验证也可以执行。如果出现错误，你的`Model#save`回调接收它
  + 验证是可自定义的

- 内置验证器
mongoose提供了几个内置验证器    
  + 所有的`SchemaType`都有内置的`require`验证器，所需的验证器使用`SchemaType`的`checkrequired()`
    函数确定值是否满足所需的验证器
  + 数值(Numbers)有最大(`man`)和最小(`min`)的验证器
  + 字符串(String)有`enum`,`match`,`maxLength`和`minLength`验证器

示例：创建一个用户`Schema`,给不同的字段添加验证器  
```javascript
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const UserSchema = new Schema({
  name: {
    type: String,
    required: true,
    minlength: 3,
    maxlength: 6
  },
  age: {
    type: Number,
    min: 10,
    max: 60,
    required: true
  },
  sex: {
    type: String,
    enum: {
      values: ['male', 'female'],
      message: '`{PATH}`是`{VALUE}`, 您必须确定您的性别!'
    },
    required: true
  },
})
```
- 验证器的错误提示(`message`)中，有5个内置变量供我们使用
  + {PATH}: 键名
  + {VALUE}: 当前键值
  + {TYPE}: 验证器类型，比如min, regexp等
  + {MIN}: 最小值，只存在数值
  + {MAX}: 最大值，只存在数值

对于内置验证器，我们还可以自定义他的错误提示信息，例如：
```
min: [6, "自定义错误提示"],
required: [true, "必须项"]
```  

- 自定义验证器
我们以自定义一个手机验证器为例  
```javascript
// modules/common/validation.js  

module.exports = {
  phone(v) {
    return /1[3|5|7|8]\d{9}/.test(v)
  }
}
```
然后我们在`UserSchema`中的`phone`字段上添加自定义验证器   
```javascript
// modules/users/usersModel.js
...
const validation = require('../commen/validation')

const UserSchema = new Schema({
  ...
  phone: {
    type: String,
    validate: {
      validator: validation.phone,
      message: '`{PATH}`必须是有效的11位手机号码!'
    },
    required: true
  }
}, {collection: 'users'})
```
我们也可以使用数组添加多个验证器
```javascript
[
  {
    validator: validation.phone,
    message: '`{PATH}`必须是有效的11位手机号码!'
  }
]
``` 
- 错误提示
当验证失败后，Error会返回一个错误对象，它实际上是`validatorError`对象，每个`validatorError`
对象都有`kind`,`path`,`value`,`message`属性。  
```javascript
const user = new UserModel(req.body)
user.save((err, result) => {
  if(err) {
    console.error(err.errors['name']['message']);
    return res.status(400)
    .send({
      message: err
    })
  } else {
    res.jsonp(user)
  }
})
```
我们也可以同步拿到验证错误
```javascript
const user = new UsersModel();
const errors = user.validateSync();
```  
除了在Schema上添加验证器，我们还可以使用`validate()`方法,作用与在Schema上定义一样      
> UserSchema.path('phone').validate(validation.phone, '`{PATH}`必须是有效的11位手机号码!')   

默认情况下，验证器只有`save`操作才会触发，但是在mongoose4.x后，我们也可以开启`update()`和
`findOneAndUpdate()`的验证器，只需将`runVailidators`设为true(默认为false)   
```javascript
const opts = { runVailidators: true}
UsersModel.update({}, { name: 'john'}, opts, err => {})
```  
注意：update验证器只运行在$set,$unset,$push(>=4.8.0),$addToSet(>=4.8.0),$pull(>=4.12.0),
$pullAll (>= 4.12.0)。

## Model  
由Schema构造器生成的模型，除了Schema定义的数据库骨架外，还具有数据库操作的行为，类似于管理数据库属性
，行为的类。   
> const User = mongoose.model('User', UserSchema)      
集合名字首字符大写，mongoose会自动将该词转为复数，如'User'转为'users',集合不存在将自动创建   

## Entity
由Model创建的实体，使用`save`方法保存数据，Model和Entity都具有影响数据库的操作，但Model比Entity
更具有操作性。  
```javascript
const TestEntity = new TestModel({
  name : "Lenka",
  age  : 36,
  email: "lenka@qq.com"
});
console.log(TestEntity.name); // Lenka
console.log(TestEntity.age); // 36
```  

## 方法  

## 实例方法
模型的实例是文档(document)。文档有许多内置的实例方法，同时我们也可以自定义文档实例方法    
```javascript
const UserSchema = new Schema({ name: String, age: Number}) 
const User = mongoose.model('User', UserSchema)
```
常用的实例方法有`remove`,`set`,`invalidate`,`populate`,`save`等。  

我们也可以自定义实例方法。   
```javascript
UserSchema.methods.findUserName = function(cb) {
  return this.model('User').find({ name: this.name}, cb)
}
```

### 静态方法
常用的内置静态方法有：`create`,`find`,`findOne`等。   
给模型添加静态方法：
```javascript
UserSchema.statics.findByName = function(name, cb) {
  return this.find({ name: new RegExp(name, 'i')}, cb)
}

const User = mongoose.model('User', UserSchema)
User.findByName('john', (err, user) => {
  console.log(user)
})

//批量添加静态方法
UserSchema.statics = {
  fetch: function(cb) {
    return this
      .find()
      .sort('meta.updateAt')
      .exec(cb)
  },
  findByName: function(name, cb) {
    return this
      .findOne({
        name: name
      })
      .exec(cb)
  }
}
```

## mongoose 模块化

1. 创建一个`db.js`文件
```javascript
const mongoose = require('mongoose')

mongoose.connect('mongodblocalhost/test', { useMongoClient: true}, () => {
  console.log('数据库服务器已连接')
})

moudle.exports = mongoose
```
2. 在相关模块文件引入`db.js`文件
```javascript
// user.js
const mongoose = require('./db.js')

const Schema = mongoose.Schema

const UserSchema = new Schema({
  name: String,
  gender: {
    type: Number,
    enum: [1, 2]
  }
})

const User = mongoose.model('User', UserSchema, 'user')

module.exports = UserModel  

```

## mongoose预定义修饰符

- lowercase(转小写), uppercase(转大写), trim(去首尾空格) 
```javascript
var UserSchema = mongoose.Schema({
  name: {
    type: String,
    trim: true 
  },
  age: Number
})
```  
- mongoose getter和setter修饰符
除了mongoose内置的修饰符以外，我们还可以通过set（建议使用）修饰符在增加数据时对数据格式化。
也可以通过get(不建议使用)在实例获取数据时对数据进行格式化。   
```javascript
// setter:  
var NewSchema = mongoose.Schema({
  title: String,
  redirect: {
    type: String,
    set(url) {
      if(!url) return url;
      if(url.indexOf('http://')!==0 && url.indexOf('https://') != 0) {
        url = 'http://' + url;
      }
      return url;
    },
    get(url) {
      if(!url) return url;
      if(url.indexOf('http://')!==0 && url.indexOf('https://') != 0) {
        url = 'http://' + url;
      }
      return url;
    }
  }
})



```