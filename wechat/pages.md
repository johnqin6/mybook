# 普通页面

## .json 页面配置
在全局的window窗口的配置，页面中都可以使用，在此就不在赘述,页面中的窗口配置，无需放在window对象中，并且优先级大于全局的配置，即会覆盖全局的配置。

页面配置独特的属性
|属性	|类型	|默认值	|描述|
|----|-----|-----|-----|
|disableScroll|boolean|false| 设置为 true 则页面整体不能上下滚动。只在页面配置中有效，无法在 app.json 中设置|
|disableSwipeBack| boolean| false|禁止页面右滑手势返回|
|usingComponents| Object| 否| 页面自定义组件配置|

### 配置示例
```json
{
  "navigationBarBackgroundColor": "#ffffff",
  "navigationBarTextStyle": "black",
  "navigationBarTitleText": "微信接口功能演示",
  "backgroundColor": "#eeeeee",
  "backgroundTextStyle": "light"
}
```

## .js 页面中的逻辑层
### Page()
注册小程序中的一个页面。接受一个 Object 类型参数，其指定页面的初始数据、生命周期回调、事件处理函数等 

|属性	|类型	|默认值	|必填	|说明|
|-----|---|----|---|-------|
|data| Object| | | 页面的初始数据|
|onLoad|function| | | 生命周期回调--监听页面加载|
|onShow|function| | | 生命周期回调--监听页面显示|
|onReady|function| | | 生命周期回调--监听页面初次渲染完成|
|onHide|function| | | 生命周期回调--监听页面隐藏|
|onUnload|function| | | 生命周期回调--监听页面卸载|
|onPullDownRefresh|function| | | 监听用户下拉动作|
|onReachBottom|function| | | 页面上拉触底事件的处理函数|
|onShareAppMessage|function| | | 用户点击右上角转发|
|onPageScroll|function| | | 页面滚动触发事件的处理函数|
|onResize|function| | | 页面尺寸改变时触发|
|onTabItemTap|function| | | 当前时tab页时，点击tab时触发|

### 示例代码
```javascript
//index.js
Page({
  data: {
    text: "This is page data."
  },
  onLoad: function(options) {
    // 页面加载时
  },
  onReady: function() {
    // 页面初次渲染
  },
  onShow: function() {
    // 页面显示
  },
  onHide: function() {
    // 页面隐藏
  },
  onUnload: function() {
    // 页面卸载
  },
  onPullDownRefresh: function() {
    // 下拉刷新时
  },
  onReachBottom: function() {
    // 上拉触底时（多用于上拉加载更多）
  },
  onShareAppMessage: function () {
    // 应用信息分享时
  },
  onPageScroll: function() {
    // 页面滚动时
  },
  onResize: function() {
    // 页面大小变化
  },
  onTabItemTap(item) {
    console.log(item.index)
    console.log(item.pagePath)
    console.log(item.text)
  },
  // Event handler.
  viewTap: function() {
    this.setData({
      text: 'Set some data for updating view.'
    }, function() {
      // this is setData callback
    })
  },
  customData: {
    hi: 'MINA'
  }
})
data
```

### 组件事件处理函数
Page 中还可以定义组件事件处理函数。在渲染层的组件中加入事件绑定，当事件被触发时，就会执行 Page 中定义的事件处理函数。

```wxml
<view bindtap="viewTap"> click me </view>
```
```javascript
Page({
    viewTap(){
        console.log('view tap');
    }
})
```
### Page.route
到当前页面的路径，类型为String。  
```javascript
Page({
    onShow(){
        console.log(this.route)
    }
})
```

### Page.prototype.setData(Object data, Function callback)
setData 函数用于将数据从逻辑层发送到视图层（异步），同时改变对应的 this.data 的值（同步）。

###### 参数说明
|字段	|类型	|必填	|描述|
|----|----|---|------|
|data| Object| 是| 这次要改变的数据|
|callback| Function| 否| setData引起的界面更新渲染完毕后的回调函数|

`Object` 以 `key: value` 的形式表示，将this.data中的key 对应的值变成value.   
其中 key 可以以数据路径的形式给出，支持改变数组中的某一项或对象的某个属性，如 array[2].message，a.b.c.d，并且不需要在 this.data 中预先定义。  
注意： 
1. 直接修改`this.data` 而不调用`this.setData `是无法改变页面的状态的，还会造成数据的不一致。
2. 仅支持设置可json化的数据。
3. 单次设置的数据不能超过1024kB，请尽量避免一次设置过多的数据。
4. 请不要把 data 中任何一项的 value 设为 undefined ，否则这一项将不被设置并可能遗留一些潜在问题。

示例
```javascript
Page({
    data: {
        value: ''
    },
    onShow(){
        this.setData({
            value: 'hello world'
        });
        this.setData({
            value: 'hello',() => {

            }
        })
    },

})
```

### PageObject[] getCurrentPages()

获取当前页面栈。数组中第一个元素为首页，最后一个元素为当前页面。

注意：

* 不要尝试修改页面栈，会导致路由以及页面状态错误。
* 不要在 App.onLaunch 的时候调用 getCurrentPages()，此时 page 还没有生成。
