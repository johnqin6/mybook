# Limit，Skip方法和sort()方法

## limit方法   
- 作用： 读取指定数量的数据记录  
- 语法： 
> db.collection.find().limit(number)   
- 实例：  
获取两条数据
```
> db.col.find().limit(2)
```
注：如果不指定参数,则limit()方法显示集合中的所有的数据   

## Skip() 方法
- 作用：跳过指定数量的数据 
- 语法：
> db.collection.find().skip(start)   
- 实例
显示第二条及以后的文档数据
```
> db.col.find().skip(2)   
```

## limit()和skip() 结合使用 
读取集合中第10条以后的100条数据    
> db.collection.find().skip(10).limit(100)   

补充说明skip和limit方法只适合小数据量分页，如果是百万级效率就会非常低，因为skip方法是一条条数据数过去的，建议使用where_limit     

结合时间戳进行查询 查询第100001条数据，这条数据的Amount值是：2399927  
```
> db.test.sort({'amout': 1}).skip(100000).limit(10)  // 183ms  
> db.test.find({amount: {$gt: 2399927}}).sort('amount': 1).limit(10)  // 53ms
```   

## sort() 方法  
- 作用：对数据进行排序   
- 语法：  
> db.collection.find().sort({KEY:1})   
- 参数：
  + 1: 升序  
  + -1：降序  

- 实例
对 col结合的数据按字段age 的降序排列
```
> db.col.find().sort({"age": -1})
```  

**注: skip(),limit(),sort()一起执行时，先执行sort(),然后时skip(),最后是limit()** 
