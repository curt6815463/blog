---
title: Sinon.js 測試套件與測試替身
date: 2018-05-22 20:43:44
tags: testing
---
# Sinon.js 測試套件與測試替身

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


#### Stub

可以避免某些功能被呼叫，像是 ajax call，並且能控制 function 走向特定流程（EX：拋出錯誤）


#### sinon文件上的 anonymous function 示範

**withArgs**

設定stub要帶入的參數，搭配其後續動作
```JavaScript=
  var callback = sinon.stub();
  callback.withArgs(42).returns(1);
  callback.withArgs(1).throws("name");

  callback(); // No return value, no exception
  callback(42); // Returns 1
  callback(1); // Throws Error("name")
```
透過 `withArgs` 設定傳入的參數，並且決定他要傳出的結果為 `1` 或是 `error case`

 **onCall**
```JavaScript=
  var callback = sinon.stub();
   callback.onCall(0).returns(1);
   callback.onCall(1).returns(2);
   callback.returns(3);

   callback(); // Returns 1
   callback(); // Returns 2
   callback(); // All following calls return 3
```
透過 `onCall` 設定 function 被 call 幾次後會有什麼回傳，像是第一次 call 回傳 1，第二次為2

#### 已存在函示示範

**yields** `stub.yields([arg1, arg2, ...])`

如果stub有收到callback參數，用yields會直接call第一個callback函式

```JavaScipt=
  var myfun = {
    fun1: function () {
    }
  }
  function cb(data) {
    console.log(data);
  }
  sinon.stub(myfun, "fun1").yields('callback arg1')
  myfun.fun1(cb) //印出'callback arg1'
```

可以發現即使myfun裡的fun1沒有任何參數，還是可以通過，代表stub不會真的去call那個function

**yieldTo** `stub.yieldsTo(property, [arg1, arg2, ...])`

如果呼叫funtion時傳入一個object，可透過 `yeildsTo` 指定invoke某個function
```JavaScript=
  sinon.stub(myfun, "fun1").yieldsTo('shit','shit arg')
  myfun.fun1({shit:function (data) { //印出shit arg
    console.log(data);
  }})   
```
到這邊都只是在瞭解語法，那我們來阻止 ajax call 真正的發生吧！！
**來stub jQuery 的 ajax call**

```JavaScript=
sinon.stub(jQuery, "ajax").yieldsTo("success", "ajax arg");

    jQuery.ajax({
        success: function (data) {
            console.log(data)
        }
    })
    //最後印出ajax arg
}
```
但是我幾乎都用promise方式來接成功或失敗呀！那就使用usingPromise

**usingPromise** `stub.usingPromise(promiseLibrary)``

```JavaScript=
  sinon.stub($,'ajax').usingPromise($.Deferred()).resolves("baz")
  $.ajax('123')
    .done(function (data) {
      console.log(data); //baz
  })
```

如果測試要接上promise，首先我們必須知道它所使用的 Promise Library，查了一下 jQuery 才知道說原來他使用 defeered (使用.done連接）

只要使用jQuery.Deferred() 可以取得deferred object，所以就將它丟進usingPromise funtion 當參數

假如今天是使用預設的Promise，也就是使用 `.then` 串接，那`promiseLibrary`則要傳入 `Promise`

**來把 .done 改成 .then**

```JavaScript=
sinon.stub($,'ajax').usingPromise(Promise).resolves("87")
$.ajax('123') //印出87
  .then(function (data) {
    console.log(data);
  })
```      
