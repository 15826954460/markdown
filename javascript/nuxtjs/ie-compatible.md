##### nuxt.js 开发兼容ie浏览器的es6 转码
1. 安装
```
npm install babel-polyfill -s
```
2. 引入 polyfill
```js
// plugins/ployfill.js
import 'babel-polyfill';
```
3. 配置config.js
```js
// config.js
plugins: [
  { src: '@/plugins/polyfill' }
]
```
4. 跟目录下创建 .babelrc 
```js
// .babelrc文件
{
  "presets": [
    "es2015","stage-0","stage-3"
  ]
}
```