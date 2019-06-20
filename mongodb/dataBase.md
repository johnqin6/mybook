# Mongodb 数据库的连接与操作 

## mongodb 数据库的开启
1. 在c盘或d盘等某一盘符新建一个`database`目录用以保存mongodb数据库
> $ mkdir database      
2. 打开cmd命令面板，开启数据库服务器
> $ mongod --dbpath D://database     
注：进行数据库操作期间该窗口不能关闭，否则数据库服务无法使用，在此窗口可以查看数据库服务器连接情况   

3. 另开一个cmd命令窗口（注意：原命令窗口一定不要关闭），输入mongo命令，进入mongodb运行环境  
> $ mongo    
4. 此时就可以用命令窗口进行数据库操作，比如查看数据库  
> $ show dbs    

## 连接mongodb数据库 
- 本地命令行连接
> use test(数据库名)   
- 使用服务端语言连接数据库
  + 标准URI连接语法：
  > mongodb://[username:password@]host1[:port1][,host2[:port2],...][/[database][?options]]    
  + mongodb:// 这是固定的格式，必须要指定  
  + username:password@ 可选项，如果设置，再连接数据库服务器
  + host1 必须的指定至少一个host,host1是这个URI唯一要填写的，它指定了要链接服务器的地址，如果要连接复制集，请指定多个主机地址
  + portX 可选的指定端口，如不填，默认为27017  
  + /database 如果指定username:password@,连接并验证登录指定数据库，若不指定，默认打开test数据库  
  + ?options 是连接选项，如果不使用/database,则前面需要加上/，所有连接选项都是键值对name=value,键值对之间通过&或;(分号)隔开  
  连接数据库实例：
  > mongodb://fred:foobar@localhost/baz  

  + 标准的连接格式包含了多个选项(options),如下所示 ：
  
|选项 | 描述 |   
|-----|------|   
|replicaSet=name| 验证replica set的名称，Impliesconnect=replicaSet. |    
|slaveOk=true/false|  ture: 在connect=direct模式下，驱动会连接第一台机器，即使这台服务器不是主,在connect=replicaSet模式下，驱动会发送所有的写请求到主服务器并且把读取操作分布在其他从服务器。false: 在connect=direct模式，驱动会自动找寻主服务器，在connect=replicaSet模式下，驱动仅仅连接主服务器，并且所有的读写命令都连接到主服务器。|      
|safe=true/false|  true: 在执行更新操作后，驱动都会发送getLastError命令来确保更新成功，false: 在每次更新之后，驱动不会发送getLastError来确保更新成功|    
|w=n|驱动添加{w:n}到getLastError命令，应用于safe=true |     
|wtimeoutMS=ms|驱动添加{wtimeout:ms} 到getlasterror命令. 应用于sage=true |     
|fsync=true/false|  true: 驱动{fsync: true} 到到getlasterror命令. 应用于sage=true ，false: 驱动不会添加到getLastError命令中。|    
|journal=true/false| true: 同步到journal(在提交到数据库前写入到实体中) 应用于sage=true|      
|connectTimeoutMS=ms| 可以打开连接的时间 |      
|socketTimeoutMS=ms | 发送和接受sockets的时间 |     

## 创建数据库 
- 语法： 
> use DATABASE_NAME    
如果数据库不存在，则创建数据库，否则切换到指定数据库   
- 实例：
```
> use demo  // 创建一个demo数据库 
switched to db runoob  
> db     // 显示当前数据库
demo  
> show dbs   // 查看所有数据库
admin   0.000GB
config  0.000GB
local   0.000GB
// 此时我们创建的数据库demo 并不在数据库列表中，如果要其显示，需要向demo数据库中插入一些数据 
> db.demo.insert({"name": "john"}) 
WriteResult({ "nInserted" : 1 })  
> show dbs 
admin   0.000GB
config  0.000GB
local   0.000GB
demo    0.000GB
```  
mongodb 中默认的数据库是test, 如果你没有创建新的数据库，集合将放在test数据库中  
注意：在mongodb 中，集合只有在内容插入后才会创建，即创建集合后要再插入一个文档(记录)，集合才会真正创建   

## 删除数据库 
- 语法：
> db.dropDatabase()       
删除当前数据库，默认为test, 可使用db命令查看当前数据库名    
- 实例： 
删除数据库demo  
```
> use demo    // 使用数据库 
switched to db runoob
> db.dropDatabase() // 删除数据库demo 
// 也可以指定名称删除
> db.demo.dropDatabase() 
```