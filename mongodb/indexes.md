# mongodb 索引  

索引通常能极大的提高查询效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。
这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的。
索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构   

## createIndex() 方法 
- 作用：创建索引  
- 语法：
> db.collection.createIndex(keys, options)   
语法中 key值为要创建的索引字段，1为指定按升序创建索引，-1为降序 。
- 实例
> db.col.createIndex({"title": 1})   
createIndex()方法中也可为多个字段创建索引（关系型数据库称之为复合索引）   
> db.col.createIndex({"title": 1,"description": -1})     

- createIndex() 接收可选参数：

|参数| 类型| 描述 |   
|----|----|-------|    
|background|Boolean| 将索引过程中阻塞其他数据库操作，background可指定以后台方式创建索引，默认值为false|   
|unique|Boolean| 建立的索引是否唯一，true: 创建唯一索引，默认为false |   
|name|string| 索引的名称，如未指定，mongodb的通过连接索引的字段名和排序顺序生成一个所引名称|    
|sparse|Boolean| 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 false.|     
|expireAfterSeconds|integer| 指定一个以秒为单位的数值，完成ttl设定，设定集合的生存时间|   
|v| index version| 索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本|   
|weights|document| 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重|  
|default_language| string| 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语|   
|language_override|string| 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language|   

- 实例 
在后台创建索引    
> db.col.createIndex({open: 1, close: 1}, {background: true})   
通过在创建索引时加 background: true的选项，让创建工作在后台执行    

## 集合索引的其他操作  

- 查看集合索引
> db.col.getIndexes()    
- 查看集合索引大小   
> db.col.totalIndexSize()  
- 删除集合所有索引
> db.col.dropIndexes()   
- 删除集合指定索引
> db.col.dropIndex("索引名称")  

利用 TTL 集合对存储的数据进行失效时间设置：经过指定的时间段后或在指定的时间点过期，MongoDB 独立线程去清除数据。类似于设置定时自动删除任务，可以清除历史记录或日志等前提条件，设置 Index 的关键字段为日期类型 new Date()。

例如数据记录中 createDate 为日期类型时：

 * 设置时间180秒后自动清除。
 * 设置在创建记录后，180 秒左右删除。
> db.col.createIndex({"createDate": 1},{expireAfterSeconds: 180})    
由记录中设定日期点清除。

设置 A 记录在 2019 年 1 月 22 日晚上 11 点左右删除，A 记录中需添加 "ClearUpDate": new Date('Jan 22, 2019 23:00:00')，且 Index中expireAfterSeconds 设值为 0。

> db.col.createIndex({"ClearUpDate": 1},{expireAfterSeconds: 0})
其他注意事项:

 * 索引关键字段必须是 Date 类型。
 * 非立即执行：扫描 Document 过期数据并删除是独立线程执行，默认 60s 扫描一次，删除也不一定是立即删除成功。
 * 单字段索引，混合索引不支持