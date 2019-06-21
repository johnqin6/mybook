# 封装回调  

如果需要获取一个函数中异步操作的结果，则必须通过回调函数获取  

实例demo 
```javascript
function fn(callback) {
  setTimeout(() => {
      var data = 'hello';
      callback(data);
  })
}

//调用函数，得到值 
fn(data => {
  console.log(data);
})
```    

# node 中的封装实例
```javascript
//student.js
/**
 * 获取所有学生列表
 * callback 中的参数 
 * callback(err, data)  错误对象， 数据
 * return []
 */
exports.find = function (callback) {
  fs.readFile(dbPath, (err, data) => {
      if(err) return callback(err) 
      callback(null, JSON.parse(data).students)
  })
}

//router.js
//使用
var students = require('./student.js');
router.get('/', (req, res) => {
    students.find((err, data) => {
        if(err) return res.status(500).end('服务器错误');
        res.render('index.html', {
            students: data
        })
    })
});

```

