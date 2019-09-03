#### 关于picture标签使用的坑

[mdn中关于picture标签的介绍](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/picture)

HTML <picture> 元素通过包含零或多个 <source> 元素和一个 <img> 元素来为不同的显示/设备场景提供图像版本。浏览器会选择最匹配的子 <source> 元素，如果没有匹配的，就选择 <img> 元素的 src 属性中的URL。

正常使用
```html
<!-- type 属性允许你为 <source> 元素的 srcset 属性指向的资源指定一个 MIME 类型。如果用户代理不支持指定的类型，那么这个 <source> 元素会被跳过。 -->
<picture>
  <source srcset="https://s3.us-east-2.amazonaws.com/test-docer-file/photo/20190328/17bf10bca5f94f7c9666f168f136991f.webp" type="image/webp">
  <img src="mdn-logo-narrow.png" alt="MDN" @error="imageError"> 
</picture>

<script>
    // onerror函数  
    imageError(event) {
      event.target.src = 'www.baidu.com';
    },
</script>
```

<font color=red  face="黑体">坑:</font> 
在一切正常的情况下，上述的使用完全没有问题，浏览器会自行根据source 标签进行匹配最佳元素，但是，如果远程资源 srcset 资源不存在的时，srcset会不断的去获取资源，这样带来的问题就是，一旦我们的资源不存在的时候，整个页面就会在 srcset 不断的获取资源中导致崩溃

<font color=green>解决办法：</font>
1、直接避免使用这些标签, 简单粗暴
2、通过变量来控制是否渲染改标签,使用img标签来代替
   eg:
```html
<template>
  <img v-if="isWebP" :img="https://s3.us-east-2.amazonaws.com/test-docer-file/photo/20190328/17bf10bca5f94f7c9666f168f136991f.webp" @error="imageError">
  <img v-else :src="https://s3.us-east-2.amazonaws.com/test-docer-file/photo/20190328/17bf10bca5f94f7c9666f168f136991f.jpg" @error="imageError"> 
</template>
```
```js
export default {
  data() {
    return {}
  },
  created() {
    // 通过一个小小的base64的引用来判断是否支持webp
    checkWebpStatus({ commit }) {
      let isWebP = false;
      const img = new Image();
      img.onload = function() {
        isWebP = !!(img.height > 0 && img.width > 0);
        commit('setWebpStatus', isWebP);
      };
      img.onerror = function() {
        isWebP = false;
        commit('setWebpStatus', isWebP);
      };
      img.src = 'data:image/webp;base64,UklGRiQAAABXRUJQVlA4IBgAAAAwAQCdASoBAAEAAgA0JaQAA3AA/vuUAAA=';
    },
  },
  methods: {
  }
}
```

第二种支持了webp图片格式，以及远端资源不存在的兼容，做了错误处理