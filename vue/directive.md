# 自定义指令
- 作用：进行DOM操作
- 使用场景：对纯DOM元素进行底层操作，比如：文本框获取焦点
- 指令类型：全局指令，局部指令  

## 自定义全局指令
- 作用：定义一个指令可全局使用
- 关键code: Vue.directive()
- 建议：最好单独创建一个文件进行管理

```javascript
// 自定义指令参数介绍
// 参数1：指令名称
// 参数2：配置对象，指定指令的钩子函数
Vue.directive('directiveName', {
  // bind 中只能对元素自身进行DOM操作，而无法对父级元素操作
  // 只调用一次 指令第一次绑定到元素时调用。在这里可进行一次性的初始化设置
  bind(el, binding, vnode) {
    /**
     * 参数详解
     * - el: 指令所绑定的元素，可直接操作DOM
     * - binging: 一个对象，包含以下属性:
     *   + name: 指令名，不包含 v- 前缀
     *   + value: 指令绑定的值，等号后面的值。
     *   + oldValue: 指令绑定的前一个值，仅在update和componentUpdated钩子中可用，无论值是否改变都可用
     *   + expression: 字符串形式的指令表达式 等号后面的字符串形式
     *   + arg: 传给指令的参数，可选。例如 v-my-directive:foo 中，参数：`foo`
     *   + modifiers: 指令修饰符。例如: v-directive.foo.bar, 修饰符对象为{foo: true, bar: true}
     *   + vnode: vue编译生成的虚拟节点
     *   + oldVnode: 上一个虚拟节点，仅在update和componentUpdated钩子中可用
     */
  },

  // inserted 这个钩子函数调用的时候，当前元素已插入页面中，也就是说可以获取到父级节点了
  inserted (el, binding, vnode) {},
  // dom 重新渲染前
  update (el, binding, vnode, oldVnode) {},
  // dom 重新渲染后
  componentUpdated (el, binding, vnode, oldVnode) {},
  // 只调用一次，指令与元素解绑时调用
  unbind (el) {
    // 指令所在的元素在页面中消失，触发
  }
})

// 简写 如果你只想在 bind和 update时触发相同行为，而不关心其他的钩子
Vue.directive('color', function (el, binding) {})
// 例子：
Vue.directive('color', function (el, binding) {
  el.style.color = binging.value
})
// 使用：注意直接写会被解析成data中的数据“red” 需要字符串则嵌套引号"'red'"
<p v-color="'red'"></p>
```
1. 首先创建一个`directive.js`文件然后编写全局的自定义组件
```javascript
export default (Vue) => {
  Vue.directive('dColor', {
    inserted: function (el, binding) {
      el.style.color = binding.value
    }
  });
  Vue.directive('dFont', {
    inserted: function (el, binding) {
      el.style.fontSize = binding.value + 'px'
    }
  })
}
```
2. 在main.js文件中引入 `directive.js`文件, 并使用Vue.use(directive)注册
```javascript
import Vue from 'vue';
import App from './App.vue';
import directive from './directive';

Vue.config.productionTip = false;

Vue.use(directive); //全局使用directive文件

new Vue({
    render: h => h(App),
}).$mount('#app')
```

3. 在组件中使用
```html
<template>
    <div class="hello">
        <p v-dColor="'red'"> 我是全局定义的组件修改颜色值</p>
        <p v-dFont="'50'"> 我是全局定义的组件可以修改大小</p>
    </div>
</template>
```

## 自定义局部指令
- 作用：定义一个指令，指令局部组件使用
- 使用场景：组件中经常重用的某些操作Dom的方法,仅在在这一个组件中使用
- 关键code钩子: directives: 写一个局部指令,定义一个input自动焦点的指令

```javascript
export default {
  directives: {
    // 自定义组件的名字
    autoFocus: {
      // 钩子函数，被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于document中）
      inserted (el) {
        el.focus()
        console.log('inserted')
      },
      // 只调用一次，第一次绑定到元素时调用。在这里可进行一次性的初始化设置
      bind () {
        console.log('bind')
      },
      // 所在组件的 VNode 更新时调用，但是可能发生在其孩子的 VNode 更新之前。
      // 指令的值可能发生了改变也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 
      updata () {
        console.log('updata');
      },
      // 所在组件的 VNode 及其孩子的 VNode 全部更新时调用。
      componentUpdated () {
        console.log('componentUpdated');
      },
      // 只调用一次，指令与元素解绑时调用。
      unbind () {
        console.log('unbind');
      }
    }
  }
}
```
使用局部自定义组件
```html
<template>
  <div>
    <input type="text" placeholder="请输入" v-autoFocus/>
  </div>
</template>
```
