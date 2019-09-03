#### 关于在nuxt中使用vue filter 过滤器

首先回顾一下vue项目中顾虑器的使用，两种使用方法

1. 全局过滤器
```js
// vue-filter.js
import Vue from 'vue';
// 这里以千分符格式化的过滤器为🌰， 金额做千位符格式化并保留两位小数  如果是数字就直接做千位符格式化，不保留两位小数
let toThousands = (num, type) => {
  let result = ''
  if (num) {
    if (type === 'num') {
      let numStr = num.toString()
      while (numStr.length > 3) {
        result = ',' + numStr.slice(-3) + result
        numStr = numStr.slice(0, numStr.length - 3)
      }
      result = numStr + result
    } else {
      num = Number(num.toString() || 0).toFixed(2)
      let numAry = num.toString().split('.')
      let preNum = numAry[0]
      let lastNum = numAry[1]
      while (preNum.length > 3) {
        result = ',' + preNum.slice(-3) + result
        preNum = preNum.slice(0, preNum.length - 3)
      }
      if (preNum) {
        result = preNum + result
      }
      result = result + '.' + lastNum
    }
  } else {
    result = '0.00'
  }
  return result
}

Vue.filter('formatToThree', (value, type) => {
  return toThousands(value, type)
})
// 最长12个字符
Vue.filter('minStr12', value => {
  if (!value || value === '' || value.length <= 12) return value
  if (value.length > 10) return value.substr(0, 10) + '...'
})

// 千位符格式化
Vue.filter('toThousands', (value) => {
  return toThousands(value)
})
```

2. 组件级别的过滤器
```js
export default { 
  filters: {
    capitalize: function (value) {
      // todo what you want to do 
    }
  }
}

```
##### <font color="#dd0000">注意，当全局过滤器和局部过滤器重名时会使用局部过滤器</font>
实际开发中全局过滤器足够搞定所有的相关过滤器的需求


#### nuxt.js 中过滤器的使用
- 局部的使用为例,简单易懂
```js
// 以格式话文件大小为🌰，filter-file-size.js
export default {
  filters: {
    filterFileSize(val) {  // 格式化文件大小显示: 12345 -> 12.05KB
      let unit = '';
      if (typeof val !== 'number' || val <= 0) {
        return '';
      } else if (val < 1024) {
        unit = 'B';
      } else if (val < 1048576) { // 1024*1204
        unit = 'KB';
        val = val / 1024;
      } else if (val < 1073741824) { // 1024 * 1024 * 1024
        unit = 'MB';
        val = val / 1048576;
      } else {
        unit = 'GB';
        val = val / 1073741824;
      }
      val += '';
      const ind = val.indexOf('.');
      if (ind > -1) {
        val = val.slice(0, ind + 3);  // 保留两位小数
        val = parseFloat(val) + '';
      }
      return val + unit;
    },
  },
};
// 在使用改顾虑器的页面中通过局部混入的方式进行引入
import filterFileSize from '**/filter-file-size.js';
export default {
  mixins: [filterFileSize],
}
```
- 你也可以通过写全局的方法函数来处理
```js
// utils/filter.js
const funa(val) {
  return // 经过处理后的val 
}
export { funa }
// 页面使用
<template>
   <div> {{ funa(someVal) }} </div>
</template>
import {} from "";
export default {
  data() {
    return {
      funa
    }
  }
}
```


##### main.js 中引入
```js
import "**/vue-filter.js";
```

##### 页面使用
```
<div>{{ var | toThousands }}</div>
```