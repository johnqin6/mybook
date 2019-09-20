# 搞懂extend和$mount原理并实现一个命令式confirm弹框组件  
我们使用组件库的时候应该都使用命令式组件,即像调用函数一样调用组件，今天我们就来了解
这种命令式组件的实现原理，并实现一个简单的命令式的弹框组件。
命令式组件调用方式如下：   
```javascript
this.$Confirm({...})
  .then(confirm => {
    ...
  })
  .catch(cancel => {
    ...
  })
```

## 原理解析之extend和$mount  
这两个都是`vue`提供的`api`,当遇到嵌套组件时，首先将子组件转为组件形式的`VNode`时,
会将引入的组件对象使用`extend`转为子组件的构造函数，作为`vnode`的一个属性`Ctor`,
然后将`vnode`转为真实的Dom时实例化这个构造函数。最后实例化后手动调用`$mount`进行
挂载，将真实DOM插入父节点完成渲染.    
> 所以这个弹框组件可以这样实现，我们自己对组件对象使用`extend`转为构造函数，然后
手动调用`$mount`转为真实`DOM`，由我们来指定一个父节点让它插入到指定的位置。

### extend
> 接收的是一个组件对象，再执行`extend`时将继承基类构造器上的一些属性，原型方法，静态方法等。
最后返回`Sub`这么一个构造好的子组件构造函数。拥有和`vue`基类一样的能力，并在实例化时会执行
继承来的`_init`方法完成子组件的初始化。   

```javascript
Vue.extend = function (extendOptions = {}) {
  const Super = this // vue基类构造函数
  const name = extendOptions.name || Super.options.name

  const Sub = function (options) { //定义构造函数
    this._init(options) // _init继承而来
  }

  Sub.prototype = Object.create(Super.prototype) // 继承基类Vue初始化定义的原型方法
  Sub.prototype.constructor = Sub // 构造函数指向子类
  Sub.options = mergeOptions( // 子类合并options
    Super.options, // components, directives, filters, _base
    extendOptions // 传入的组件对象
  )
  Sub['super'] = Super // Vue基类

  // 将基类的静态方法赋值给子类
  Sub.extend = Super.extend
  Sub.mixin = Super.mixin
  Sub.use = Super.use

  ASSET_TYPES.forEach(function (type) { // ['component', 'directive', 'filter']
    Sub[type] = Super[type]
  })

  if (name) {// 让组件可以递归调用自己，所以一定要定义name属性
    Sub.options.components[name] = Sub // 将子类挂载到自己的components属性
  }
  Sub.superOptions = Super.options
  Sub.extendOptions = extendOptions
  return Sub // 返回子组件的构造函数
}
```

### 实例化Sub 
> 执行`_init`组件初始化的一系列操作，初始化事件、生命周期、状态等。将`data`
`props`内定义的变量挂载到当前`this`实例下，最后返回一个实例化后的对象。

```javascript
Vue.prototype._init = function(options) {  // 初始化
  ...
  initLifecycle(vm)
  initEvents(vm)
  initRender(vm)
  callHook(vm, 'beforeCreate')
  initInjections(vm)
  initState(vm)
  initProvide(vm)
  callHook(vm, 'created')  // 初始化阶段完成
  ...
  
  if (vm.$options.el) {  // 开始挂载阶段
    vm.$mount(vm.$options.el)  // 执行挂载
  }
}
```

### $mount

> 在得到初始化后的对象后，开始组件的挂载。首先将当前`render`函数转为`vnode`,然后将`vnode`
转为真实`dom`插入到页面完成渲染。再实现挂载之后，会在当前组件实例`this`下载`$el`属性，他就是
完成挂载后对应的真实`dom`, 我们就需要使用到这个属性。  

### 实现一个简单的命令式`confirm`组件
1. 组件实现(仅js逻辑部分，完整代码之后展示)

```javascript
export default {
  data () {
    return {
      showFlag: false,
      title: 'hello',
      confirmBtnText: '确定',
      cancelBtnText: '取消'
    }
  },
  methods: {
    show (cb) {
      this.showFlag = true
      typeof cb === 'function' && cb.call(this, this)
      // 返回Promise，使命令函数异步调用
      return new Promise((resolve, reject) => {
        this.reject = reject // 给取消按钮使用
        this.resolve = resolve // 给确定按钮使用
      })
    },
    hide () {
      this.showFlag = false
      document.body.removeChild(this.$el) // 结束移除DOM
      this.$destroy() // 销毁组件
    },
    cancel () {
      this.reject(false) // 抛布尔值true或false，表示点击确定按钮还是取消按钮
      this.hide()
    },
    confirm () {
      this.resolve(true)
      this.hide()
    }
  }
}
```

2. 转换调用方式
```javascript
import Vue from 'vue'
import Confirm from './confirm.vue'

let newInstance
// 创建构造函数
const ConfirmInstance = Vue.extend(Confirm)

// 执行方法后完成挂载
const initInstance = () => {
  newInstance = new ConfirmInstance() // 实例化
  document.body.appendChild(newInstance.$mount().$el)
  // 实例化后手动挂载，得到$el真实dom, 将其添加到body最后
}

// 导出一个方法，接收配置参数
export default options => {
  if (!newInstance) {
    initInstance() // 挂载
  }
  Object.assign(newInstance, options)
  // 实例化后newInstance就是一个对象了，所有data内的数据会挂载到this下，传入一个对象与之合并
  return newInstance.show(vm => { // 显示弹框
    newInstance = null // 将实例对象清空
  }).then(confirm => {
    return Promise.resolve(confirm) // 确认的回调
  }).catch(cancel => {
    return Promise.reject(cancel) // 取消的回调
  })
}

```

首先使用extend将组件对象转换为组件构造函数，执行initInstance方法后就会将真实Dom挂载到body的最后。为什么之前不使用props而是用的data，因为它们初始化后都会挂载到this下，不过data代码量少。导出一个方法给到外部使用，接受配置参数，调用后返回一个Promise对象。

3. 挂载到全局

```javascript
// main.js
import Confirm from './components/confirm'  
Vue.prototype.$confirm = Confirm
```

4. 使用  
```javascript
export default {
  methods: {
    showSubmit() {
      this.$Confirm({
        title: 'vue大法好!'
      }).then(confirm => {
        console.log(confirm)  
      }).catch(cancel => {
        console.log(cancel)
      })
    }
  }
}
```

