---
title: 關於瀏覽器的字型
date: 2018-05-30 11:21:45
tags: [HTML,CSS]
---

# 關於瀏覽器的字型

## 在網頁中的應該使用奇數還是偶數的字體？為什麽呢？

通常在網頁上看到的字體大小都是偶數，像是12px 14px 16px，為什麼大家不喜歡用奇數的大小呢？

## 原因

* 字體渲染的時候可能會不對稱，像是`中`這個字，中間一條線畫下來平分兩邊，勢必有一邊會大點或小點
* 在過去window內建的宋體，一開始也只提供12px、14px、16px......等字型
* 通常設計師設計圖樣時會使用偶數字，所以到前端也是自然用偶數切版
* 偶數可以方便用相對的方式來改變字型大小，例如預設字體通常會用 16px，因為 16 可以除很多次 2 後還是整數

## chrome 瀏覽器限制

在 chrome 瀏覽器中， font-size 最小限制在12px，如果設定12以下都會被自動轉成12px，如果今天有這種需求話該如何解決？

### 1. -webkit-text-size-adjust: none
可以在 CSS 中設定
```css=
-webkit-text-size-adjust: none
```
不過這個屬性在 27.0 版之前才會 work
### 2. transform:scale(0.7)
使用CSS3的屬性 transform:scale()
```css=
transform:scale(0.7);
```
不過他會以`區塊`為單位放大縮小，因此切版上需做些改變

### 冷知識
chrome的字體確實最小只能調到12px，不過嚴格說起來應該是：`最小只能呈現出畫面 100% 時 12px 的像素大小`

圖中是畫面縮放100%時，文章列表字體為12px
![](https://i.imgur.com/KRMPrWC.png)
如果使用`cmd +`將畫面放大到200%，把字體調成6px是會更小的！！！
![](https://i.imgur.com/Zqi6y2p.png)





## 瀏覽器字體單位

瀏覽器字體的單位除了 px 還有 pt、em、rem，那麼 em 與 rem 差別是什麼呢？

### em

em 是依照父層的字體大小為基準，做等比例放大，例如

```htmlmixed=
<div class='father'>
    父
    <div class='children'>
    子
    </div>
</div>
```

```css=
.father{
    font-size: 14px;
}
.children{
    font-size: 1em;
}
```
這時子層的實際大小就是 14px

### rem

rem 則是不管在哪一層都會依照 `html tag` 為基準(應該說 root 層）做等比例放大，例如：

```htmlmixed=
<html>
    <head>
    </head>
    <body>
        <div class='father'>
         父
            <div class='children'>
             子
            </div>
        </div>
    </body>
</html>
```

```css=
html{
font-size: 16px;
}
.father{
    font-size: 14px;
}
.children{
    font-size: 1em;
}
```

子層會以root為基準，所以實際字型大小會是 16px




## 參考資料

[六角](http://www.hexschool.com/2016/01/02/2016-08-08-em-vs-rem/)

[jmin_coming](https://blog.csdn.net/jian_xi/article/details/79346477)
