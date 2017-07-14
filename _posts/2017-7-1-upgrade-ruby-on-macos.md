---
layout: post
title: 更新 macOS 中的 Ruby
---

macOS 系統內建的 Ruby 版本較為老舊，我們可以使用 [rbenv](https://github.com/rbenv/rbenv) + [ruby-build](https://github.com/rbenv/ruby-build) 來管理、安裝不同版本。

## 安裝 rbenv + ruby-build

使用 [Homebrew](http://brew.sh/index_zh-tw.html) 安裝：

```bash
$ brew install rbenv
$ brew install ruby-build
```

## 設定 rbenv

首先執行：

```bash
$ rbenv init
```

rbenv 會根據執行的 shell 環境來顯示說明，例如在 bash 下提示訊息為：

```
# Load rbenv automatically by appending
# the following to ~/.bash_profile:

eval "$(rbenv init -)"

```

依此說明，至 .bash_profile 設定檔中加入：

```
eval "$(rbenv init -)"
```

再重載入 .bash_profile 設定檔：

```bash
$ source ~/.bash_profile
```

## 安裝指定版本 Ruby

查看目前系統中所有己安裝的版本：

```bash
$ rbenv versions
```

查看目前可安裝的版本：

```bash
$ rbenv install --list
```

以安裝 Ruby 2.4.1 版為例：

```bash
$ rbenv install 2.4.1
```

待由 ruby-build 編譯、安裝完指定版本後，再設定要使用的 Ruby 版本：

```bash
$ rbenv global 2.4.1
```

查看目前所使用的版本：

```bash
$ rbenv version
```