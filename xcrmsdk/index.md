# xcrmsdk的使用  

## 1. 获取列表数据  

```javascript
import { invokeHiddenApi } from "vue-xcrmsdk";
export default {
  data() {
    return {
      list: [],
      viewType: 1,
      filters: {} // 高级筛选
    }
  },
  methods: {
    getListData(page) {
       let params = {
        viewType: this.viewType,
        filterValue: JSON.stringify(this.filters),
        pageIndex: page.pageIndex,
        pageSize: page.pageSize
      };
      // invokeHiddenApi('实体名称', '实体名称/对应方法', )
      return invokeHiddenApi("new_task","Task/GetTaskList", params).then(taskList => {
        return taskList;
      });
    }
  }
}
```

## 2. 获取详情数据  
```javascript
import { invokeHiddenApi } from "vue-xcrmsdk";
export default {
  data() {
    return {
      list: []
    }
  },
  methods: {
    async getDetailData(page) {
      try {
        let params = {
          leadId: this.leadId
        };
        const res = await invokeHiddenApi("new_task","Task/GetTaskReadonlyInfo", params);
      } catch(err) {
        console.log(err);
      }
    }
  }
}
```
