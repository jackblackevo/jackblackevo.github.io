---
layout: post
title: 管理 macOS 中的 JDK 版本
---

在 macOS 中，可以安裝並同時存在多個版本的 Java Development Kit（JDK）。我們可以使用 [jEnv](http://www.jenv.be/) 來管理不同版本的 JDK 開發環境。

## 安裝 jEnv

使用 [Homebrew](http://brew.sh/index_zh-tw.html) 安裝：

```bash
$ brew install jenv
```

## 設定 jEnv

首先執行：

```bash
$ jenv init
```

會根據執行的 shell 環境來顯示說明，例如在 zsh 下提示訊息為：

```
# Load jenv automatically by adding
# the following to ~/.zshrc:

eval "$(jenv init -)"

```

依此說明，至 .zshrc 設定檔中加入：

```
eval "$(jenv init -)"
```

再重載入 .zshrc 設定檔：

```bash
$ source ~/.zshrc
```

## 管理 JDK 版本

查看目前系統中所有已安裝的版本：

```bash
$ jenv versions
```

待手動安裝完指定版本後，再設定要使用的 JDK 版本：

```bash
$ jenv global 1.8
```

*（jEnv 沒有安裝、移除 JDK 的功能，要自行安裝所需版本的 JDK）*

查看目前所使用的版本：

```bash
$ jenv version
```