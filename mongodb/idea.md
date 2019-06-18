# MongoDB 概念解析  

## SQL 和 MongoDB 术语/概念对比 

| SQL | MongoDB | 解释/说明|  
|-----|--------|----------|   
|database|database| 数据库|   
|table|collection| 数据库表/集合|   
|row|document|数据记录行/文档|          
|column|field| 数据字段/域|             
|index | index| 索引 |        
|table joins |   | 表连接，Mongodb不支持|   
|primary key|primary key| 主键，Mongodb自动将_id字段设置为主键|   

## 数据库 
一个mongodb中可以建立多个数据库。  
mongodb的默认数据库为“db”, 该数据库存储在 data 目录中。 
mongodb的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，
不同的数据库也放置着不同的文件中。 
- 查看数据库 
```
// 首先需要进入mongo 运行环境
> $ ./mongo
MongoDB shell version: 3.0.6
connecting to: test  
// 查看数据库
> show dbs
local 0.000GB
test  0.000GB
// 连接一个指定数据库
> use test 
switched to db local
// 显示当前数据库对象或集合 
> db 
test  
```  
- 数据库名的规范  
  + 不能是空字符串 ("")
  + 不得含有''(空格)、$、\和\0(空字符) 
  + 应全部小写
  + 最多64字节  
  + 其他的任意utf-8字符串都可  

- 保留的数据库名，可直接访问这些特殊作用的数据库  
  + admin: 从权限的角度看，这是“root”数据库。将一个用户添加到这个数据库，
  这个用户自动继承所有数据库的权限，一些特定的服务器端命令也只能从这个数据库运行，
  比如列出所有的数据库或者关闭服务器。
  + local: 这个数据库永远不会被复制，可以用来存储限于本地单台服务器的任意集合。
  + config: 当mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。  

## 集合  
集合就是mongodb文档组，类似于 RDBMS (关系数据库管理系统)的表格。   
集合存在于数据库中，集合没有固定的结构，这意味着你在集合中可以插入
不同格式和类型的数据，但一般我们插入集合的数据都会有一定的关联性。   
比如，我们可以把一下不同的数据结构的文档插入到集合中

```json
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
``` 
当第一个文档插入时，集合就会被创建。 

- 合法的集合名 
    * 集合名不能是空字符串""。
    * 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
    * 集合名不能以"system."开头，这是为系统集合保留的前缀。
    * 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。　    
- capped collections 
  + Capped collections 就是固定大小的collection。
  + 它有很高的性能以及队列过期的特性(过期按照插入的顺序). 有点和 "RRD" 概念类似。
  + Capped collections 是高性能自动的维护对象的插入顺序。它非常适合类似记录日志的功能和标准的 collection 不同，你必须要显式的创建一个capped collection，指定一个 collection 的大小，单位是字节。collection 的数据存储空间值提前分配的。
  + Capped collections 可以按照文档的插入顺序保存到集合中，而且这些文档在磁盘上存放位置也是按照插入顺序来保存的，所以当我们更新Capped collections 中文档的时候，更新后的文档不可以超过之前文档的大小，这样话就可以确保所有文档在磁盘上的位置一直保持不变。
  + 由于 Capped collection 是按照文档的插入顺序而不是使用索引确定插入位置，这样的话可以提高增添数据的效率。MongoDB 的操作日志文件 oplog.rs 就是利用 Capped Collection 来实现的。
  + 要注意的是指定的存储大小包含了数据库的头信息。  

**创建一个集合并限定集合大小**    
> db.createCollection("mycoll", {capped: true, size: 10000})  

- capped collection 的特点：
  + 在 capped collection中，你能添加新的对象  
  + 能进行更新，但对象不会增加存储空间。如果增加，更新就会失败。
  + 使用capped Collection 不能删除一个文档，可以使用 drop() 方法删除collection所有的行。
  + 删除后，你必须显式地重新创建这个collection。
 
## 文档(Document) 
文档是一组键值(key-value)对(即bson)。mongodb的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系数据库有很大区别，也是mongodb非常突出的特点。

一个简单的文档如下：
```json
{"name": "john", "age": 20}
``` 
- 注意点：
  1. 文档的键/值对是有序的
  2. 文档中的值不仅可以是在字符串，还可以是其他数据类型（甚至可以是整个文档）
  3. mongodb 区分类型和大小写
  4. mongodb 的文档不能有重复的键
  5. 文档的键是字符串，除了少数例外情况，见可以使用任意utf-8字符  
- 文档键命名规范
  + 键不能含有\0(字符)。这个字符表示间的结尾。
  + .和$有特别的意义，只有特点的环境下才能使用。
  + 以下划线"_"开头的键是保留的。  

## 元数据
数据库的信息是存储在集合中，他们使用了系统的命名空间。 
> dbname.system.*    
在mongodb 数据库中名字空间 <dbname>.system.* 是包含多种系统信息的特殊集合(collection)，如下：   

|集合命名空间| 描述|
|-----|-----------|
|dbname.system.namespaces| 列出所有命名空间|
|dbname.system.indexes| 列出所有索引|
|dbname.system.profile| 包含数据库概要(profile)信息|
|dbname.system.users| 列出所有可访问数据库的用户|
|dbname.system.sources| 包含复制对端(slave)的服务器信息和状态|  

- 对于修改系统集合中的对象有如下限制。
  + 在{{system.indexes}}插入数据，可以创建索引。但除此之外该表信息是不可变的(特殊的drop index命令将自动更新相关信息)。
  + {{system.users}}是可修改的。 {{system.profile}}是可删除的。 

## mongodb 常用数据类型
|数据类型| 描述 |
|-----|--------|
|String|字符串，存储数据常用的数据类型，在Mongodb中，utf-8编码的字符串才是合法的|  
|Integer|整型数值，用于存储数值，根据服务器，可分为32位或64位| 
|Boolean|布尔值，存储布尔值（真/假）|
|Double|双精度浮点值|
|Min/Max keys| 将一个值于BSON(二进制的json)元素的最低值和最高值相对比|
|Array| 将数组或列表或多个值存储为一个键|
|Timestamp|时间戳，记录文档修改或添加的具体时间|
|Object|用于内嵌文档|
|Null|创建空值|
|Symbol| 符号，等同于字符串类型，不同的是，他一般采用特殊符号类型的语言|
|Date| 日期时间，用unix时间格式存储当前时日期或时间，也可指定自己的日期时间：
创建Date对象，传入年月日信息| 
|ObjectID|对象ID，创建文档的ID|
|Binary Data| 二进制数据|
|Code|代码类型，在文档中存储javascript代码| 
|Regular expression| 正则表达式类型|   

- ObjectId 
ObjectId 类似唯一主键，可以很快生成和排序，包含12 bytes, 含义是：
* 前4个字符表示创建unix时间戳，格林尼治时间UTC时间，比北京时间晚了8小时。
* 接下来的3个字节是机器标识符
* 紧接的两个字节由进程id组成PID
* 最后三个字节是随机数 
mongodb 中存储的文档必须有一个_id键，这个键的值可以是任何类型的值，默认是Objectid对象。   
由于Objectid已保存了创建的时间戳，所以你不必在为你的文档保存时间戳字段，可通过getTimestamp函数来获取文档的创建时间。   
```
> var newObject = ObjectId()
> newObject.getTimestamp()
ISODate("2019-11-25T07:21:10Z")
```
ObjectId 转为字符串
```
> newObject.str
5a1919e63df83ce79df8b38f
```  

- 时间戳
BSON 的时间戳类型用于mongodb内部使用，与不同的日期类型不相干，时间戳值是一个64位的值
  + 前32位是一个 time_t值（玉unix新纪元相差的秒数）
  + 后32位是在某秒中操作的一个递增的序数。 
  + 在单个 mongod 实例中，时间戳值通常是唯一的。

在复制集中， oplog 有一个 ts 字段。这个字段中的值使用BSON时间戳表示了操作时间。    
**注意: BSON 时间戳类型主要用于 MongoDB 内部使用。在大多数情况下的应用开发中，你可以使用 BSON 日期类型。**     

- 日期  
表示当前距离UNIX新纪元（1970年1月1日）的毫秒数 
```
> var mydate1 = new Date() //格林尼治时间
> mydate1
ISODate("2018-03-04T14:58:51.233Z")
> typeof mydate1
object 

> var mydate2 = ISODate() //格林尼治时间
> mydate2
ISODate("2018-03-04T15:00:45.479Z")
> typeof mydate2
object
```  
