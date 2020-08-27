# vue项目遇到的相关问题

## 1. 数据变化了，页面没有刷新

原因：   
1. 该数据对象未在data中初始化

示例：
```html
<template>
  <div class="">
    <h3>测试</h3>
    <p>{{form.name}}</p>
  </div>
</template>

<script>
export default {
  data () {
    return {
      form: {

      }
    }
  },
  mounted () {
    this.loadData()
  },
  methods: {
    loadData () {
      setTimeout(() => {
        this.form.name = '张三'
      }, 1500)
    },
    changeName () {
      // this.form.name = '李四'
      // Object.assign更新数据
      // this.form = Object.assign(this.form, { name: '历史' })
      // 手动更新数据，可触发vue的渲染机制
      this.$set(this.form, 'name', '例外')

      // 数组形式的数据更新
      // this.$set(this.list, 0, this.form)
      // 手动触发渲染
      // this.$forceUpdate()
    }
  }
}
</script>
```