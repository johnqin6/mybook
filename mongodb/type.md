# mongodb $type 操作符   

## 描述 
$type操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果：   
类型如下：    

|类型| 数字 |备注|     
|----|-----|----|   
|Double|1  |   |    
|String|2  |   |   
|Object|3  |   |   
|Array|4   |   |   
|Binary data|5| |   
|undefined|6 | 已废弃|   
|Object id|7 |    |    
|Boolean| 8  |    |   
|Date|9   |   |   
|Null|10  |   |   
|Regular Expression| 11|   |   
|javascript|13|   |  
|Symbol| 14 |    |   
|javaScript(with scope)| 15|  |   
|32-bit integer| 16 |   |   
|Timestamp| 17  |   |    
|64-bit integer|18 |   |    
|Min key| 255| Query with -1|  
|Max key| 127|    |   

使用实例：   
```
> db.col.find({title: {$type: 2}})  或  db.col.find({title: {$type: 'string'}})
```   
