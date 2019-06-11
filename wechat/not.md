# 小程序开发过程遇到的问题和注意事项

1. 调用wx.getlocation()需先在app.json中进行配置接口权限(permission)

示例：
```json
{
  "pages": ["pages/index/index"],
  "permission": {
    "scope.userLocation": {
      "desc": "你的位置信息将用于小程序位置接口的效果展示"
    }
  }
}
```
2. 微信小程序的data中无法拿到this 

3. 使用微信小程序地图组件时，使用gcj02的定位方式，中心点才能对齐

实例：
```javascript
wx.getLocation({ //调用前需在app.json进行权限配置（permission）
    type: 'gcj02',
    success: (res) => {

    } 
})
``` 
4. 使用不随地图移动控件时需先获取地图上下文

示例：返回定位点
```wxml
<map id="map" longitude="{{longitude}}" latitude="{{latitude}}" controls="{{controls}}" scale="14" 
  class="map" bindcontroltap="controlback" show-location></map>
```

```javascript
Page({
    //页面首次渲染时
    onReady() {
        this.mapCtx = wx.createMapContext('map'); //获得地图上下文
    },
    //点击图标返回
    controlback(e) {
        this.mapCtx.moveToLocation();
    },
});

```

5. 点击不随地图移动控件失效   
原因：微信开发工具对分辨率处理有bug，
解决方法：将电脑分辨率调到100%；
步骤：点击鼠标右键 --> 显示设置 --> 显示 --> 缩放与布局设置为100%

6. 事件函数如何传参
微信小程序的事件函数无法直接传参  
解决方法：在元素上自定义属性(自定义属性以data-开头), 再在函数中用e.target.dataset获得值;


```html
<view data-id="{{id}}" bindtap="bindtap"></view>
```

```javascript
Page({
  bindtap(e) {
    let id = e.target.dataset.id;
  }
})
```

7. 微信小程序如何操作元素
开发过程也有可能要操作dom元素，这一点小程序也想到了，提供了一些API帮助我们操作dom。   
wx.createSelectorQuery()用以返回一个 SelectorQuery 对象实例为操作dom元素做准备，
在自定义组件使用有所区别，用this.createSelectorQuery()。  

以获得dom元素的宽为例：  
```wxml
<view id="text">hello</view>
```
```javascript
Page({
  onShow(){
    var query = wx.createSelectorQuery();
    //select() 相当于原生的querySelect(),用以拿到id或class标识的元素
    query.select('#text').boundingClientRect(function(rect){
      rect.id      // 节点的ID
      rect.dataset // 节点的dataset
      rect.left    // 节点的左边界坐标
      rect.right   // 节点的右边界坐标
      rect.top     // 节点的上边界坐标
      rect.bottom  // 节点的下边界坐标
      rect.width   // 节点的宽度
      rect.height  // 节点的高度
    }).exec();  //exec()执行所有的请求，请求结果按请求次序构成数组
  }
})
```  
详细使用可以前往小程序文档--> API --> WXML查看 [传送门](https://developers.weixin.qq.com/miniprogram/dev/api/wxml/wx.createSelectorQuery.html)
