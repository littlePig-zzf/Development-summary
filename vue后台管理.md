### 权限控制：v-has指令

```vue
// 权限检验方法
auth = {post,/pv/card: true,
post,/pv/card/pageSearch: true,
delete,/pv/order/*: true,
post,/pv/order: true,
post,/pv/activateCode/pageSearch: true, …}

Vue.prototype.$_has = (auths) => 
    Array.isArray(auths) ? auths.every(item => auth[item]) : auth[auths];

// 权限指令
Vue.directive('has', {
  bind(el, binding) {
    if (!Vue.prototype.$_has(binding.value)) {
      el.parentNode.removeChild(el);
    }
  },
});

 <el-button v-has="$root.auth.distributor.update"
   type="primary"
   @click="showUserDialog(scope.row)">编辑</el-button>

//permission/index.js

export default {
  distributor: {
    add: 'post,/pv/distributor',
    update: 'put,/pv/distributor',
    deleteItem: 'delete,/pv/distributor/*',
  },
  card: {
    add: 'post,/pv/card',
    update: 'delete,/pv/card',
    deleteItem: 'delete,/pv/card/*',
  },
  order: {
    recording: {
      add: 'post,/pv/order',
      deleteItem: 'delete,/pv/order/*',
    },
    detail: {
      exportData: 'post,/pv/order/detail/excelExport',
    },
    statistics: {
      exportData: 'post,/pv/order/statistics/excelExport',
    },
  },
  code: {
    exportData: 'post,/pv/activateCode/excelExport',
  },
  system: {
    account: {
      add: 'post,/pv/admin',
      update: 'put,/pv/admin',
      disable: 'patch,/pv/admin',
      deleteItem: 'delete,/pv/admin/*',
    },
    role: {
      add: 'post,/pv/role',
      update: 'put,/pv/role',
      disable: 'patch,/pv/role',
      deleteItem: 'delete,/pv/role/*',
    },
  },
};

// main.js
import permission from '@/permission/index';

new Vue({
  data() {
    return {
      auth: permission,
    };
  },
  el: '#app',
  router,
  store,
  template: '<App/>',
  components: { App },
});
```

