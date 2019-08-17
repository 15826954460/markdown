#### 关于nuxt.js中间件的使用

实际使用场景就不做过多的介绍的，根据实际情况活学活用，这里以路由中间件的使用为🌰

1. 新建中间件
```js
// middleware/router-middleware.js
import { utils } from '~/utils/utils';
export default function ({ route, store }) {
  // 针对实际的业务进行处理，我这里过滤参数并将数据同步到vuex
  store.commit('setSource', utils.getSource(route));
};
```
2. nuxt.config.js 配置
```js
router: {
    middleware: 'router-middleware'
},
```

通过以上配置后就可以在路由层面进行一些业务相关的过滤和处理