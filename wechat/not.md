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