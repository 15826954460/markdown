#### 路由导航钩子的使用

关于路由导航钩子的使用就不过多的赘述，[参考vue官网](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)， 这里介绍一下坑

需求背景：前端项目嵌入到pc客户端使用，大约一年之后，由于业务的发展，客户端会在一个新的版本中在通过ulr添加参数的方法来做新版本的业务，但是老的版本还要做兼容，而且vue项目同样可以独立访问，假设 原始前端项目的访问路径为  vip.wps.com  由于业务发展 现如今的访问添加新的参数  vip.wps.com?supportTemplateVip=1
根据参数 support 来处理一些不同的业务

```js
// 假设路由有五十多个
export default [
  {
    path: '/',
    name: 'home',
    meta: {
      title: 'home页面',
      keepAlive: true // 指定组件的缓存性
    },
    component: () => import(/* webpackChunkName: "Home" */ '../../views/Home.vue'),
  },
  ... // 此处省略多个路由
}
```

最优解决方案肯定是在路由层面做统一处理，通过路由导航守卫

eg: 
```js
// router.js
const router = new Router({
  mode: 'history',
  base: process.env.NODE_ENV === 'production' ? '/homepage2019' : '/',
  routes: []
})
router.beforeEach((to, from, next) => {
  /**
   * supportTemplateVip = 1 表示支持付费模板 用来做新老版本兼容获取模板
   */
  if (to.query && to.query.supportTemplateVip) {
    store.commit('updateIsAllowToGetTemplatePremium', false); // 通过vuex保存状态来控制全局业务逻辑
  }
  /**
   * 针对老的版本做兼容，走以前的路由跳转逻辑
   */
  next();
})
export default router
```
以上代码看起来很完美，但是spa有一个所有开发都必须注意的点是，当用户手动刷新页面的时候，vue的生命周期会重新执行，导致所有的数据回复到初始状态

<font color=red>解决办法：</font>
```js
// router.js
const router = new Router({
  mode: 'history',
  base: process.env.NODE_ENV === 'production' ? '/homepage2019' : '/',
  routes: []
})
router.beforeEach((to, from, next) => {
  if (to.query && to.query.supportTemplateVip) {
    store.commit('updateIsAllowToGetTemplatePremium', false); // 通过vuex保存状态来控制全局业务逻辑
  }
  /**
   * 针对新的接口统一给路由添加是否支付付费模板的判断条件
   * 如果当前路由已经有改判断参数就返回
   */
  if (to.query && to.query.supportTemplateVip) {
    next();
    return; // return 为必写字端，否则会导致死循环
  }
  // 根据上一个路由将新的控制参数添加到当前目标路由
  if (from.query && from.query.supportTemplateVip) {
    let toQuery = JSON.parse(JSON.stringify(to.query));
    toQuery.supportTemplateVip = from.query.supportTemplateVip;
    next({
      path: to.path,
      query: toQuery
    })
    return;
  }
  /**
   * 针对老的版本做兼容，走以前的路由跳转逻辑
   */
  next();
})
export default router
```