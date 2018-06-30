---
title: Fish Shell 製作 terminal custom greeting
date: 2018-06-30 12:30:33
tags: Shell
---
# Fish Shell 製作 terminal custom greeting

## User Story

每次打開 terminal 都可以看到隨機的勵志語錄在上方

![](https://i.imgur.com/lnI3G9O.png)


## 清除預設 greeting

預設打開 fish shell 會看到

> Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish

如果要更換文字的話可以到 `~/.config/fish/config.fish` 檔案內加上
```shell=
set fish_greeting "Welcome"
```

這時候再重新打開 fish shell 會看到 `Welcome` 字眼而不再是
```
Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
```
![](https://i.imgur.com/K0lvX8Q.png)


註：如果沒有 config.fish 就自己手動創建一份


## Shell Script


### echo

如果在 `set fish_greeting "Welcome"` 下面加上

```scala=
echo 'Hello World'
```

重開終端機就會看到

![](https://i.imgur.com/otFF7Bj.png)

### set

set 可以設定變數為某個值，並在 `echo` 時候加上 `$` 取出來

```shell=
set a 'this is a'
echo $a

//result
//this is a
```

因此可以開始設定語錄變數

```shell=
set 0 擁有夢想只是一種智力，實現夢想才是一種能力。
set 1 人生只有走出來的美麗，沒有等出來的輝煌。
set 2 人生就像騎腳踏車，想保持平衡就得往前走。

echo $0
```

但我希望每次可以隨機挑遠一筆，就可使用 random 配上陣列

### Random choice And Array

先設定 quotes 變數為一組陣列
```shell=
set quotes 'a' 'b' 'c'
```
並使用 random choice 在裡面隨機取一筆資料出來

```shell=
set quote (random choice $quotes)

//result
//a or b or c
```

再換回我們原本的勵志語錄就會是這樣

```shell=
set 0 擁有夢想只是一種智力，實現夢想才是一種能力。
set 1 人生只有走出來的美麗，沒有等出來的輝煌。
set 2 人生就像騎腳踏車，想保持平衡就得往前走。

set quotes $0 $1 $2
set quote (random choice $quotes)
echo $quote
```

## 網路上爬語錄

如果想抓網路上的語錄，但是很多筆該怎麼辦？

像是這個網站：http://mingyanjiaju.org/juzi/jingdianduanju/2014/1011/847.html

可以觀察 fish shell 的程式碼都有固定的 pattern，因此我使用 Node.js 來將這些複製下來的語錄轉成 fish shell 程式碼樣子

### 複製文章內容並貼到 data.txt 檔

![](https://i.imgur.com/Bci2bgi.png)

### Node.js 讀取檔案並格式化樣式

創立 index.js 並加入以下內容
```javascript=
var fs = require('fs');

fs.readFile('data.txt', function (err, data) {
    if (err) throw err;

    var array = data.toString().split("\n");
    let str = 'set xxx'
    for(i in array) {
        if(array[i]){
          if(i < 18){
            console.log(`set ${i} ${array[i].slice(2)}`);
          }else if(i < 198){
            console.log(`set ${i} ${array[i].slice(3)}`);
          }else {
            console.log(`set ${i} ${array[i].slice(4)}`);
          }
          str = `${str} $${i}`
        }
    }
    console.log(str);
});
```

在 terminal 執行 `node index.js` 可看到
![](https://i.imgur.com/AQ4xPMJ.png)

我們就可以直接將它貼到 `~/.config/fish/config.fish` 裡面

## 結果

![](https://media.giphy.com/media/BMPTpk1o0e5b0wPph0/giphy.gif)
