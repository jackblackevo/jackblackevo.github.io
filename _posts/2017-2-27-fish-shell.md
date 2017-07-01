---
layout: post
title: 以 fish 做為預設的 shell
---

[fish shell](https://fishshell.com/) 是可以代替 macOS 預設 bash 的另一個 shell 選擇。fish 有語法高亮（syntax highlighting）以及指令提示等功能，對常使用 CLI 的開發者來說相當便利。

## 安裝 iTerm2
由於 macOS 預設的 Terminal 終端機過於陽春，所以我們使用 [iTerm2](https://www.iterm2.com/) 來搭配 fish shell。

使用 [Homebrew Cask](https://caskroom.github.io/) 安裝 iTerm2：

```bash
$ brew cask install iterm2
```

## 安裝 fish shell
```bash
$ brew install fish
```

## 將 fish 設為預設 shell
```bash
$ echo "/usr/local/bin/fish" | sudo tee -a /etc/shells
$ chsh -s /usr/local/bin/fish
```

### 只在 iTerm2 中使用 fish
如果只想在 iTerm2 中使用 fish，可以在：

iTerm 2 -> Preferences -> Profiles -> General

設定 Command 為 /usr/local/bin/fish

### 將預設 shell 改回 bash
若要將預設 shell 從 fish 改回 bash：

```bash
$ chsh -s /bin/bash
```

## 設定 iTerm2 顯示顏色
iTerm 2 -> Preferences -> Profiles -> Colors

選擇 Color Presets… 為 Solarized Dark

## 設定 fish shell 環境變數
由於 fish 並不相容 bash，所以環境變數的設定也不太一樣。

這裡以 [Go](https://golang.org/) 做為範例，原本在 bash 的 `GOPATH` 環境變數設定指令：

```bash
$ echo "export GOPATH=$HOME/go" >> ~/.bash_profile
$ echo "export PATH=$PATH:$GOPATH" >> ~/.bash_profile
```

在 fish 則是：

```bash
$ echo "set -g -x GOPATH $HOME/go" >> ~/.config/fish/config.fish
$ echo "set -g -x PATH $PATH $GOPATH" >> ~/.config/fish/config.fish
```

設定完畢後重新載入 fish shell 設定檔即可：

```bash
$ source ~/.config/fish/config.fish
```

## 設定 fish shell 配色及提示
fish 內建多種配色以及提示功能，若有使用 Git 還可以直接看到目前所在的分支名稱、檔案異動狀態等資訊。

開啟 fish shell 的設定頁面：

```bash
$ fish_config
```

※點選 Set 按鈕之後請等待點秒鐘，看到 Set! 才代表設定成功。完成設定後，須在 CLI 按下 Enter 結束。

## 解決 NVM 無法在 fish shell 下執行問題
同樣因為 fish 不相容 bash，也導致 NVM 中的 nvm.sh 無法正常在 fish 中執行。我們可以透過 fish 的套件管理工具—[fisherman](https://fisherman.github.io/) 來安裝 [Fish-nvm](https://github.com/fisherman/nvm) 以支援 NVM。

假設已透過 Homebrew 安裝完 NVM，接著安裝 fisherman：

```bash
$ curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs git.io/fisher
```

透過 fisherman 安裝 Fish-nvm：

```bash
$ fisher nvm
```

建立 symbolic link 指向由 Homebrew 所安裝 NVM 的 nvm.sh，讓 Fish-nvm 能夠找到：

```bash
$ ln -s (brew --prefix nvm)/nvm.sh ~/.nvm/nvm.sh
```

設定 NVM 的 `NVM_DIR` 環境變數：

```bash
$ echo "set -g -x NVM_DIR $HOME/.nvm" >> ~/.config/fish/config.fish
```

## 在 fish shell 中執行 bash script

雖然 fish 並不相容 bash，但仍可透過 [Bass](https://github.com/edc/bass) 來執行 bash script：

```bash
$ bass export X=3
```