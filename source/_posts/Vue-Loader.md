---
title: Vue-Loader
date: 2018-05-19 17:37:01
tags:
---
# Vue-Loader

## 前言

除了引入CDN(或安裝Vue Package)一般的方式寫vue之外，Vue還提供了一個叫作Vue-Loader的方式來寫，他讓我們可以有更好的邏輯架構去寫專案

### 沒有使用Vue-Loader範例
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
如果有人可以幫我們處理架構問題是再好不過


## 來使用Vue-loader吧！！

### 使用情況

可將`Vue`程式碼寫在副檔名為.vue的file裡面，讓我們更方便的拆分檔案邏輯，但是需要有人幫他翻成JS(.vue file瀏覽器不知道是三小啊！！)，這時候就要請到webpack打包它

基本上可透過vue-cli產生一個專案，裡面webpack已經將Vue-loader設定寫好可以直接使用它






<!-- <style> -->
<!-- .highlight .line .regexp{
  color:#4d4d4c;
} -->
<!-- .post-content p img{

} -->
<!-- </style> -->
