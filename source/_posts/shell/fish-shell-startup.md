---
title: Fish Shell 起手式
date: 2018-06-30 01:36:50
tags: Shell
---
# Fish Shell 起手式

## 查看shell

先使用指令看自己用哪個 `shell`
```shell=
echo $SHELL
```
![](https://i.imgur.com/vau74Hz.png)

可以看到目前我使用 zsh
那麽就來安裝 fish 吧！！

## Install

```shell=
brew install fish
```

灌好之後就可以在 terminal 執行 fish 將 `zsh shell` 轉成 `fish shell`

```shell=
fish
```
![](https://i.imgur.com/9YQP9xZ.png)

可以看到成功的轉成 fish

## Set Default

我不想要每次打開都執行一次 `fish` 所以我們來將他設定成 default

使用 `chsh -s <shell 路徑>`

在這之前我們先看看 `fish` 的路徑在哪裡

```shell=
which fish
```

![](https://i.imgur.com/lJMvNUE.png)

知道之後就來設定了

```shell=
chsh -s /usr/local/bin/fish
```

其實可以有更方便方法

```shell=
chsh -s `which fish`
```

有可能會發生
### `non-standard shell`

![](https://i.imgur.com/djUctIo.png)

這時候你要去 `/etc` 底下的 shells 設定（記得使用sudo，避免 shells 檔沒有 write 權限)

```shell=
cd /etc
sudo vim shells
```

![](https://i.imgur.com/Zp2KOxn.png)

加上 `/usr/local/bin/fish` 這行之後重開 `terminal`

### `no changes made`

可參考這篇 [change default shell](https://apple.stackexchange.com/questions/88278/change-default-shell-from-bash-to-zsh)

### 當上述兩個問題解決後在使用

```shell=
chsh -s `which fish`
```

之後打開 `terminal` 預設就會使用 `fish shell` 了



## fisherman

>The fish-shell plugin manager

fisherman 是 fish-shell plugin的管理者

你可能發現我的 fish-shell 也有著漂亮的箭頭，是因為使用 fisherman 來設定了 agnoster 主題 (原本的 zsh 也有這個主題設定)

### 安裝 fisherman

```shell=
curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs https://git.io/fisher
```

### 安裝主題

```shell=
fisher install hauleth/agnoster
```

```shell=
agnoster powerline
```



## 參考資源

[login shell](https://apple.stackexchange.com/questions/88278/change-default-shell-from-bash-to-zsh)

[fish vim mode](https://stackoverflow.com/questions/28444740/how-to-use-vi-mode-in-fish-shell)

[set default shell](https://askubuntu.com/questions/26439/how-do-i-set-fish-as-the-default-shell)

[fisherman](https://github.com/fisherman/fisherman)

[fisherman agnoster](https://github.com/hauleth/agnoster)
