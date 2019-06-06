# 全局页面

## app.json 全局配置页面

小程序根目录下的 app.json 文件用来对微信小程序进行全局配置，决定页面文件的路径、窗口表现、设置网络超时时间、设置多 tab 等。   

### 配置项
|属性| 类型| 必填| 描述|
|----|----|-----|----|
|pages| string[] | 是 | 页面路路径列表，第一项为初始页面|
|window| Object| 否| 全局的默认窗口表现|
|tabBar| Object| 否| 控制底部tab栏|
|networkTimeout| Object| 否| 网络超时时间|
| debug| boolean| 否| 是否开启 debug 模式，默认关闭|
|functionalPages | boolean| 否| 是否开启插件功能页,默认关闭|
|subpackages|Object[]|否| 分包结构配置|
|workers | string| 否| Worker 代码放置的目录|
|requiredBackgroundModes| string[]|否| 需要在后台使用的能力，如「音乐播放」|
|plugins| Object| 否| 使用的插件|
|preloadRule| Object| 否| 分包预下载规则|
|resizable| boolean| 否| iPad 小程序是否支持屏幕旋转，默认关闭|
|navigateToMiniProgramAppIdList| string[]| 否| 需要跳转的小程序列表，详见 |
|usingComponents| Object| 否| 全局自定义组件配置|
|permission| Object| 否| 小程序接口权限相关设置|
|sitemapLocation| String| 是| 指明 sitemap.json 的位置,sitemap.json用于配置小程序及其页面是否允许被微信索引,没有默认所有页面都允许被索引|

### 全局配置的例子
```json
{
  "pages": ["pages/index/index", "pages/logs/index"],
  "window": {
    "navigationBarTitleText": "Demo"
  },
  "tabBar": {
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页"
      },
      {
        "pagePath": "pages/logs/logs",
        "text": "日志"
      }
    ]
  },
  "networkTimeout": {
    "request": 10000,
    "downloadFile": 10000
  },
  "debug": true,
  "navigateToMiniProgramAppIdList": ["wxe5f52902cf4de896"]
}

```
### 常用配置项

* window
用于设置小程序的状态栏、导航条、标题、窗口背景色。
|属性| 类型| 默认值| 描述|
|----|-----|-----|-------|
|navigationBarBackgroundColor| HexColor| #000000| 导航栏背景颜色|
|navigationBarTextStyle| string| white| 导航栏标题颜色，仅支持black/white|
|navigationBarTitle| string| | 导航栏标题文字|
|navigationStyle| string| default| default 默认样式custom 自定义导航栏，只保留右上角胶囊按钮|
|backgroundColor| HexColor| #ffffff| 窗口的背景色|
|backgroundTextStyle|string| dark| 下拉 loading 的样式，仅支持 dark / light|
|backgroundColorTop| string| #ffffff| 顶部窗口的背景色，仅 iOS 支持|
|backgroundColorBottom| string| #ffffff| 底部窗口的背景色，仅 iOS 支持|
|enablePullDownRefresh| boolean| false| 是否开启全局的下拉刷新|
|onReachBottomDistance| number| 50| 页面上拉触底事件触发时距页面底部距离，单位为 px|
|pageOrientation| string| protrait|屏幕旋转设置，支持 auto / portrait / landscape|

    * HexColor(十六进制颜色值)，如"#ff00ff"

* tabBar
如果小程序是一个多 tab 应用（客户端窗口的底部或顶部有 tab 栏可以切换页面），可以通过 tabBar 配置项指定 tab 栏的表现，以及 tab 切换时显示的对应页面。

|属性|	类型	|必填	|默认值	|描述|
|----|------|-----|---|-----------|	
|color|	HexColor|	是|	tab 上的文字默认颜色，仅支持十六进制颜色|	
|selectedColor|	HexColor|	是|	tab 上的文字选中时的颜色，仅支持十六进制颜色|	
|backgroundColor|	HexColor	|是|tab 的背景色，仅支持十六进制颜色|	
|borderStyle	|string	|否|black|	tabbar 上边框的颜色， 仅支持 black / white|	
| list	|Array	|是	|	tab 的列表，详见 list 属性说明，最少 2 个、最多 5 个 tab|	
|position	|string	|否	|bottom	|tabBar 的位置，仅支持 bottom / top	|
|custom	|boolean	|否	|false|	自定义 tabBar|
  * list属性
|属性	|类型	|必填	|说明|
|----|----|---|----|
|pagePath|	string|	是	|页面路径，必须在 pages 中先定义|
|text|	string|	是|	tab 上按钮文字|
|iconPath	|string	|否	|图片路径，icon 大小限制为 40kb，建议尺寸为 81px * 81px，不支持网络图片。
当 position 为 top 时，不显示 icon。
selectedIconPath	string	否	选中时的图片路径，icon 大小限制为 40kb，建议尺寸为 81px * 81px，不支持网络图片。
当 position 为 top 时，不显示 icon。|

* networkTimeout
各类网络请求的超时时间，单位均为毫秒。

|属性|	类型|	必填|	默认值|	说明|
|----|-----|----|-----|------|
|request|number	|否|	60000|	wx.request 的超时时间，单位：毫秒。|
|connectSocket|	number	|否	|60000|	wx.connectSocket 的超时时间，单位：毫秒。|
|uploadFile|	number	|否	|60000|	wx.uploadFile 的超时时间，单位：毫秒。|
|downloadFile|	number|	否|	60000|	wx.downloadFile 的超时时间，单位：毫秒。|

## app.js 应用入口逻辑
### App()
注册小程序。接受一个 Object 参数，其指定小程序的生命周期回调等。
App() 必须在 app.js 中调用，必须调用且只能调用一次。不然会出现无法预期的后果。

#### 参数
|属性|	类型|	默认值|	必填|	说明|
|----|---|----|----|----|-----|
|onLaunch| function | 否| 生命周期回调---监听小程序初始化,全局只触发一次,也可以使用 wx.getLaunchOptionsSync 获取|
|onShow| function| 否| 生命周期回调---监听小程序启动或切前台,也可以使用 wx.onAppShow 绑定监听|
|onHide| function| 否| 生命周期回调——监听小程序切后台,也可以使用 wx.onAppHide 绑定监听|
|onError|	function|		|否	|错误监听函数,也可以使用 wx.onError 绑定监听|
|onPageNotFound|function|		|否	|页面不存在监听函数|
|globalData| |否|存储全局变量|
|其他	|any|否	|开发者可以添加任意的函数或数据变量到 Object 参数中，用 this 可以访问|

```javascript
App({
  onLaunch (options) {
    // Do something initial when launch.
  },
  onShow (options) {
    // Do something when show.
  },
  onHide () {
    // Do something when hide.
  },
  onError (msg) {
    console.log(msg)
  },
  globalData: 'I am global data'
})

```

### getApp();
获取到小程序全局唯一的 App 实例,在普通页面调用  
实例
> let app = getApp()  

注意
* 不要在定义于 App() 内的函数中，或调用 App 前调用 getApp() ，使用 this 就可以拿到 app 实例。
* 通过 getApp() 获取实例之后，不要私自调用生命周期函数。