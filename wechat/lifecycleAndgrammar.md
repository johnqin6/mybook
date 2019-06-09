# 生命周期

![生命周期](https://res.wx.qq.com/wxdoc/dist/assets/img/page-lifecycle.2e646c86.png)   

# wxss样式  

WXSS (WeiXin Style Sheets)是一套样式语言，用于描述 WXML 的组件样式。

WXSS 用来决定 WXML 的组件应该怎么显示。

为了适应广大的前端开发者，WXSS 具有 CSS 大部分特性。同时为了更适合开发微信小程序，WXSS 对 CSS 进行了扩充以及修改。

与 CSS 相比，WXSS 扩展的特性有：

* 尺寸单位
* 样式导入
* 尺寸单位
rpx（responsive pixel）: 可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx。如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素。 

## 内联样式(动态赋值)  

``` <view style="color:{{color}};" /> ```

## 基本语法

1. 列表渲染 
```wxml
<view wx:if="{{length > 5}}">1</view>
<view wx:if="{{length > 2}}">2</view>
<view wx:else>3</view>
```  
block wx:if 
因为wx:if是一个控制属性，需要将它添加到一个标签上。但是如果我们想一次性判断多个组件标签，我们可以使用一个`<block/>`标签将多个组件包装起来，并在上边使用wx:if控制属性。 
```wxml
<block wx:if="{{true}}">
  <view> view1 </view>
  <view> view2 </view>
</block>
```  
注意：`<block/>`并不是一个组件，它仅仅是一个包装元素，不会在页面中做任何渲染，只接受控制属性。

