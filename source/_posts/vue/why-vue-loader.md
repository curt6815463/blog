---
title: 為什麼vue可以寫在.vue檔裡面？
date: 2018-05-19 17:37:01
tags: [Vue]
---
# 為什麼vue可以寫在.vue檔裡面？

## 前言

想要寫一個vue應用你可以引入他的CDN，接著new出一個vue instance並開始寫裡面的邏輯，但除此之外Vue提供了別的方式來撰寫，那就是`vue-loader`

### 沒有使用vue-loader範例
```html=
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>

<body>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>

  <div id="counter">
    <vue-button>
    </vue-button>
  </div>

  <script>
    Vue.component('vue-button', {
      template: `
        <div>
          {{count}}
          <button v-on:click='increment'>click me</button>
        </div>
      `,
      data: function () {
        return {
          count: 0
        }
      },
      methods: {
        increment: function () {
          this.count += 1
        }
      },
    })
    new Vue({
    el: '#counter'
   })
 </script>
</body>
</html>

```
### 畫面效果
![](https://i.imgur.com/tyjfMlA.png)

### 問題點

當今天畫面邏輯變大時候，肯定要拆分不同的檔案不可能全部都寫在`html`裡面
而在html裡面我們也只能透過script tag引入不同js file來拆分檔案
如果有個方式來幫我們處理`分檔問題`是再好不過


## 來使用vue-loader吧！！

### 使用概念

將`Vue`程式直接寫在副檔名為.vue的file裡面，可是這些file始終要放到瀏覽器執行，因此需要有人幫他轉譯成.js檔案，那就是使用Webpack配上vue-loader

Webpack是一個前端打包工具，可以將各種資源通過一些規則（loader)，將其都打包成.js(或是.css .jpg......)，像是：
* sass-loader把.scss編譯成css
* json-loader把 .json編譯成js (Webpack 二版後可以直接使用import引入json file)
* vue-loader把.vue編譯成js

題外話：webpack本身會解析ES6 module，將import關鍵字包裝成立即執行函數，裡面就放著要import的file程式碼

事實上這些設定是比較困難也複雜的，最快的方法可以透過`vue-cli`來產生vue project的模板，裡面已經幫我們做好vue-loader的配置

### vue-cli指令

```shell=
// 安裝全域vue-cli
$ npm install -g vue-cli

// 初始化vue project
// 格式：vue init <template-name> <project-name>
$ vue init webpack my-project

```
進入my-project資料夾內的build/webpack.base.conf.js可以看到vue-loader的設定
![](https://i.imgur.com/b8FfO9L.png)

### vue-loader file說明

根據vue-loader官方文件說明，一個.vue檔基本上由`template`,`script`,`style`三個部份組成

1. **template**: 撰寫html以及渲染的邏輯指令，ex:v-show,v-model
2. **css**: 可直接寫入html的css樣式(若再tag中加入scoped，則代表只有當前的.vue檔會生效)
3. **script**：使用exprot default輸出這個component，意思就是說一個.vue檔可把他當作一個component

### Example
```html=
//這是test.vue檔
<template>
    <div class="example">{{ msg }}</div>
</template>

<script>
export default {
    data () {
        return {
            msg: 'Hello world!'
        }
    }
}
</script>

<style>
.example {
    color: red;
}
</style>
```
### 不同Vue file間引入
```html=
<template>
    <div class="example">{{ msg }}
        <xxx>
        </xxx>
    </div>
</template>

<script>
import xxx from xxx.vue
export default {
    components:{
        xxx
    },
    data () {
        return {
            msg: 'Hello world!'
        }
    }
}
</script>

<style>
.example {
    color: red;
}
</style>
```
* 使用import引入xxx.vue file（9行
* 並且在template中使用xxx組件（3 4行
* 注意！！記得要在components中掛上xxx（11行
