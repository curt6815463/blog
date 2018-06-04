---
title: Wooden Puzzle Block 遊戲實作
date: 2018-06-05 01:37:56
tags:
---
# Wooden Puzzle Block 遊戲實作

## 遊戲介紹

[Youtube](https://www.youtube.com/watch?v=bpSTRpy9qYo&t=649s)

## 開發過程

### 1. 畫出遊戲的版面

```htmlmixed=
<div class="frame">
    <div class="row">
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
      <div class="col"></div>
    </div>
    ....總共有10個row 100個col
</div>
```

套上CSS

```css=
.frame{
  height: 500px;
  width: 500px;
  margin: 2em auto;
}
.row{
  height: 50px;
  display: flex;
}
.col{
  width: 50px;
  outline: 1px solid red;
}

```

畫面效果
![](https://i.imgur.com/8qItSTs.png)

### 2. 創建table

雖然畫出畫面，不過到時候我們會需要有個二維陣列，用來記錄每個格子的狀態，像是：狀態0畫出白色，狀態1畫出藍色

```javascript=
let table = []
for(let y = 0 ; y < 10 ; y++){
  table[y] = []
  for(let x = 0 ; x < 10 ; x++){
    table[y][x] = 0
  }
}
```

我們來 `console.table(table)` 看看結果
![](https://i.imgur.com/WMJcPk8.png)

### 3. 宣告各種方塊的資料，並且畫在畫面上

```javascript=
let shape = [
    [
      [1,0,0,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ],
    [
      [0,0,1,0,0],
      [0,0,1,0,0],
      [1,1,1,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ],
    [
      [0,0,1,0,0],
      [0,0,1,0,0],
      [0,0,1,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ],
    [
      [0,0,0,0,0],
      [0,0,0,0,0],
      [1,1,1,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ],
    [
      [1,1,1,0,0],
      [1,1,1,0,0],
      [1,1,1,0,0],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ],
    [
      [0,0,1,0,0],
      [0,0,1,0,0],
      [0,0,1,0,0],
      [0,0,1,0,0],
      [0,0,1,0,0]
    ],
    [
      [0,0,0,0,0],
      [0,0,0,0,0],
      [1,1,1,1,1],
      [0,0,0,0,0],
      [0,0,0,0,0]
    ]
]
```

做好資料後我們就可以畫在瀏覽器上，首先在html加上
```htmlmixed=
<div class="pendingBox">
    <div class="pendingOne">

    </div>
    <div class="pendingTwo">

    </div>
    <div class="pendingThree">

    </div>
</div>
```
CSS 加上
```css=
.pendingBox{
  position: relative;
}
.pendingOne{
  position: absolute;
  left:0px;
}
.pendingTwo{
  position: absolute;
  left: 300px;
}
.pendingThree{
  position: absolute;
  left: 600px;
}
/* 填滿的style */
.blue{
  background-color: blue;
}
```
pendingBox是個容器負責放那三個準備被使用者拖動的方塊
pedningOne~Three負責當作方塊節點，讓我們將資料配合DOM製作上去
創一個負責繪製Box的function
```javascript=
//三個方塊的對照表，我們要知道 one two three分別產生了哪種 shpae
var pendingBoxComparisonTable = {}
function createPendingBox(pendingBox) {
  //隨機產生 shape 的編號
  let randNum = Math.floor(Math.random() * (shape.length-1 - 0 + 1)) + 0;
  // 放.row
  let nodeY
  // 放.row 底下的.col
  let nodeX
  shape[randNum].some((row, y) => {
    nodeY = document.createElement('div')
    nodeY.classList.add('row')
    row.some((value, x) => {
      nodeX = document.createElement('div')
      nodeX.classList.add('col')
      if(value){nodeX.classList.add('blue')}
      nodeY.appendChild(nodeX)

      document.querySelector(`.${pendingBox}`).appendChild(nodeY)
    })
  })
  pendingBoxComparisonTable[pendingBox] = shape[randNum]
}
createPendingBox('pendingOne')
createPendingBox('pendingTwo')
createPendingBox('pendingThree')
```
#### 畫面效果

![](https://i.imgur.com/fHFrUou.png)
下面多了三個隨機方塊

### 4. 處理拖動事件
我們要讓Box自由自在的拖動，並且畫到表格上方
#### 滑鼠按下去
三個 Box 分別監聽 `mousedown event` ，當按下去時記下他們起始位置，並且設定 `selectedBox` 讓等等的 mousemove 事件知道該移動哪個 BOX 的 `top` `left`

```javascript=
var pendingOne = document.querySelector('.pendingOne')
var pendingTwo = document.querySelector('.pendingTwo')
var pendingThree = document.querySelector('.pendingThree')
// 設定滑鼠正在點下去的狀態（還沒放開）
var isMouseDown = false
var startX = 0, startY = 0, endX = 0, endY = 0
var selectedBox = ''
pendingOne.addEventListener('mousedown', function (e) {
  isMouseDown = true
  startX = e.clientX
  startY = e.clientY
  selectedBox = 'pendingOne'
})

pendingTwo.addEventListener('mousedown', function (e) {
  isMouseDown = true
  startX = e.clientX
  startY = e.clientY
  selectedBox = 'pendingTwo'
})

pendingThree.addEventListener('mousedown', function (e) {
  isMouseDown = true
  startX = e.clientX
  startY = e.clientY
  selectedBox = 'pendingThree'
})
```

#### 滑鼠拖動

滑鼠移動時根據他`移動時的終點`，扣掉一開始按下去的距離就是我們所要設定的`left` `top`

```javascript=
document.addEventListener('mousemove', function(e) {
  if(isMouseDown){
    endX = e.clientX
    endY = e.clientY

    let offsetX = endX - startX
    let offsetY = endY - startY
    let pendingBox = document.querySelector(`.${selectedBox}`);
    switch (selectedBox) {
      case 'pendingOne':
        offsetX += 0
        break;
      case 'pendingTwo':
        offsetX += 300
        break;
      case 'pendingThree':
        offsetX += 600
        break;
      default:

    }
    pendingBox.style.left = offsetX + "px"
    pendingBox.style.top = offsetY + "px"

  }
})
```

#### 畫面效果

![](https://media.giphy.com/media/BCg5D6bDs3h48AhqaT/giphy.gif)

#### 滑鼠放下瞬間
這部分的邏輯會很多，首先我會想知道 Box 最後是在哪個座標被放下來的，並且對應到 table 在哪

下圖中有 A B 兩點，我只要 B 的 x y 扣掉 Ａ 的 x y 就可以知道方塊被拖進來會相應到什麼位置


![](https://scontent.fkhh1-2.fna.fbcdn.net/v/t1.15752-9/s2048x2048/34474996_1785874701495978_2263218512522641408_n.jpg?_nc_cat=0&oh=4df5d3c6a44b17fc4026f41c010404b7&oe=5BBEBA0C)

因為我的表格中的每一格都是50px，因此假設最後 Bx - Ax && By - Bx 算出來是 75 55 ，將他們個別除以 50 就知道他們墜落在表格中哪個部分，不過我會再加上25作為緩衝區域，我們來看下面的圖

![](https://scontent.fkhh1-2.fna.fbcdn.net/v/t1.15752-9/34529458_1785880891495359_451174563414278144_n.jpg?_nc_cat=0&oh=d2750ca88ca124f29d56ff0c631bf763&oe=5B7BA46D)

雖然他的左上角靠在表格（0,0），不過事實上他更靠近中間，應該要算在中間

```javascript=
document.addEventListener('mouseup', function (e) {
    if(isMouseDown){
        isMouseDown = false
        let pendingBox = document.querySelector(`.${selectedBox}`);
        let posX = Math.floor(((pendingBox.getBoundingClientRect().left - frameX) + 25) / 50)
        let posY = Math.floor(((pendingBox.getBoundingClientRect().top - frameY) + 25) / 50)
    }
}
```
取到該放的位置之後就可以將他畫上去了

話的時候必須注意
* 有沒有超過邊界
* 位置上是否已經有東西

最後如果話成功就回傳 success
```javascript=
let result = fillTable(pendingBoxComparisonTable[selectedBox],{x:posX,y:posY})

function fillTable(box,point) {
  let fillGridList = []
  let outOfBound = false
  let boxFillEmpty = true
  box.some((row, y) => {
    row.some((value, x) => {
      if(value === 1){
        if((y+point.y) >= 0 && (x+point.x) >= 0 &&
          (y+point.y) < 10 && ((x+point.x) < 10)){
          if(checkGridEmpty(x+point.x,y+point.y)){
            let count = (y+point.y)*10 + (x+point.x)
            grid = {
              x:x+point.x,
              y:y+point.y,
              count:count
            }
            fillGridList.push(grid)
          }
          else {
            boxFillEmpty = false
          }
        }
        else {
          outOfBound = true
        }

      }
    })
  })
  if(!outOfBound){
    if(boxFillEmpty){
      fillGridList.some((grid)=>{
        cols[grid.count].classList.add('blue')
        table[grid.y][grid.x] = 1
      })
      return 'success'
    }
  }
}
```

如果回傳success我們需要先把畫成功的dom先砍了（因為他只是畫面效果），接者檢查是否有直行或橫列填滿一條了

getFullLine() 會找到 x y 軸有哪些是滿的，結果像是這樣
```javascript=
{x:[2,5,6] y:[1,3,7]}
```
接著再根據這些index去把他們的狀態都改成0並且讓他的style取消blue
```javascript=
if(result === 'success'){
  removePendingBoxDom(selectedBox)
  // createPendingBox(selectedBox)
  clearFullLine(getFullLine())
}

function removePendingBoxDom(selectedBox) {
  let removeDom = document.querySelector(`.${selectedBox}`)
  while(removeDom.hasChildNodes()){
    removeDom.removeChild(removeDom.lastChild)
  }
}

function clearFullLine(fullLine) {
  fullLine.xFullLine.some((x)=>{
    for(let i = 0 ; i < 10 ; i++){
        table[x][i] = 0
        cols[x*10+i].classList.remove('blue')
    }
  })
  fullLine.yFullLine.some((y)=>{
    for(let i = 0 ; i < 10 ; i++){
        table[i][y] = 0
        cols[y+i*10].classList.remove('blue')
    }
  })

}
```

最後若是 pendingOne~three 都成功填滿了(代表也都被移除節點了），則要另外再加入新的三個 Box

```javascript=
if(pendingOne.childElementCount === 0 &&
       pendingTwo.childElementCount === 0 &&
       pendingThree.childElementCount === 0){
      createPendingBox('pendingOne')
      createPendingBox('pendingTwo')
      createPendingBox('pendingThree')
}
```

## 最終畫面

![](https://media.giphy.com/media/kPCaLOf9w5znh21dK2/giphy.gif)

## 實作結果
https://curt6815463.github.io/woodenPuzzleBlock/
