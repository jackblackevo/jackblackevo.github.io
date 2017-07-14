---
layout: post
title: 管理 macOS 中的 Python 版本
---

目前 macOS 系統內建的 Python 版本為 2.x，我們可以使用 [pyenv](https://github.com/pyenv/pyenv) 來管理、安裝不同版本。

## 安裝 pyenv

使用 [Homebrew](http://brew.sh/index_zh-tw.html) 安裝：

```bash
$ brew install pyenv
```

## 設定 pyenv

首先執行：

```bash
$ pyenv init
```

pyenv 會根據執行的 shell 環境來顯示說明，例如在 fish 下提示訊息為：

```
# Load pyenv automatically by appending
# the following to ~/.config/fish/config.fish:

status --is-interactive; and source (pyenv init -|psub)

```

依此說明，至 config.fish 設定檔中加入：

```
status --is-interactive; and source (pyenv init -|psub)
```

再重載入 config.fish 設定檔：

```bash
$ source ~/.config/fish/config.fish
```

## 安裝指定版本 Python

查看目前系統中所有已安裝的版本：

```bash
$ pyenv versions
```

查看目前可安裝的版本：

```bash
$ pyenv install --list
```

以安裝 Python 3.6.1 版為例：

```bash
$ pyenv install 3.6.1
```

待編譯、安裝完指定版本後，再設定要使用的 Python 版本：

```bash
$ pyenv global 3.6.1
```

查看目前所使用的版本：

```bash
$ pyenv version
```