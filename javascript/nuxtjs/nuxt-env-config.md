##### 关于nuxt.js多环境配置

Nuxt.js默认提供了开发环境和正式环境，但是在实际使用过程中，我们用到的环境远远不止默认的这两种，为了满足测试环境部署的需求，所以需要支持额外的环境配置，下面以dev、testing、preprod、prod 四种环境为例：

```js
// package.json
"script": {
    "dev": "cross-env NODE_ENV=development nodemon server/index.js --watch server", // 开发环境保持不变
    "build:test": "cross-env NODE_ENV=testing nuxt build", // 测试环境
    "build:preprod": "cross-env NODE_ENV=preproduction nuxt build", // 与生产环境
    "build": "nuxt build", // 生产环境  "build": "cross-env NODE_ENV=production nuxt build" 亦可，这里就直接走默认的
}
/**
 * 以上命令都会都过 corss-env 来设置当前环境变量
 **/
```
<font color=red>坑：</font>
但是改环境变量无法在client页面中获取

解决：
```js
// nuxt.config.js
module.exports = {
  env: {
    NODE_ENV: process.env.NODE_ENV, // 将命令行中设置的变量保存到env对象上
  }
}
```

到此为止，你就可以在页面的任何地方需要根据环境来处理的业务逻辑获取当前环境变量了  通过  process.env.NODE_ENV 获取变量

