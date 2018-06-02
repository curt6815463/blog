---
title: 前端路由
date: 2018-06-01 16:32:35
tags: JS
---
# 前端路由

## 路由

> 在網頁中，路由代表了網址( url )與頁面呈現之間的關係。

例如：在網址列輸入
* https://curt6815463.github.io/
* https://curt6815463.github.io/tags/

會看到不同的畫面，因此可以說

* 路由為`/`時，顯示文章列表畫面
* 路由為`/tags`時，顯示有tag分類的文章列表

## 前端後端？

在講前端路由之前，瑞凡你知道`前後端`是什麼東西嗎？

在 `ajax` 發明之前，事實上網頁沒有前後端之分，當使用者要瀏覽網頁時，都是從後端吐一份被整理好的檔案( html )，這裡所謂整理好意思是：我想要呈現在瀏覽器畫面的資料，已經在後端直接跟資料庫溝通，並且渲染上去

圖解來說大概是這樣：
![](https://i.imgur.com/IMxhNhG.png)

### 問題點
* 使用者每次只要想換頁面，就必須跟後端再發一次請求，然後頁面就會整個重新整理並且吃到新的HTML/CSS，
* 後端要負責 1.跟資料庫溝通 2.處理商業邏輯 3.畫面渲染邏輯

### AJAX

AJAX是JavaScript的網頁非同步請求技術，簡單來說我發出去的請求，只跟後端拿必要的資料，並且也不會讓網頁重新整理

### 分離吧！！

在AJAX出來之後，就有人開始思考把處理資料的部分切一邊，處理畫面的切一邊，也就是現在所謂前端，那麼需要串接畫面邏輯的資料就靠AJAX去達成，後端負責出資料就好

### 前後端的架構好處

* 以前的後端就不用處理太多東西
* 前端使用AJAX可以讓畫面不必重整，增加使用者體驗
* 每次只取需要的資料，可減少 server 的 loading

圖畫起來會像是：

![](https://i.imgur.com/tmCoFMi.png)


## 回到前端路由

由上敘述可以得知，如果今天使用後端路由，每當我更換 route 那勢必整個頁面都會重新載入，那麼當在前端使用路由技術配上ajax，就可製造出：`沒有整理頁面但是頁面資料卻有變換的效果`，對使用者體驗來說是大大加分

## 如何實作前端路由？

要在前端更換網址卻又不啟動重新整理的方式有兩個

### 1. 使用`hash #`

可在url加上符號`#`，像是在 `https://curt6815463.github.io/` 加上 `#123` 變成
`https://curt6815463.github.io/#123` 並不會讓頁面重新整理，而我們可以使用 `hashchange` 去監聽事件，並在裡面做出畫面改變

```javascript=
window.addEventListener("hashchange", () => {
    改變畫面
})
```

可使用兩種方式控制url

1. 使用 HTML tag `<a>`
```css=
<a href="#123">to 123</a>
```

2. 使用location.hash
```javascript=
location.hash="#123"
```

### 2. 使用`history.pushState` `history.replaceState`

HTML5在 `history` 中新增了兩個功能 `history.pushState` `history.replaceState`，它可以讓我們加入或修改歷史，但不會真的發出資源的請求

他們同樣都帶入三個參數（state object, title, URL)，不過今天我們只要專注在第三個參數`URL`，另外兩個細節有興趣可參考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/History/pushState)

```javascript=
history.pushState(null,null,"a.html")

history.replaceState(null,null,"b.html")

```

所以當你改變畫面呈現方式時，就可以 call `pushState` or `replaceState`，造成使用者覺得好像有切換頁面的效果，不過他們兩個差別是什麼？

實測結果如下

![](https://media.giphy.com/media/3Z1ppTua0q9wVIbCd8/giphy.gif)

可以發現點了四次 pushState 需要按四次上一頁才能會到 localhost:3333，但是如果 replaceState 點四次按一次上一頁就回到最原本頁面

replaceState 是修改目前的歷史紀錄，並不會像pushState創造一個新的





## 參考資料

* [MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/History_API)
* [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/History/pushState)
* [淺談前端路由](https://github.com/kaola-fed/blog/issues/137)
