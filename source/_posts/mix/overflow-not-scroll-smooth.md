---
title: 設定overflow後scroll時，不能平滑滾動的問題怎麽處理？
date: 2018-05-20 14:54:54
tags: [JS,CSS]
---
# overflow: scroll 時不能平滑滾動的問題怎麽處理？

## 情境
> 在div設定 overflow:scroll後，從IOS手機打開瀏覽器會發現，滾動畫面的時候很卡，手一離開畫面就立即停止繼續滑動


## 解法
使用
```css=
 -webkit-overflow-scrolling: touch;
```
這行會讓IOS啟動硬件加速，因此可解決卡頓的問題
不過這也是WebKit 108400版本左右才支持
因此iOS Safari需要5.0以上，Android4.0以上才有效
## 探討前綴字元

#### 首先要知道不同的瀏覽器，會使用不同的引擎去實作

* OperaGoogle、Safari：Webkit
* Firefox：Gecko

註：
Opera以前是Presto後來改為Webkit
Firefox在mobile中也有部分使用Webkit實作

#### 因此在某些情況，瀏覽器會選擇使用前綴字眼表示某些CSS

#### EX:
1. 試驗一些還未成為標準的的CSS屬性——也許永遠不會成為標準
2. 對新出現的標準的CSS3屬性特徵做實驗性的實現
3. 對CSS3中一些新屬性做等效語義的個性實現

> 主要就是讓瀏覽器來測試或試驗新的CSS標準

#### 前綴字眼

1. -ms-：Trident（IE）
2. -moz-：Gecko(Firefox)
3. -o-：Presto(Opera)
4. -webkit-：Webkit(Chrome、Safari)


## 談談Web open in mobile app

> 常常開發的網頁在Line或Messenger，甚至是其他應用程式中打開居然就壞了！！明明自己寫跟測試的時候都正常啊？

首先我們要理解，在手機應用程式內開啟的瀏覽器，與我們一般打開的browser有所出入，事實上俗稱**Webview**（以Chrome、Safari為主）

### Webview (In App Web)
根據Webkit引擎實作的手機內核瀏覽器，只是為了簡單的展示網頁，因此有些功能很可能沒有實作上去，像是Webview所打開的Safari就沒有cookie功能



## 回到源頭：萬一這些In app web沒有實作這些CSS？

這時或許只好從JS的角度來解決問題

以這次的範例來思考，是不是可以再滾動的末尾在偷偷給加上一些數值？讓他假裝滑順（先不考慮它的加速度狀況）

首先我會直覺想到Dom的**scroll event**、**wheel event**
註：scroll偵測scrollbar的變動，wheel偵測滑鼠滾輪滾動

```htmlmixed=
<div id="wrap">
    <ul>
        <li>...</li>
        ....無限多
    </ul>
</div>
```

```javascript=
let el = document.querySelector('#wrap')
el.addEventListener('scroll',(event)=>{
    console.log('正在scroll');  
})
```

可以偵測到每次地滾動，不過我要的應該是最後一個scroll的事件而不是每一個都給它加上數值，可以使用**setTimeout**

```javascript=
let el = document.querySelector('#wrap')
let timer = null
el.addEventListener('scroll',(event) => {
    if(timer !== null) {
      clearTimeout(timer);
    }
    timer = setTimeout(function() {
      console.log('這次scroll最後一個event');
    }, 100);
})
```

但是做到這開始思考，大部分容易出這個狀況的應該是在mobile，那麼滑螢幕滾動時有按下去的瞬間以及釋放的瞬間，是不是使用**touch event**更加合適？

### touch event
共有四組
* touchstart
* touchend
* touchcancel
* touchmove

目前狀況看起來可使用touchend事件，來讓滑動的最後加上一些數值

```javascript=
let el = document.querySelector('#wrap')
el.addEventListener('touchend',(event)=>{
    setTimeout(()=>{
        el.scrollTop = el.scrollTop + 20
    },1000)
  })
```

當手離開時（touchend），把scrollTop偷偷加20模擬smooth的感覺
不過這樣會頓一下，因為是直接加上20所以會直接跳到那個位置
如果要有點smooth的feel我們可以用setInterval

```javascript=
let el = document.querySelector('#wrap')
el.addEventListener('touchend',(event)=>{
    setTimeout(()=>{
      var final = el.scrollTop + 20
      scrollInterval = setInterval(function(){
      if (el.scrollTop < final) {
        el.scrollTop = el.scrollTop + 5
      }
      else clearInterval(scrollInterval);
      },15);
    },100)
  })
```
當手離開時，每0.015秒scrollTop加上5直到加滿20
這樣下來就稍微有模擬smooth的樣子
當然了，如果要讓它非常的滑順，則需要有良好計算及演算


PS:若想要使用手機debug，除了Chrome Dev Tool之外可使用
https://console.re/
