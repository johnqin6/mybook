# vue 动画 过渡

vue 在插入，更新或移除DOM时，提供多种不同方式的应用过渡效果.   
在页面实现过渡效果的最简单方法是通过VUE的`<transition>`组件，该组件是vue全局组件，可直接使用。  
vue过渡的工作原理是插入，更新或者移除DOM时添加和删除元素上的类。   
处理过渡时有六种不同的类前缀：
- -enter: 定义进入过渡的开始状态
- -leave: 定义离开过渡的开始状态
- -enter-active: 定义进入过渡生效时的状态
- -leavar-active: 定义离开过渡生效时的状态
- -enter-to: 定义进入过渡生效时的状态
- -leave-to：定义离开过渡生效时的状态

## 过渡
使用示例：
```html
<div id="example-1">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
```
```css
/* 可以设置不同的进入和离开动画 */
/* 设置持续时间和动画函数 */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active for below version 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
```

## 动画
css动画用法同css过渡相同，区别是在动画中`v-enter`类名在节点插入DOM后不会立即删除，而是在
`animationend`事件触发时删除    
示例：  
```html
<div id="example-2">
  <button @click="show = !show">Toggle show</button>
  <transition name="bounce">
    <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi tristique senectus et netus.</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-2',
  data: {
    show: true
  }
})
```
```css
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```
