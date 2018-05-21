---
title: Docker－Image篇
date: 2018-05-21 15:00:18
tags: docker
---
# Docker－Image篇

## image（映像檔）

docker 的唯獨模板，例如：一個映像檔可以包涵完整的 ubuntu系統，就叫他 ubuntu映像檔，當然這個映像檔也可以裝上 apache，以及其他東西，就被稱作ＸＸＸ映像檔。

## 取得映像檔
可使用 docker pull 指令下載某個倉庫的映像檔，若沒有指定tag，則會預設使用 latest標籤（最新）

```shell=
// latest
docker pull ubuntu
// 14 version
docker pull ubuntu:14.04
```

事實上都是從預設的註冊伺服器下載（docker hub），相當於

```shell=
docker pull registry.hub.docker.com/ubuntu:XXX
```

當然也可以從其他register抓取資源，像是docker pool。

```shell=
docker pull dl.dockerpool.com:5000/ubuntu
```

下載好後即可利用這個image 建出container(實例），並且執行bash

```shell=
docker run -t -i ubuntu /bin/bash
```
## 查看映像檔

使用 `docker images` 查看本機上已存在的映像檔

```shell=
docker images
```

![](https://i.imgur.com/usGN02N.png)


TAG 資訊用於標記來自同一個倉庫（ubuntu) 不同的映像檔， docker tag 則可為本機上映像檔添增新標籤 (repository欄位名稱），這兩者不一樣。

docker tag 產生的新映像檔實際上指向了同一個映像檔，所以他們 IMAGE ID 相同

```shell=
docker tag ubuntu:latest xxxx
docker tag <ID> xxxx
```

![](https://i.imgur.com/LtVv2RY.png)

若要查看映像檔詳細資訊可使用

```shell=
docker inspect <ID>
//可使用-f查詢特定參數
docker inspect -f {{".Architecture"}} <ID>
```

## 搜尋映像檔
使用 docker search <key term> 搜尋遠端倉庫中的映像檔，預設搜尋 Docker Hub

```shell=
docker search mysql
```

## 刪除映像檔
使用 docker rmi <ID or tag> 刪除映像檔

使用 tag 刪除: 假設有多個 Tag 指向同個 ID，則刪除某個 image（使用 docker images 看到的 image) 時不會真的刪掉真實的 image，只是刪除他的標籤。如果最後只剩一個，則真實 image就會被刪除。

使用 ID 刪除:如果有多個 tag 指到同個 ID (使用 docker tag)，則無法刪除必須使用 tag，它不會讓你直接刪除真實 image，除非只剩一個 tag 指到 image ID

## 建立映像檔

* 基於現有映像檔的容器建立

啟動一個本來就有的映像檔，然後在上面進行修改並新增一個檔後退出

```shell=
docker run -ti ubuntu /bin/bash
docker commit -m "xxxxx" -a "xxxx" <container ID> <tag Name>
ex:
docker commit -m "added a new file" -a "docker newbee" a925cb40b3fo test
```

此時使用docker images就會發現有新的image

* 基於Linux 容器（LXC）範例匯入

有空補上

* Dockerfile

有空補上

## 儲存和載入映像檔

如果要將image存成壓縮檔可使用docker save

```shell=
docker save -o ubuntu_latest.tar ubuntu
//這比較常見
docker save ubuntu > xxx.tar
```

反之則可使用docker load把壓縮檔載入本機映像檔

```shell=
docker load < xxx.tar
```

## 上傳映像檔

首先，你必須先去 Docker Hub 辦帳號，並且創一個 repo 取名為 uploadtest (隨意)，接著可以使用 docker push 上傳(根本就 github)。

以下需注意，假設 tag 為 ubuntu，則需要先用 docker tag 將其新增一個 tag，名稱必須是 Docker Hub ID/Docker hub上 repo
name，在使用push就可直接推到遠端。記得要先用docker login登入，不然就耍智障了


```shell=
1. docker login
//curt6815463是我docker hub上ID, uploadtest是我在上面創的repo名字
2. docker tag ubuntu curt6815463/uploadtest
3. docker push curt6815463/uploadtest
```
