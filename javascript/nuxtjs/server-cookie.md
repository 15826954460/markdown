### nuxt 服务端cookie身份认证问题

##### 业务场景：公司业务需求，开发海外模板网站，针对一些功能需要用户进行登陆和认证，然而用的就是cookie，初始代码如下
```js
// nuxt.config.js
module.exports = {
  modules: [
     // Doc: https://axios.nuxtjs.org/usage
    '@nuxtjs/axios',
    '@nuxtjs/pwa',
  ],
  axios: {
    // See https://github.com/nuxt-community/axios-module#options
    credentials: true, // 通过配置该项，会在请求头中携带cookie
  }
}
```
- 看起来并没有什么问题，但是该配置获取的cookie只在 客户端请求中生效，在 [asyncData ()] 中并不会生效

一通 google 也是才不少坑尝试不少方法，终不得果  js-cookie 只能在页面为客户端渲染的时候获取cookie  cookie-universal-nuxt 在服务端进程的时候操作cookie,但是并不能满足我们携带cookie到服务端，因为都不在接口成，最后想着手动设置请求头的cookie, 代码如下

```js
// plugin/axios.js
export default function({ $axios, req, redirect }) {
  // 客户端请求
  if (process.client) {
    // do something
    $axios.defaults.headers = {
      // 针对客户端进程做一些处理
    }
  }
  if (process.server) {
    // do something
    $axios.defaults.headers = {
      // 针对服务端进程做一些处理
      cookie: req.header.cookie || ''
    }
  }
}
```

##### 参考文档
- [cookie-universal-nuxt 服务端操作cookie ](https://www.npmjs.com/package/cookie-universal-nuxt)
- [js-cookie 客户端操作 cookie](https://www.npmjs.com/package/js-cookie)