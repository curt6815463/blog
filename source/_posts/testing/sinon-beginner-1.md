---
title: Sinon.js 測試套件與測試替身（上）
date: 2018-05-22 20:43:44
tags: testing
---
# Sinon.js 測試套件與測試替身（上）

## 前言
Sinon.js 是一套提供給JavaScipt使用的Testing Plugins，如果有先前有看過JavaScript 中的測試工具怎麼分類？文章，那你會大概瞭解測試套件在測試中扮演什麼角色

## Test Doubles
首先必須先知道 − Test Double (測試替身），它可以在測試中代替那些會造成我們不便的 funciton
>Test Double類似於電影中一些危險動作，導演們會請專業替身代替影星們去執行這些事情。

### Test Doubles 目的

#### 註

* SUT = System under test 你要測的東西
* DOC = Depended On Component SUT 所依賴的東西(function or package)


1. 讓我可以只測SUT而不必去管DOC狀況

例如：前端接完 API 後的畫面邏輯已經寫好了並想要測試它，可是瑞凡你後端的 API 還沒開好，這時 ajax 發出的 request 就可以使用 Test Doubles 跳過去

2. 模擬狀況

可以控制流程的走向，像Call 登入 API 的時候，讓他走到Error Case

3. 加速開發，不用等其他人



### Test Doubles種類

1. Spy
2. Stub
3. Mock
4. Fake
5. Dummy

Sinon.js提供 Spy, Stub, Mock, Fake四種實作方式，因此Dummy不另外介紹

### Spy

spy是間諜的意思，如字面上它可以監測某個function，並取得她的資訊，像是：function被call了幾次、被call的時候帶了哪些參數、回傳值是什麼……等

使用 sinon 的 anonymous function 示範
```JavaScript=
  let spy = sinon.spy()
  spy('a','b')
  console.log(spy.firstCall.args) //['a', 'b']
```
第四行看到使用`spy.firstCall.args`可取得function被呼叫時所帶入的參數

接著換作一個已存在的函式 isExpire 作為範例

註：isExpire用來判斷傳入的參數是否過期
```JavaScript=
  var product = {
      isExpire:function(expireDate){
          let now = (new Date()).getTime()
          let expire = Date.parse(expireDate)
          return (now > expire) ? true : false
      }
  }

  let spy = sinon.spy(product,'isExpire')
  product.isExpire('1995/2/3')
  console.log(spy.calledOnce) //結果為1
```

11行指定要 spy product 上的 isExpire function，在確認其呼叫次數為1
