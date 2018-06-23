---
title: Mobile 端與 Window 端開發的差別
date: 2018-06-07 23:40:33
tags: [HTML,JS]
---
# Mobile 端與 Window 端開發的差別

## 1. Web In App

* Window 端：需考慮不同瀏覽器可能會有不同的使用效果，因為個別使用了不同的 Rendering Engine
* Mobile 端：除了不同瀏覽器之外，要注意網站開啟的方式是不是使用 WebView (Web In App)，WebView 根據 Rendering Engine 來打造的一個模擬瀏覽器環境，因此不能保證所有功能都有實作上去，最常見的就是從 Line 開網頁，網頁就壞了

## 2. 事件處理

* window 端：因為是滑鼠操作關係所以會用 hover 來控制事件發生後的反應
* Mobile 端：沒有滑鼠則是使用手指觸碰，因此需考慮 touch event

## 3. 內容組織

我們都知道手機與桌機一次能呈現的畫面大小不同，因此在設計內容及動線時，需考慮到客戶的使用情況，而非將頁面都塞得滿滿。

通常桌機會以水平排列為主，手機端則會是垂直排列


## 4. 效能網路速度考量

手機端效能及網路速度差異較大，在設計mobile端時，考量到使用者等待資源載入及畫面反應時間，需做些效能優化或是移除非必要但負載較大的功能。