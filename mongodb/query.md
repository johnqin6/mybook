# 文档操作(查询)  

## find() 方法
查询方法find()以非结构化的方式显示所有文档    
- 语法：   
> db.collection.find(query, projection)     
- 参数：
  + query: 可选，使用查询操作符指定查询文件  
  + projection: 可选，使用投影操作符指定返回的键，查询时返回文档中所有的键值，只需省略该参数即可（默认省略）  
将col集合中所有的键值显示     
> db.col.find().pretty()    
pretty() 方法以格式化的方式来显示所有文档     
- 实例
```
> db.col.find().pretty()
{
    "_id" : ObjectId("56063f17ade2f21f36b03133"),
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

## findOne() 方法值返回一个文档  
用法与find() 相同    

## mongodb 与 RDBMS(传统SQL数据库) where 语句比较  

| 操作| 格式 | 范例 | RDBMS中的类似语句 |
|-----|-----|-------|-----------------|
|等于| {key:value}| db.col.find({"name": "john"}).pretty()| where by = 'john'|       
|小于|{key:{$lt:value}}|db.col.find({"age": {$lt: 30}}).pretty()| where age < 30 |   
|小于或等于|{key:{$lte:value}}|db.col.find({"age": {$lte: 30}}).pretty()|where age <= 30|        
|大于|{key:{$gt:value}}|db.col.find({"age": {$gt: 30}}).pretty()| where age > 30 |     
|大于或等于|{key:{$gte:value}}|db.col.find({"age": {$gte: 30}}).pretty()| where age >= 30|  
|不等于|{key:{$ne:value}}|db.col.find({"age"}: {$ne: 30}).pretty()| where age != 30|  
|并且|{key1:value1,key2:value2}|db.col.find({"sex": 1,"age": 20})| where sex = 1 AND age = 20| 
|或|{$or: [key1:value1,key2:value2]}|db.col.find({$or: [{"name": "john"},{"age": 20}]})| where name = 'john' OR age = 20 |       
|AND和OR联用|{key1:value1,$or:[{key2:value2},{key3:value3}]}|db.col.find({"age": {$gt: 30},$or:[{"name": "john"},{"sex": 1}]})| where age > 30 AND (name = 'john' OR sex = 1)|  
|10 < value < 50|{key: {$gt: value1,$lt: value2}}|db.col.find({age: {$gt: 10, $lt: 50}})| where age > 10 AND age <50 |        
|模糊查询(含有)|{key: /要匹配的字符/}|db.col.find({title: /教/})| where title like '%教%'|    
|模糊查询(以什么开头)|{key: /^字符/}|db.col.find({title: /^教/})| where title like '教%'|   
|模糊查询(以什么结尾)|{key: /字符$/}|db.col.find({title: /教$/})| where title like '%教'|    

## projection 参数的使用方法 
> db.collection.find(query, projection)     
如不指定projection, 则默认返回所有键，指定projection 格式如下
```
> db.collection.find(query, {title: 1, by: 1})  // inclusion模式，指定返回的键，不返回其他键  
> db.collection.find(query, {title: 0, by: 0})  // exclusion模式，指定不返回的键，返回其他键 
```
_id键默认返回，需要主动指定 _id: 0,才会隐藏   
两种模式不能混用    

若不想指定查询条件参数 query 可以 用 {} 代替，但是需要指定 projection 参数 
> db.collection.find({}, {title: 1})    

查询数据的条数
> db.collection.find().count()  
根据条件查询数据的条数
> db.collection.find({age: {$gt: 20, $lte: 50}}).count()
