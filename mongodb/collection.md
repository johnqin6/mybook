# 集合的创建和删除  

## 集合的创建  
- 语法：  
> db.createCollection(name, options)    
参数说明：
  + name: 要创建的集合名称  
  + options: 可选参数，指定有关内存大小及索引的选项  

options 参数如下：  
        
| 字段 | 类型 | 描述  |      
|------|-----|-------|          
|capped|布尔 |(可选)，true: 创建固定集合，固定集合指有着固定大小的集合，当达到最大值时，会自动覆盖最早的文档，当该值为true时，必须指定size参数 |       
|autoIndexId|布尔| (可选) true: 自动在_id字段创建索引，默认为false |     
|size|数值| (可选) 为固定集合制定一个最大值(以字节记)，如果capped为true, 也需要指定该字段|         
|max |数值| (可选) 指定固定集合中包含文档的最大数值 |       

- 实例：
在test数据库中创建demo集合  
```
> use test 
switched to db test  
> db.createCollection("demo") 
{"ok": 1}
```  
查看已有集合，可使用`show collections`或`show tables`命令 ： 
```
> show collections 
demo
```    
创建固定集合mycol, 整个集合空间大小6142800kb,文档最大个数为10000个。
```
> db.createCollection("mycol", { capped: true, autoIndexId: true, size: 6142800, max: 10000}) 
{"ok": 1}
```  
在mongodb中，你不需要创建集合，当你插入一些文档时，mongodb会自动创建集合     
```
> db.mycol2.insert({"name": "john"})
> show collections
mycol2
```   

## 删除集合
- 语法：
> db.collection.drop()   
参数说明：无     
返回值：删除成功，drop()方法返回true,否则返回false.   

- 实例：
删除集合mycol2   
```
> db.mycol2.drop()
true
```   

