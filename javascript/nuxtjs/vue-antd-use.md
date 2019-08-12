#### antd在nuxt.js中的使用
1. 添加antd
```js
// plugin/antd.js (按需引用)
import Vue from 'vue';
import {
  Row, Col, Dropdown, Icon, Menu, Button, Form, Input, Select, Upload, Table, Tag, Modal
} from 'ant-design-vue';
Vue.use(Row); Vue.use(......)
```
2. 配置文件中添加插件
```js
// config.js
plugins: [
  { src: '~/plugins/antd.js', mode: 'client' },
],
// 配置按需引用的规则
build: {
  babel: {
    plugin: [
      [
          'import',
          {
            libraryName: 'ant-design-vue',
            libraryDirectory: 'es',
            style: 'css'
          }
        ]
    ]
  }
}
```

以上已就可以正常使用 ant-design-vue, 友情提示,  如果不是纯粹的类似于后台管理系统的项目还是不要引用为好,antd的管局样式会覆盖一些样式,导致页面出现一些问题