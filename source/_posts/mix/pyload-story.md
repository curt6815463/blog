---
title: Payload 的故事
date: 2018-06-23 17:27:15
tags: trivia
---
# Payload 的故事

## Prerequisite

通常在一段訊息中區分為：

1. 使用者感興趣的部分
2. 輔助支持的部分

## origin

Payload 一詞來自交通運輸行業，假如一台油罐車可以運二十桶的油(使用者感興趣的部分)，但運輸途中駕駛、車子本身耗油、油罐、綁繩......等等都是成本(輔助支持的部分)，但是通常客戶只關心那二十桶油並且只想付那二十桶錢

payload 後來就泛指使用者感興趣部分

## Web Service 例子

從一個 Web Server Call Api 可能會拿到以下的訊息
```javascript=
{
    "status":"OK",//(輔助支持的部分)
    "data":
        {
            "message":"Hello, world!" //(使用者感興趣的部分)
        }
}

```
我們真正“有興趣的部分”是：`message:"Hello, world!"`

## 變數命名

因此當發送 POST PUT ...... 等 API 時，常會有人將要帶入 `body` 的資料變數命名為 `payload`

打開 Browser dev Tool 的 Network 查看，也會發現瀏覽器也將他們稱作 Payload

![](https://i.imgur.com/xUmq4Am.png)


## 資料來源

https://softwareengineering.stackexchange.com/questions/158603/what-does-the-term-payload-mean-in-programming
