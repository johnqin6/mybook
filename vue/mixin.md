# mixin 混入
混入(mixin)提供了一种非常灵活的方式，来分发vue组件中可复用的功能.一个混入对象可以包含
任意组件选项。当组件使用混入对象时，所有混入对象的选项将被‘混合’进入该组件本身的选项。

## 使用示例
```javascript
// mixin.js
const myMixin = {
  data () {
    return {
      name: 'mixin'
    }
  }
  create() {
    this.hello()
  },
  methods: {
    hello() {
      console.log(`hello ${name}`)
    }
  }
}
export default myMixin
```
```html
<!-- 组件 -->
<script>
import myMixin from '@/mixins/myMixin'
export default {
  // 注入mixin
  mixins: [myMixin]
}
</script>
```

### 示例解析
- 数据对象可混入，混入的数据对象会和组件的数据对象合并, 同名的被组件数据覆盖
- 钩子函数混入，同名钩子函数将合并为一个数组，因此都可被调用, 此外混入对象的
钩子函数将在组件自身钩子之前调用
- 值为对象的选项，例如`methods`,`components`和`diretives`, 将被合并为同一个
对象，两个对象键名冲突时(例如方法名重复)，取组件对象的键值对
- `Vue.extend()` 也使用同样的策略进行合并

## 全局混入
混入也可以进行全局注册。使用时格外小心！一旦使用全局混入，它将影响每一个之后创建的 Vue 实例。使用恰当时，这可以用来为自定义选项注入处理逻辑。
```javascript
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```
> 请谨慎使用全局混入，因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)。大多数情况下，
> 只应当应用于自定 义选项，就像上面示例一样。推荐将其作为插件发布，以避免重复应用混入。

