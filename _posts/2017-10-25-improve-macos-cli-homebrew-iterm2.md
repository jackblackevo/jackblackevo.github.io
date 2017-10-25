---
layout: post
title: macOS CLI 環境建置：Homebrew、iTerm2
---

由於要把自己的 Macbook Pro 升級到 macOS 10.13 High Sierra，趁著乾淨安裝系統的機會也將自己愛用的 Command Line Interface 環境建置步驟記錄下來‌～

## Homebrew
[Homebrew](https://brew.sh/) 是 macOS 下知名的套件管理工具（[package manager](https://en.wikipedia.org/wiki/Package_manager)），我們可以透過 Homebrew 來安裝、管理眾多 CLI 下的指令工具。

### 安裝方式
* 依 [Homebrew 官網](https://brew.sh/index_zh-tw.html)說明，將安裝指令貼至終端機中
* Homebrew 會用到 [Xcode](https://developer.apple.com/xcode/) 的 Command Line Tools，並會在安裝過程中自動下載 Command Line Tools

### 使用方式及常用指令
因為 Homebrew 及其中的套件清單更新相當快速，所以通常在要尋找或安裝套件前都會先透過 `brew update` 指令來更新 Homebrew 以及套件清單。

| 指令 | 說明 |
|-----|------|
| `brew help` | Homebrew 使用說明 |
| `brew help [command]` | 查看 Homebrew 指令說明 |
| `brew update` | 取得最新版本的 Homebrew 及套件清單 |
| `brew list` | 檢視已安裝套件清單 |
| `brew info` | 查看所有已安裝套件資訊 |
| `brew info [formulae]` | 檢視套件資訊 |
| `brew search [formulae]` | 搜尋套件 |
| `brew install [formulae]` | 安裝套件 |
| `brew uninstall [formulae]` | 移除套件 |
| `brew outdated` | 檢查過期（已有新版）的套件 |
| `brew upgrade` | 更新所有套件 |
| `brew upgrade [formulae]` | 更新指定套件 |
| `brew --prefix` | 檢視 Homebrew 套件安裝路徑 |
| `brew --prefix [formulae]` | 檢視套件於 cellar 的路徑 |
| `brew link` | 為所有已安裝的套件建立 symbolic link 指向套件安裝路徑 |
| `brew unlink [formulae]` | 移除套件的 symbolic link |
| `brew prune` | 移除所有指向 Homebrew 套件安裝路徑的失效 symbolic link |
| `brew cleanup` | 移除舊版本的套件檔案 |
| `brew --cache` | 檢視 Homebrew 下載快取路徑 |
| `brew --cache [formulae]` | 檢視 Homebrew 套件下載快取檔案路徑 |

## Homebrew-Cask
[Homebrew-Cask](https://caskroom.github.io/) 是利用 Homebrew [第三方套件庫](https://docs.brew.sh/brew-tap.html)以及[外部指令](https://docs.brew.sh/External-Commands.html)的方式來擴充、增加 Homebrew 安裝管理 GUI 視窗程式或其他資源套件的能力。

### 安裝方式
Homebrew-Cask 由 `brew tap [user/repo]` 將套件庫加至 Homebrew 中（必須安裝 Homebrew）：

```bash
$ brew tap caskroom/cask
```

#### Caskroom-fonts
Homebrew-Cask 還有提供[字型套件庫](https://github.com/caskroom/homebrew-fonts)，若要使用可將套件庫將加入 Homebrew：

```bash
$ brew tap caskroom/fonts
```

### 使用方式及常用指令
和 Homebrew 相同，要使用 Homebrew-Cask 尋找或安裝套件前都會先透過 `brew update` 指令來更新 Homebrew-Cask 以及套件清單。

| 指令 | 說明 |
|-----|------|
| `brew cask help` | Homebrew-Cask 使用說明 |
| `brew cask list` | 檢視已安裝套件清單 |
| `brew cask info [formulae]` | 檢視套件資訊 |
| `brew cask search [formulae]` | 搜尋套件 |
| `brew cask install [formulae]` | 安裝套件 |
| `brew cask uninstall [formulae]` | 移除套件 |
| `brew cask outdated` | 檢查過期（已有新版）的套件 |
| `brew cask cleanup` | 移除下載套件的快取檔案 |

※Homebrew-Cask 為 Homebrew 的 [External Commands](https://docs.brew.sh/External-Commands.html)，所以使用 `brew cask` 相關指令便會自 /usr/local/Homebrew/Library/Homebrew/cask 中尋找 Homebrew-Cask 的指令來執行。

## iTerm2
[iTemr2](https://www.iterm2.com/) 是一套可以取代 macOS 系統內建 Terminal App 的終端機工具，它有佈景主題等功能可以幫助我們在 CLI 環境下工作時能更加方便、有效率的操作指令來完成目標。

### 安裝 iTerm2
使用 Homebrew-Cask 安裝 iTerm2：

```bask
$ brew cask install iterm2
```

### 設定顯示配色
iTerm2 預設的背景配色是漆黑的 Dark Background，要修改成喜歡的配色可以從選單列：

* iTerm2 -> Preferences -> Profiles -> Colors

選擇 Color Presets… 為要套用的內建配色組合，我愛用的是 Solarized Dark。

### 設定顯示字型
要更改顯示字型可以從選單列：

* iTerm2 -> Preferences -> Profiles -> Text

點擊 Change Font 按鈕更改字型。

### 開啟 Word Jumps
要透過以下組合鍵進行單字跳躍：

| 組合鍵 | 組合鍵 | 說明 |
|-----|------|------|
| <kbd>⌥</kbd> + <kbd>F</kbd> | <kbd>⌥</kbd> + <kbd>→</kbd> | 向前（右）跳躍一個單字 |
| <kbd>⌥</kbd> + <kbd>B</kbd> | <kbd>⌥</kbd> + <kbd>←</kbd> | 向後（左）跳躍一個單字 |

須要先開啟 Word Jumps，從選單列：

* iTerm2 -> Preferences -> Profiles -> Keys

將左 <kbd>⌥</kbd> 設定為 +Esc，選項為以下其一（iTerm2 版本不同可能會不一樣）：

* Left option (⌥) key acts as: +Esc
* Left ⌥ key: +Esc

### 還原至預設值
根據 iTerm2 官網的 [FAQ 說明](http://iterm2.com/faq.html)，要將設定還原成預設值可以透過以下指令刪除設定檔來達成：

```bash
$ defaults delete com.googlecode.iterm2
```

## 額外參考
* [Homebrew - Documentation](https://docs.brew.sh/)
* [Homebrew-Cask - How to Use Homebrew-Cask](https://github.com/caskroom/homebrew-cask/blob/master/USAGE.md)
* [iTerm2 - Documentation](http://iterm2.com/documentation.html)