# mongoose 高级

# mongoose聚合管道 

##  mongodb 的聚合管道查询

```javascript
// 数据模拟
// order集合中插入的数据
db.order.insert({'order_id': 1, 'uid': 10, 'trade_no': 111, 'all_price': 100, 'all_num': 2})
db.order.insert({'order_id': 2, 'uid': 7, 'trade_no': 222, 'all_price': 12, 'all_num': 2})
db.order.insert({'order_id': 3, 'uid': 9, 'trade_no': 333, 'all_price': 80, 'all_num': 6})

// order_item集合中插入的数据
db.order_item.insert({'order_id': 1, 'title': '鼠标1', 'price': 50, num: 1})
db.order_item.insert({'order_id': 1, 'title': '鼠标2', 'price': 30, num: 1})
db.order_item.insert({'order_id': 1, 'title': '鼠标3', 'price': 20, num: 1})

db.order_item.insert({'order_id': 2, 'title': '牛奶1', 'price': 50, num: 1})
db.order_item.insert({'order_id': 2, 'title': '牛奶2', 'price': 30, num: 1})
db.order_item.insert({'order_id': 2, 'title': '牛奶3', 'price': 20, num: 1})

db.order_item.insert({'order_id': 3, 'title': '花生1', 'price': 50, num: 1})
db.order_item.insert({'order_id': 3, 'title': '花生12', 'price': 30, num: 1})
db.order_item.insert({'order_id': 3, 'title': '花生13', 'price': 20, num: 1})

// 聚合查询
db.order.aggregate([
  {
    $lookup: { // 连接操作符，用于连接同一个数据库中另一个集合，并获取指定的文档，类似于populate
      from: 'order_item', // 需要关联的集合名
      localField: 'order_id', // 本集合中需要查找的字段
      foreignField: 'order_id', // 另外一个集合中需要关联的字段
      as: 'items' // 输出的字段名
    }
  },
  {
    $match: { 'all_price': {$gte: 90} } // $match：用于过滤数据，只输出符合条件的文档
  }
])

// 查到的数据
{
  "_id" : ObjectId("5d2097d7cc0a208a81d5155a"),
  "order_id" : 1,
  "uid" : 10,
  "trade_no" : 111,
  "all_price" : 100,
  "all_num" : 2,
  "items" : [
    {
      "_id" : ObjectId("5d209454cc0a208a81d51551"),
      "order_id" : 1,
      "title" : "鼠标1",
      "price" : 50,
      "num" : 1
    },
    {
      "_id" : ObjectId("5d209454cc0a208a81d51552"),
      "order_id" : 1,
      "title" : "鼠标2",
      "price" : 30,
      "num" : 1
    },
    {
      "_id" : ObjectId("5d209456cc0a208a81d51553"),
      "order_id" : 1,
      "title" : "鼠标3",
      "price" : 20,
      "num" : 1
    }
  ]
}
```

## mongoose中使用聚合管道

```javascript
var mongoose = require('./db.js');

// 定义orderSchema
var OrderSchema = mongoose.Schema({
  order_id: String,
  uid: Number,
  trade_no: String,
  all_price: Number,
  all_num: Number
});

module.exports = mongoose.model('Order', OrderSchema, 'order');

// 定义order_itemSchema
var OrderItemSchema = mongoose.Schema({
  order_id: String,
  title: String,
  price: Number,
  num: Number
});

module.exports = mongoose.model('OrderItem', OrderItemSchema, 'order_item');

// 聚合查询
var OrderModel = require('./models/order');

OrderModel.aggregate([
  {
    $lookup: { // 连接操作符，用于连接同一个数据库中另一个集合，并获取指定的文档，类似于populate
      from: 'order_item', // 需要关联的集合名
      localField: 'order_id', // 本集合需要查找的字段
      foreignField: 'order_id', // 另外一个集合需要关联的字段
      as: 'items' // 输出的字段名
    }
  }, {
    $match: { 'all_price': {$gte: 90} } // $match：用于过滤数据，只输出符合条件的文档
  }
], function(err, docs) {
  if (err) console.log(err);
  console.log(JSON.stringify(docs));
})

```