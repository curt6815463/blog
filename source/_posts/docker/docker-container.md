---
title: Docker － Container 篇
date: 2018-05-22 15:00:18
tags: Docker
---
# Docker － Container 篇

## container(容器）

其實就是image跑在記憶體中的樣子（實例），是一個輕量級的沙箱，彼此之間都是獨立的。

## 建立容器

使用docker create創建容器，此時的容器狀態是stopped狀態，在使用docker start 啟動它。其實可直接使用docker run

```shell=
sudo docker run -i -t ubuntu /bin/bash
//這樣會打開後直接執行 echo hello world並關掉容器
sudo docker run -i -t ubuntu /bin/echo 'Hello World'
```

## 取得映像檔
可使用 docker pull 指令下載某個倉庫的映像檔，若沒有指定tag，則會預設使用 latest標籤（最新）

```shell=
// latest
docker pull ubuntu
// 14 version
docker pull ubuntu:14.04
```
-i : 讓容器的標準保持打開
-t : 分配一個虛擬終端機綁到指定容易

基本上以後開docker就是加-t -i就對了顆顆

## 背景狀態執行

有時候只會希望docker run在背景，此時就可使用 -d

```shell=
docker run -d ubuntu /bin/sh -c "while true: do echo hello world; stop 1; done"
```

## 停止容器

如果要停止一個執行中的容器可使用docker stop

```shell=
docker stop <container ID>
```

停止後使用docker ps -a -q可看到處於停止狀態的容器，處於停止狀態的容器可使用docker start來重啟。
docker restart可以將執行中容器關閉並重啟

## 進入容器

如果使用-d讓docker在背景執行，但有時卻又需要透過指令操作可使用attach 來進入容器的command line

```shell=
docker run -idt ubuntu
docker attach <name>
```

## 刪除容器

如果要刪除不要的容器可使用docker rm

```shell=
docker rm <Id>
```

記得要先將容器給終止(docker stop)，如果要直接刪除運作中容器可加上-f

## 匯入匯出容器

可以將一個已建立的容器(不管是否運行狀態)，在本機上匯成一個檔案

```shell=
//先查看有哪些容器
docker ps -a
//匯出成10d2.tar
docker export 102d > 10d2.tar
```

![](https://i.imgur.com/gnEWnk4.png)

匯入的話則可使用docker import搭配bash 的cat + pipe（將結果丟到下一個指令），成為映像檔

```shell=
cat 10d2.tar | docker import - 10d2.tar/ubunut
```
