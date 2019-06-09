# 事件

## 什么是事件 
----------
* 事件是视图层到逻辑层的通讯方式。  
* 事件可以将用户的行为反馈到逻辑层进行处理。  
* 事件可以绑定在组件上，当达到触发事件，就会执行逻辑层中对应的事件处理函数。  
* 事件对象可以携带额外信息，如id, dataset, touches。  

## 事件的使用
* 在组件中绑定一个事件处理函数  
如`bindtap`，当用户点击该组件的时候会在该页面对应的Page中找到相应的事件处理函数。  
```wxml
<view id="tapTest" data-hi="WeChat" bindtap="tapName"> Click me! </view>
```  
* 在相应的Page定义中写上相应的事件处理函数，参数是event。  
```javascript
Page({
  tapName: function(event) {
    console.log(event)
  }
})
```  
* 可以看到log出来的信息大致如下
```json
{
"type": "tap",
"timeStamp":895,
"target": {
  "id": "tapTest",
  "dataset": {
   "hi": "WeChat"
  }
},
"currentTarget": {
  "id": "tapTest",
  "dataset": {
    "hi": "WeChat"
  }
},
"detail": {
  "x":53,
  "y":14
},
"touches": [{
  "identifier":0,
  "pageX":53,
  "pageY":14,
  "clientX":53,
  "clientY":14,
}],
"changedTouches": [{
  "identifier":0,
  "pageX":53,
  "pageY":14,
  "clientX":53,
  "clientY":14,
}],
}
```  
### 微信小程序的事件方法(冒泡事件)

| 类型 | 触发条件 |    
|------|---------|    
|touchstart| 手指触摸动作开始|   
|touchmove| 手指触摸后移动| 
|touchcancel| 手指触摸动作被打断，如来电提醒，弹窗|       
|touchend| 手指触摸动作结束|     
|tap| 手指触摸后马上离开|    
|longtap| 手指触摸后，超过350ms再离开|    
注：除上表之外的其他组件自定义事件都是非冒泡事件，如`<form/>`的submit事件，`<input/>`的input事件，`<scroll-view/>`的scroll事件  

### 事件绑定 
事件绑定的写法同组件的属性，以key、value的形式。  
* key以`bind`或`catch`开头，然后跟上事件的类型，如`bindtap`, `catchtouchstart`
* value是一个字符串，需要在对应的Page中定义同名的函数。不然当触发事件的时候会报错   
bind事件绑定不会阻止冒泡事件向上冒泡，catch事件绑定可以阻止冒泡事件向上冒泡。

如在下边这个例子中，点击inner view会先后触发`handleTap3`和`handleTap2`(因为tap事件会冒泡到middle view，而middle view阻止了tap事件冒泡，不再向父节点传递)，点击middle view会触发`handleTap2`，点击outter view会触发`handleTap1`。   
```wxml
<view id="outter" bindtap="handleTap1">
  outer view
  <view id="middle" catchtap="handleTap2">
    middle view
    <view id="inner" bindtap="handleTap3">
      inner view
    </view>
  </view>
</view>
```  

### 事件对象  
如无特殊说明，当组件触发事件时，逻辑层绑定该事件的处理函数会收到一个事件对象。

BaseEvent基础事件对象属性列表： 

| 属性 | 类型 | 说明 |     
|----|----|-----|    
| type | String | 事件类型 |    
| timeStamp | Integer | 事件生成时的时间戳|     
| target |Object|触发事件的组件的一些属性值集合|    
| currentTarget| Object| 当前组件的一些属性值集合|  

CustomEvent 自定义事件对象属性列表（继承 BaseEvent）：  

|属性|类型|说明|    
|----|----|-----|    
|detail|Object| 额外的信息|    

TouchEvent 触摸事件对象属性列表（继承 BaseEvent） 
 
|属性|类型|说明|     
|----|----|-----|    
|touches|Array|触摸事件，当前停留在屏幕中的触摸点信息的数组|    
|changedTouches|Array| 	触摸事件，当前变化的触摸点信息的数组|    
特殊事件：`<canvas/>`中的触摸事件不可冒泡，所以没有 currentTarget。   

**type**   
通用事件类型       
**timeStamp**      
该页面打开到触发事件所经过的毫秒数。  

**target**       
触发事件的源组件。  

|属性|类型|说明|    
|----|----|-----|    
|id|String|事件源组件的id|     
|tagName| String| 当前组件的类型|     
|dataset| Object| 事件源组件上由`data-`开头的自定义属性组成的集合|     

**currentTarget** 
事件绑定的当前组件。   

|属性|类型|说明|    
|----|----|-----|     
|id|String|当前组件的id|      
|tagName| String| 当前组件的类型|     
|dataset| Object| 当前组件上由`data-`开头的自定义属性组成的集合|   
   
说明： target 和 currentTarget 可以参考上例中，点击 inner view 时，`handleTap3` 收到的事件对象 target 和 currentTarget 都是 inner，而 `handleTap2` 收到的事件对象 target 就是 inner，currentTarget 就是 middle。  

**dataset**
在组件中可以定义数据，这些数据将会通过事件传递给 SERVICE。书写方式：以data-开头，多个单词由连字符-链接，不能有大写(大写会自动转成小写)如`data-element-type`，最终在 `event.target.dataset` 中会将连字符转成驼峰`elementType`。   
```wxml
<view data-alpha-beta="1" data-alphaBeta="2" bindtap="bindViewTap"> DataSet Test </view>
```  
```javascript
Page({
  bindViewTap:function(event){
    event.target.dataset.alphaBeta === 1 // - 会转为驼峰写法
    event.target.dataset.alphabeta === 2 // 大写会转为小写
  }
})
```  
**touches**    

touches 是一个数组，每个元素为一个 Touch 对象（canvas 触摸事件中携带的 touches 是 CanvasTouch 数组）。 表示当前停留在屏幕上的触摸点。   

**Touch对象**      

|属性|类型|说明|    
|----|----|-----|   
|identifier|Number| 触摸点的标识符|    
|pageX,pageY|Number| 距离文档左上角的距离，文档的左上角为原点 ，横向为X轴，纵向为Y轴|    
|clientX,clientY|Number|距离页面可显示区域（屏幕除去导航条）左上角距离，横向为X轴，纵向为Y轴|    

**CanvasTouch 对象**    

|属性|类型|说明|    
|----|----|-----|    
|identifier|Number| 触摸点的标识符|    
|x,y|Number| 距离 Canvas 左上角的距离，Canvas 的左上角为原点 ，横向为X轴，纵向为Y轴|     

**changedTouches**

changedTouches 数据格式同 touches。表示有变化的触摸点，如从无变有（touchstart），位置变化（touchmove），从有变无（touchend、touchcancel）  
**detail**

自定义事件所携带的数据，如表单组件的提交事件会携带用户的输入，媒体的错误事件会携带错误信息，详见组件定义中各个事件的定义。

点击事件的detail 带有的 x, y 同 pageX, pageY 代表距离文档左上角的距离。