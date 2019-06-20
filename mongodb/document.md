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
- update()方法 
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
    * update: 

