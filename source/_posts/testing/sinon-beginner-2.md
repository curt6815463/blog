---
title: Sinon.js 測試套件與測試替身（中）
date: 2018-05-24 20:48:38
tags: testing
---
# 接續Sinon.js 測試套件與測試替身（中）
此篇 `接續Sinon.js 測試套件與測試替身（上）`
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
