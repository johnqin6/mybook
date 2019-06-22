# Promise   

## 回调地狱 (callback hell)

例子
```javascript
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
```  
- 异步函数无法保证执行的顺序
  + 可以使用回调嵌套来保证执行顺序  

## Promise 
- 为了解决回调地狱带来的问题（回调嵌套地狱），ESMAScript6提供了新的API--Promise 
- Promise 是一个构造函数  
- Promise 容器 一旦创建就会执行里面的代码


例子 
```javascript
var getA = new Promise((resolve, reject) => {
    fs.readFile('./data/a.txt', (err, data) => {
        if (err) {
            reject(err)   // 执行失败时执行
        }
        // 成功时执行
        resolve(data)
    })
})

var getB = new Promise((resolve, reject) => {
    fs.readFile('./data/b.txt', (err, data) => {
        if (err) {
            reject(err)   // 执行失败时执行
        }
        // 成功时执行
        resolve(data)
    })
})

// promise的调用
getA.then( res => {
    console.log(res)
    return getB
}).catch(err => {
    console.log(err)
}).then( res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})
```  
将上例封装改良
```javascript
function pReadyFile (url) {
  return new Promise((resolve, reject) => {
      fs.readyFile(url, (err, data) => {
          if (err) {
              reject(err)
          } else {
              resolve(data)
          }
      })
  })
}

pReadyFile('./data/a.txt')
.then(res => {
    console.log(res)
    return pReadyFile('./data/b.txt')
}).catch(err => {
    console.log(err)
}).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})

```  
操作mongodb数据库的例子
```javascript
User.findOne({
    username: 'john'
}).then(user => {
    if (user) {
        console.log('用户已存在，不能注册')
    } else {
        // 用户不存在,可以注册
        return new User({
            username: 'john',
            password: '123',
            email: 'dddegrrg'
        }).save()
    }
}).then(res => {
    console.log(res)
})
```
