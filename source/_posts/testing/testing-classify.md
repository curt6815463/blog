---
title: JavaScript中的測試工具怎麼分類？
date: 2018-05-20 21:29:57
tags: testing
---
# 測試的工具怎麼分類？

## 前言
開始練習寫測試的時候，再學會語法之前常常就被一堆的工具搞混，像是Jest、Mocha、Karma......等，這篇文章來簡單聊聊這些工具的功用及分類

## 隨意說說Unit Test
>單元測試專注在單一區塊程式碼，可能是function、Object、Module，在JS中主要以function為主，透過傳入的參數或設定外部變數來確認這個function的回傳為預期的結果

舉個例子：假如今天在電商網站中，實作一個確認商品沒有過期的Function

```JavaScript=
function isExpire(expireDate){
  let now = (new Date()).getTime()
  let expire = Date.parse(expireDate)
  return (now > expire) ? true : false
}
```

當傳入的expireDate參數比今天日期小時，代表商品已經過期必須要回傳false，因此測試可能寫成這樣

```JavaScript=
if (isExpire('3000/4/4')){
  console.log('OK pass')
}
else {
  throw 'expire function error'
}
```
但是這樣寫不但需要自己管理測試程式，呈現的結果也很糟糕，這時候就可以使用一些測試工具摟

<!-- 補充：除了Unit Test，還有End-To-Ent Testing呢！！ -->

## 工具分類

測試工具通常可以分為四類，這不是絕對！不過可以思考這樣分類的意義

1. Test Runner
2. Testing Framework
3. Assertion Libraries
4. Testing Plugins

### Test Runner
提供環境給測試工具在其中運行，並且將處理結果輸出到Log、File、Console……等

Karma是個標準的Test Runner，他會產生一個網頁伺服器，針對不同瀏覽器跑你寫的Unit Test，並將結果輸出到console，告訴你哪些測試是pass哪些是fail

當你要測的程式碼`本身需要跑在瀏覽器時`，像是操作網頁的DOM或是測試一些前端framework的畫面邏輯，你就可以使用`Karma`，因為通常Unit Test會寫在.js檔並用Node.js去跑，而Node.js只是一個跑在伺服器端環境的JavaScript，所以想要在伺服器端運行瀏覽器端的程式碼就得靠它了

註：這邊指的伺服器端只是代表`他沒有在browser端`，並不是一定要開個Http Server才能寫Node.js

### Testing Framework

提供一個寫測試的規則，讓你照著那個準則就可以很方便的撰寫及管理測試程式碼（根本只是在講framwork是什麼東西吧），那我們來舉個實例

如果使用了mocha剛剛的範例就會如下

```JavaScript=
describe('isExpier', () => {
  it('check the date whether is expier', () => {
    if (isExpier("2000/04/04")) {
      throw new Error('the date should be expire')
    }
  })
})
```

可以看到多了describe及it，這就是mocha提供的規則，讓我們能比較方便對Unit Test 進行分類，對於Test Runner執行後的結果也能整理成漂亮的報表來呈現

但是依舊要寫If-Else好煩啊！！還要寫那麼長，這時候Assertion Libraries（斷言庫）就可以派上用場拉！

### Assertion Libraries

斷言可以替Unit Test省下冗長的If-Else，除此之外還可以增加程式碼的可讀性

我們使用Chai再將剛剛的例子修改一下

```JavaScript=
describe('isExpire', () => {
  it('check the date whether is expier', () => {
    expect(isExpire('2000/04/04').to.equal(false))
  })
})
```

是不是就簡單多了？

註：斷言寫法有分為TDD style, BDD style，上面範例為BDD style，這又可追溯到開發原理TDD與BDD，不過本篇暫時不討論

### Testing Plugins

基本上使用Test Runner、Test Framework、Assertion Libaraies就可以寫好測試了，不過試想有時為了測試某個功能，而特地開了API、Server、Database……等，是不是太麻煩了？或是為了測試異步的function，而使得測試過程為了等待回應而降低了測試的效益，像是Ajax Call，這時就可以使用一些Plugins，來幫助我們解決類似問題。

## 結論

* Karma是Test Runner
* Mocha是Testing Framework
* Chai是Assertion Libaraies
* Sinon主要是Testing Plugins，並自帶Aserrtion Libaraies
* Jest主要是Testing Framework，並且有Assertion Libaraies + Testing Plugins
>Jest好棒棒

## 資料來源
http://amzotti.github.io/testing/2015/03/16/what-is-the-difference-between-a-test-runner-testing-framework-assertion-library-and-a-testing-plugin/

本篇文章的工具四種分類為參考amzotti想法，並加上自己的觀點

<!-- 首先必須先知道−Test Double(測試替身），它可以在測試中代替那些會造成我們不便的funciton
>Test Double類似於電影中一些危險動作，導演們會請專業替身代替影星們去執行這些事情。

主要有五種

1. Spy
2. Stub
3. Mock
4. Fake
5. Dummy

不過為我會以Sinon.js（JavaScript的Testing Plugins套件)的實作角度去解釋功用

#### Spy

spy是間諜的意思，如字面上它可以監測某個function，並取得她的資訊，像是：function被call了幾次、被call的時候帶了哪些參數、回傳值是什麼……等

```JavaScript=
it('show firstCall args',function () {
    let spy = sinon.spy()
    spy('a','b')
    console.log(spy.firstCall.args) //['a', 'b']
})
```
第四行看到使用`spy.firstCall.args`可取得function被呼叫時所帶入的參數
換作剛剛isExpire的例子：

```JavaScript=
var product = {
    isExpire:function(expireDate){
        let now = (new Date()).getTime()
        let expire = Date.parse(expireDate)
        return (now > expire) ? true : false
    }
}

describe('Spy Example', function (){
    it('isExpire should call once',function () {
        let spy = sinon.spy(product,'isExpire')
        product.isExpire('1995/2/3')
        console.log(spy.calledOnce) //結果為1
        sinon.assert.calledOnce(spy)
    })
})
```

11行指定監測product上的isExpire function，再使用sionn自帶的Assertion Libraries：`calledOnec`，確認它只被呼叫一次 。

註：當然你也可以使用Chai的Assertion Libraries。

#### Stub -->
