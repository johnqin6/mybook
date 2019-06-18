# MongoDB 数据库 

MongoDB 是一个基于分布式文件存储的数据库，有C++ 语言编写。旨在为 WEB 应用
提供可扩展的高性能数据存储解决方案。  
MongoDB 是一个介于关系数据库和非关系数据库（NOSQL）之间的产品，是非关系数据库
当中功能最丰富，最像关系数据库的。 

mongoDB 存储的文档模式：
```bson
{
    name: "sue",
    age: 26,
    status: "A",
    groups: ["news", "sports"]
}
```

## NOSQL 非关系型数据库  
NOSQL(NOSQL = Not Only SQL), 即“不仅仅是SQL”，是对不同于传统的关系型数据库的数据管理系统的统称--> 非关系型数据库。  
noSQL用于超大规模数据的存储，例如谷歌或Fackbook 每天为他们的用户收集万亿比特
的数据，这些类型的数据存储不需要固定的模式，无需多于操作就可以横向扩展。  

### 为什么使用NoSQL数据库  
随着互联网的发展，用户数据成倍增加，如果对这些大量的无序的数据进行挖掘，传统的关系型数据库已经不能胜任，而NoSQL数据库能很好的处理这些大量数据。   

### NOSQL数据库与传统的SQL数据库(RDBMS)的对比 

|NoSQL| RDBMS|
|-----|------|
|代表着不仅仅是SQL|高度组织化的结构化数据|
|没有声明性查询语言|结构化查询语言（SQL）|
|没有预定义的模式|数据操纵语言，数据定义语言|
|键-值对存储，列存储，文档存储，图形存储|数据和关系都存储在单独的表中|
|最终一致性，而非ACID属性|严格的一致性|
|非结构化和不可预知的数据| 基础事务|
|GAP定理|   |
|高性能，高可用性和可伸缩性|  |   

### NoSQL的优点/缺点  
- 优点：
  + 高可扩展性
  + 分布式计算
  + 低成本
  + 架构的灵活性，半结构化数据
  + 没有复杂的关系  
- 缺点：
  + 没有标准化
  + 有限的查询功能 （到目前为止）
  + 最终一致是不直观的程序  

### NoSQL 数据库分类  

|类型 | 部分代表 | 特点|   
|-----|-------|---------|    
|列存储|Hbase,cassandra,hypertable| 顾名思义，是按列存储数据的，最大特点：方便存储结构化和半结构化数据，方便做数据压缩，对针对某一列或者某几列的查询有非常大的IO优势|  
|文档存储|mongodb,couchdb| 文档存储一般用类似json的格式存储，存储的内容是文档性的，这样也就有机会对某些字段建立索引，实现关系型数据库的某些功能|    
|key-value存储|Tokyo cabinet| 可通过key快速查询到其value,一般来说，存储不管value的格式，照单全收|   
|图存储| Neo4j,FlockDB| 图形关系的最佳存储，使用传统关系数据库的话性能低下，而且设计使用不方便|   
|对象存储| db4o,versant| 通过类似面向对象语言的语法操作数据库，通过对象的方式存取数据|    
|xml数据库| Berkeley DB XML BaseX| 高效的的存储XML数据，并支持XML的内部查询语法，比如XQuery,Xpath |   

### MongoDB 的特点

* MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。
* 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
* 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
* 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
* Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
* MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
* Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
* Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。  
* Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
* GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
* MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
* MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
* MongoDB安装简单。  

### MongoDB工具
有几种可用于MongoDB的管理工具  
- 监控   
  + mongodb 提供了网络和系统监控工具Munin, 他作为一个插件应用于Mongodb
  + Gangila 是mongodb 高性能的系统监视的工具，也是一个插件应用
  + 基于图形界面的开源工具Cacti, 用于查看cpu负载，网络带宽利用率，他提供了一个应用于Mongodb的插件  
- GUI 
  + Fang of Mongo – 网页式,由Django和jQuery所构成。
  + Futon4Mongo – 一个CouchDB Futon web的mongodb山寨版。
  + Mongo3 – Ruby写成。
  + MongoHub – 适用于OSX的应用程序。
  + Opricot – 一个基于浏览器的MongoDB控制台, 由PHP撰写而成。
  + Database Master — Windows的mongodb管理工具
  + RockMongo — 最好的PHP语言的MongoDB管理工具，轻量级, 支持多国语言  

## 安装
请自行前往官网安装下载 [传送门](https://www.mongodb.com/)  
