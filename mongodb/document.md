# 文档的操作(插入，更新，删除)  

## 文档的插入  

- mongodb 使用insert() 或 save() 方法向集合中插入文档，语法：
> db.collection_name.insert(document)     
- 实例： 
1. 以下文档存储到test数据库的col集合中  
```
> db.col.insert({ title: '插入文档',
  description: '插入一个文档',
  by: '文档',
  url: 'http://localhost',
  likes: 100
})
``` 
以上实例中 col 是我们的集合名，如果该集合不在该数据库中， MongoDB 会自动创建该集合并插入文档   
2. 查看已插入文档  
```
> db.col.find() 
{ "_id" : ObjectId("5d0aff18e35b8a814edc1225"), "title" : "插入文档", "description" : "插入一个文档", "by" : "文档", "url" : "http://localhost", "likes" : 100 }
```
3. 也可以将数据定义为一个变量   
```
> document=({title: 'MongoDB 教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
});
```  
执行结果如下 ：   
```json
{
    "title" : "MongoDB 教程",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "菜鸟教程",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "database",
            "NoSQL"
    ],
    "likes" : 100
}
```
4. 执行插入操作 
```
> db.col.insert(document)
WriteResult({ "nInserted" : 1 })
```  
插入文档你也可以使用 db.col.save(document) 命令。如果不指定 _id 字段 save() 方法类似于 insert() 方法。如果指定 _id 字段，则会更新该 _id 的数据。   

5. 3.2版本后还有几种语法用于插入文档  
- db.collection.insertOne();向指定集合中插入一条文档数据
- db.collection.insertMany(); 向指定集合插入多条文档数据
```
// 插入单条数据
> var document = db.collection.insertOne({"a": 3})
> document
{
    "acknowledged" : true,
    "insertedId" : ObjectId("571a218011a82a1d94c02333")
}
# 插入多条数据
> res = db.collection.insertMany([{b: 3},{c: 4}])
> res 
{
    "acknowledged" : true,
    "insertedIds" : [
            ObjectId("571a22a911a82a1d94c02337"),
            ObjectId("571a22a911a82a1d94c02338")
    ]
}
```  
6. 一遍历数组的方式插入多条数据   
```
# 1. 先创建数组
var arr = [];
# 2. 将数据放在数组中
for (var i = 1; i <= 2000; i++) {
    arr.push({num: i})
}
# 3. 一次insert到集合中
db.numbers.insert(arr);
``` 

## 文档的更新 
mongodb 使用update()和save()方法来更新集合中的文档 

### update()方法 
  + update()方法用于更新已存在的文档，语法格式如下：   
  ```
  db.collection.update(
      <query>,
      <update>,
      {
          upsert: <boolean>,
          multi: <boolean>,
          writeConcern: <document>
      }
  )
  ```  
  + 参数说明 
    * query: update的查询条件，类似sql update查询中where后面的  
    * update: update的对象和一些更新的操作符($,$inc)等，也可以理解为sql update查询中set后面的
    * upsert: 可选，这个参数是：如果不存在update的记录，是否插入objNew,true为插入，默认为false,不插入    
    * multi: 可选，mongodb 默认false,只更新找到的第一条记录，true: 就把按条件查出来的多条记录全部更新  
    * writeConern: 可选，抛出异常的级别  
- 实例： 
1. 先在集合col插入数据
```
> db.col.insert({
    title: 'MongoDB 教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
```
2. 通过update()方法进行更新 
```
> db.col.update({'title': 'mongodb 教程'}, {$set: {'title': 'mongodb'}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })   # 输出信息
> db.col.find().pretty() 
{
    "_id" : ObjectId("56064f89ade2f21f36b03136"),
    "title" : "MongoDB",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "菜鸟教程",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "database",
            "NoSQL"
    ],
    "likes" : 100
}
```  
以上语句只会修改第一条发现的文档，如修改多条相同的文档，可将multi参数设置为true  
> db.col.update({'title': 'mongodb 教程'}, {$set: {'title': 'mongodb'}}, {multi: true})    

### save() 方法  
save()方法通过传入的文档替换已有的文档，语法格式如下：
```
db.collection.save(
    <document>,
    {
        writeConcern: <document>
    }
)
```  
参数说明:     
  * document: 文档数据
  * writeConcern: 可选，抛出异常的级别  

- 实例
替换_id为 56064f89ade2f21f36b03136 的文档数据    
```
> db.col.save({
    "_id" : ObjectId("56064f89ade2f21f36b03136"),
    "title" : "MongoDB",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "Runoob",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "NoSQL"
    ],
    "likes" : 110
})
```  

### 3.2版新增的更新方法 
- db.collection.updateOne() 向指定集合更新单个文档
- db.collection.updateMany() 向指定集合更新多个文档

首先在test集合插入测试数据
```
> use test 
> db.collection.insert([
    {"name":"abc","age":"25","status":"zxc"},
    {"name":"dec","age":"19","status":"qwe"},
    {"name":"asd","age":"30","status":"nmn"},
])
```  
更新单个文档   
```
> db.collection.updateOne({"name": "abc"},{$set: {"age": 28}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.test_collection.find()
{ "_id" : ObjectId("59c8ba673b92ae498a5716af"), "name" : "abc", "age" : "28", "status" : "zxc" }
{ "_id" : ObjectId("59c8ba673b92ae498a5716b0"), "name" : "dec", "age" : "19", "status" : "qwe" }
{ "_id" : ObjectId("59c8ba673b92ae498a5716b1"), "name" : "asd", "age" : "30", "status" : "nmn" }
```
更新多个文档
```
> db.collection.updateMany({"age": {$gt: 10},{$set: {"status": "xyz"}}})
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }
> db.test_collection.find()
{ "_id" : ObjectId("59c8ba673b92ae498a5716af"), "name" : "abc", "age" : "28", "status" : "xyz" }
{ "_id" : ObjectId("59c8ba673b92ae498a5716b0"), "name" : "dec", "age" : "19", "status" : "xyz" }
{ "_id" : ObjectId("59c8ba673b92ae498a5716b1"), "name" : "asd", "age" : "30", "status" : "xyz" }
```   
移除集合中的键值对，使用$unset操作符    
语法：   
> { $unset: {<field1>: "",...}}     
如果指定的字段不存在，则不操作   
```
> db.col.update({"_id":"56064f89ade2f21f36b03136"}, {$set:{ "test2" : "OK"}})
db.col.find()

> db.col.update({"_id":"56064f89ade2f21f36b03136"}, {$unset: { "test2" : "OK"}})
```

## 删除文档
* deleteOne() 方法删除单个文档  
* deleteMany() 方法删除多个文档  
实例：  
删除集合下全部文档   
> db.inverntory.deleteMany({})        
删除status等于D的一个文档   
> db.inverntory.deleteOne({ status: "D"})    
删除status等于A的全部文档    
> db.inverntory.deleteMany({ status: "A"})

