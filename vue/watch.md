# vue中watch的用法
在vue项目开发中，我们经常会使用到监听器watch,来监听数据的变化并进行数据变化后的一系列操作。

## watch 的基本用法
`watch`在项目中使用的情况还是比较多的，例如列表通过`watch`监听搜索框搜索关键字的变化，实现自动搜索功能的实现等等。  
```html
<template>
  <!--此处示例使用了element-ui-->
  <div>
    <div>
      <span>搜索</span>
      <input v-model="searchValue" />
    </div>
    <!--列表，代码省略-->
  </div>
</template>
<script>
export default {
  data() {
      return {
        searchValue: ''
      }
  },
  watch: {
    // 当searchValue变化时自动调用搜索方法
    searchValue(newVal, oldVal) {
        // 判断值是否变化
        if (newVal !== oldVal) {
            this.loadData();
        }
    }  
  },
  methods: {
     // 搜索方法
      loadData() {
          
      }
  }
}
</script>
```  
## watch的深入学习使用

### immediate（立即执行）
通过上边的基本用法的代码，我们已经可以实现在值变化时进行相关操作了。不过如果我们有个新需求，在页面加载时就调用加载数据的方法，除了在使用生命周期函数(created, mounted)外，也可以给`watch`添加一个新的属性`immediate`,它可以立即触发监听器的执行，实现我们的新需求.   

```javascript
// 在基础用法上改造一下
export default {
  //...
  watch: {
    searchValue: {
      // 通过handler来监听属性变化, 初次调用 newValue为""空字符串， oldValue为 undefined
      handler(newVal, oldVal) {
        // 判断值是否变化
        if (newVal !== oldVal) {
            this.loadData();
        }
      },
      // 配置属性立即执行
      immediate: true
    }
  },
  //...
}
```   

### deep(深度监听)   
当我们在实现一个表单页面时，如果我们有个需求：在用户修改任意一项时，就更改表单状态，此时如果我们按照前边的方法一个一个的监听每一项就比较繁琐了，此时我们就可以使用`watch`的`deep`属性来深度监听整个对象，只要对象中某一项改变时就会触发监听器。  

```javascript
export default {
  data() {
    return {
      form: {
        name: '',
        sex: '',
        age: 0
      }
    }
  },
  watch: {
    form: {
      // 需要注意，因为对象引用的原因， newValue和oldValue的值一直相等
      handler(newVal, oldVal) {
        // 这里标记页面编辑状态
      },
      // 配置深度监听，此时会监听form对象的每一个属性
      deep: true
    }
  },
  //...
}
```  

如果客户忽然要求不需要根据页面每个属性的变化来更改页面状态，只需要在年龄(age)变化时改变页面状态，此时使用深度监听就不合适了。这是我们可以使用对象打点属性(obj.attr)的方式监听对象的属性。    
```javascript
export default {
  data() {
    return {
      form: {
        name: '',
        sex: '',
        age: 0
      }
    }
  },
  watch: {
    'form.age': { // 此时只会给form的age添加监听器
      handler(newVal, oldVal) {
        // 这里标记页面编辑状态
      },
      immediate: true
    }
  },
  //...
}
```  

### 使用`$watch`,随时监听，随时取消  

在上边我们实现的页面编辑时改变页面状态的功能，还存在一个问题，就是只能监听新增页面的表单状态，当在编辑页面时，会回填异步请求的数据，此时就会立即触发监听器`watch`, 导致页面状态被改变。这种情况下我们就可以使用$satch来实现了。   

```javascript
export default {
  data() {
    return {
      form: {
        name: '',
        sex: '',
        age: 0
      }
    }
  },
  created() {
    this.loadData();
  },
  methods: {
    // 模拟异步请求数据
    loadData() {
      setTimout(() => {
        // 先赋值
        this.form = {
          name: '姓名',
          age: 10
        }
        // 当表单数据回填后，再监听表单数据是否发生变化, unwatch是this.$watch的返回值，可以使用`unwatch()`取消监听
        const unwatch = this.$watch(
          'form',
          () => {
            console.log('数据变化了')
          },
          {
            deep: true
          }
        )
        // 模拟数据发生变化即客户修改了表单
        setTimeout(() => {
          this.form.name = '张三'
        }, 1000);
      }, 1000);
    }
  }
}
```  
