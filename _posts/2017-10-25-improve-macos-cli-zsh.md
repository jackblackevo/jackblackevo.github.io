---
layout: post
title: macOS CLI 環境建置：Zsh
---

前一篇安裝好 iTerm2 及 Homebrew 後，這次要將 Shell 從 bash 改為更強大的 [Zsh](https://www.zsh.org/)。Zsh 擁有自動補齊指令、路徑、變數等等的功能，並可以依據自身需求再加裝 plug-ins。

雖然 Zsh 相較於之前介紹過的 fish 仍有許多不足之處，但 Zsh 最大的優勢在於相容於 bash，不必重新學習特有的 shell script。不得不說 fish 的自動補齊以及指令提示真的非常強大，尤其是使用 `git add [deep/path/file]` 時還可以只打部分檔名再加 <kbd>Tab</kbd> 就能補齊正確路徑！

## 安裝 Zsh
macOS 已內建 Zsh，但版本較舊。我們可以直接使用 Homebrew 來安裝最新版：

```bash
$ brew install zsh
```

## 將 Zsh 設為預設 shell
先將我們透過 Homebrew 安裝的 Zsh 路徑加入 /etc/shells 中：

```bash
$ echo $(which zsh) | sudo tee -a /etc/shells
```

再使用以下指令將 Homebrew 安裝的 Zsh 設為預設 shell：

```bash
$ chsh -s $(which zsh)
```

### 只在 iTerm2 中使用 Zsh
如果只想在 iTerm2 中使用 Zsh，可以從選單列：

* iTerm2 -> Preferences -> Profiles -> General

設定 Command 為 /usr/local/bin/zsh。

### 將預設 shell 改回 bash
若要將預設 shell 從 Zsh 改回 bash：

```bash
$ chsh -s /bin/bash
```

## 初始化設定
將預設 shell 設定為 Zsh 並重新載入，或使用 `zsh` 指令進入 Zsh 後，第一次會看到 zsh-newuser-install 的初始化設定畫面：

```
This is the Z Shell configuration function for new users,
zsh-newuser-install.
You are seeing this message because you have no zsh startup files
(the files .zshenv, .zprofile, .zshrc, .zlogin in the directory
~).  This function can help you with a few settings that should
make your use of the shell easier.

You can:

(q)  Quit and do nothing.  The function will be run again next time.

(0)  Exit, creating the file ~/.zshrc containing just a comment.
     That will prevent this function being run again.

(1)  Continue to the main menu.

--- Type one of the keys in parentheses ---

```

zsh-newuser-install 可以協助設定並產生 .zshrc 設定檔，這裡我們先選擇 0：僅建立但不設定 .zshrc，稍後由 Oh My Zsh 或是 Antigen 來協助設定 Zsh。

若之後想重新透過 zsh-newuser-install 設定 Zsh，則可在備分 .zshrc 設定檔後，執行以下指令：

```bash
$ autoload -U zsh-newuser-install; zsh-newuser-install -f
```

## Oh My Zsh
Zsh 有豐富的 plug-in 資源，可以大幅擴充 Zsh 的功能。但眾多的 plug-in 管理起來，不論是安裝、更新或移除都相當麻煩。我們可以借助 Zsh 組態框架——[Oh My Zsh](http://ohmyz.sh/) 來簡化插件與相關設定的管理。

依據 [Oh My Zsh 的 GitHub](https://github.com/robbyrussell/oh-my-zsh) 說明，執行以下指令來安裝：

```bash
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### Theme
Oh My Zsh 內建了多種佈景主題，預設為 robbyrussell。若要更改，可以修改 .zshrc：

```
ZSH_THEME="[theme]"
```

安裝第三方的 Theme 則可以按照以下步驟：

1. 下載佈景主題（建議使用 `git clone`）至 `$ZSH_CUSTOM/themes/`（變數 `$ZSH_CUSTOM` 會指向 ~/.oh-my-zsh/custom）
2. 修改 .zshrc 設定檔中的 `ZSH_THEME`

#### powerlevel9k
因為 [Powerlevel9k](https://github.com/bhilburn/powerlevel9k) 主題使用了 Powerline 字型，我們先安裝 [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts) 字型（以 Knack Nerd Font 為例）：

```bash
$ brew cask install font-hack-nerd-font
```

再下載 Powerlevel9k：

```bash
$ git clone https://github.com/bhilburn/powerlevel9k.git $ZSH_CUSTOM/themes/powerlevel9k
```

修改 .zshrc 設定檔中的 `ZSH_THEME`：

```
ZSH_THEME="powerlevel9k/powerlevel9k"
```

並於 .zshrc 中加入以下設定來開啟 Powerlevel9k 主題對 Nerd Fonts 字型的支援（須在套用主題設定之前）：

```
POWERLEVEL9K_MODE='nerdfont-complete'
```

重新載入 Zsh 設定檔：

```bash
$ source ~/.zshrc
```

以及在 iTerm2 中設定好使用 Knack Nerd Font 字型。

### Plug-ins
Oh My Zsh 的套件安裝方式可依以下步驟：

1. Plug-in 分為 Oh My Zsh 內建、或是自行下載
	* **內建之 plug-in：**無須額外的下載操作
	* **自行下載之 plug-in：**將 plug-in 下載（建議使用 `git clone`）至 `$ZSH_CUSTOM/plugins/`（變數 `$ZSH_CUSTOM` 會指向 ~/.oh-my-zsh/custom）
2. 修改 .zshrc 設定檔中的 `plugins`：

	```
	plugins=(… [plugin])
	```

3. 重新載入 Zsh 設定檔：

	```bash
	$ source ~/.zshrc
	```

### NVM
#### 使用 Homebrew 安裝
**Homebrew 上的版本已不在維護，不建議使用此方式安裝。**

Oh My Zsh 內建對 [NVM](https://github.com/creationix/nvm) 的支援，首先還是要安裝好 NVM：

```bash
$ brew install nvm
```

再於使用者家目錄下建立 .nvm 資料夾，以及指向 NVM 安裝路徑的 symbolic link：

```
$ mkdir ~/.nvm
$ ln -s $(brew --prefix nvm)/nvm.sh ~/.nvm/nvm.sh
```

再修改 .zshrc 設定檔中的 `plugins` 加入 nvm。

#### 透過 zsh-nvm 安裝
**由於 nvm 已不繼續支援透過 Homebrew 的安裝方式**，因此我們使用 [zsh-nvm](https://github.com/lukechilds/zsh-nvm) 來安裝：

```bash
git clone https://github.com/lukechilds/zsh-nvm $ZSH_CUSTOM/plugins/zsh-nvm
```

再修改 .zshrc 設定檔中的 `plugins` 加入 zsh-nvm。

### z
Oh My Zsh 內建對 [Z](https://github.com/rupa/z) 的支援，首先還是要安裝好 Z：

```bash
$ brew install z
```

再修改 .zshrc 設定檔中的 `plugins` 加入 z。

<!--### zsh-completions
```bash
$ git clone https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions
$ autoload -U compinit && compinit
```-->

<!--### npm
提供 npm 指令的提示，須要先安裝好 npm 再修改 .zshrc 設定檔中的 `plugins` 加入 npm。-->

### zsh-autosuggestions
[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) 是一個提供指令提示功能的 plug-in：

```bash
$ git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

再修改 .zshrc 設定檔中的 `plugins` 加入 zsh-autosuggestions。

如果 iTerm2 使用了暗色調的配色，可能會看不到自動提示。可以執行以下指令修改提示字顏色：

```bash
$ echo "ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10'" >> ~/.zshrc
$ source ~/.zshrc
```

### zsh-syntax-highlighting
[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) 是一個提供語法高亮（syntax highlighting）功能的 plug-in：

```bash
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

再修改 .zshrc 設定檔中的 `plugins` 加入 zsh-syntax-highlighting。

<!--### zsh-history-substring-search
```bash
$ git clone https://github.com/zsh-users/zsh-history-substring-search $ZSH_CUSTOM/plugins/zsh-history-substring-search
```-->

<!--### history-search-multi-word
[history-search-multi-word](https://github.com/zdharma/history-search-multi-word) 是一個提供以隨打即找的方式來搜尋指令歷史操作的 plug-in：

```bash
$ git clone https://github.com/zdharma/history-search-multi-word.git $ZSH_CUSTOM/plugins/history-search-multi-word
```

再修改 .zshrc 設定檔中的 `plugins` 加入 history-search-multi-word。-->

### bd
[bd](https://github.com/Tarrasch/zsh-bd) 是一個提供直接跳至目前所在路徑的特定名稱之父目錄的 plug-in：

```bash
$ git clone https://github.com/Tarrasch/zsh-bd.git $ZSH_CUSTOM/plugins/bd
```

再修改 .zshrc 設定檔中的 `plugins` 加入 bd。

### fzf
[fzf](https://github.com/junegunn/fzf) 是一個模糊搜尋工具，可以輸入關鍵字的方式尋找檔案或目錄。fzf 不是 Zsh 的 plug-in，但為許多隨打即找工具的相依套件。

```bash
$ brew install fzf
```

接著安裝 fzf 的 key bindings 及 fuzzy completion 功能：

```bash
$ $(brew --prefix)/opt/fzf/install
```

安裝後會於 .zshrc 設定檔中新增：

```bash
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
```

### zsh-interactive-cd
[zsh-interactive-cd](https://github.com/changyuheng/zsh-interactive-cd) 是一個提供以隨打即找的方式來切換目錄的 plug-in。

※須要先安裝 [fzf](#fzf)

```bash
$ git clone https://github.com/changyuheng/zsh-interactive-cd.git $ZSH_CUSTOM/plugins/zsh-interactive-cd
```

再修改 .zshrc 設定檔中的 `plugins` 加入 zsh-interactive-cd。

### fz
[fz](https://github.com/changyuheng/fz) 是一個提供以隨打即找的方式自歷史目錄中切換的 plug-in。

※須要先安裝 [fzf](#fzf) 以及 [z](#z)

```bash
$ git clone https://github.com/changyuheng/fz.git $ZSH_CUSTOM/plugins/fz
```

再修改 .zshrc 設定檔中的 `plugins` 加入 fz。

## Antigen
除了直接使用 Oh My Zsh 來管理 Zsh 插件外，另一個選擇是透過 [Antigen](https://github.com/zsh-users/antigen)。

Antigen 是一個 Zsh 的 plug-in 管理工具，可以只透過設定檔的方式來管理所有插件。不用自己下載、更新 plug-in，甚至能把 Oh My Zsh 也當作插件來管理。

```bash
$ brew install antigen
```

### 使用 Antigen 管理 Zsh Plug-ins
以上面所列的插件為例，編輯 .zshrc 設定檔：

```
# 開啟 Powerlevel9k 主題對 Nerd Fonts 字型的支援
POWERLEVEL9K_MODE='nerdfont-complete'

# 載入 Antigen
source /usr/local/share/antigen/antigen.zsh

# 透過 Antigen 來使用 Oh My Zsh
antigen use oh-my-zsh

# 透過 Antigen 載入 Oh My Zsh 內建 theme
# antigen theme robbyrussell

# 透過 Antigen 使用其他 Zsh theme
antigen theme bhilburn/powerlevel9k

# 透過 Antigen 使用 Oh My Zsh 內建 plug-ins
antigen bundle git
antigen bundle z

# 透過 Antigen 使用其他 Zsh plug-ins
antigen bundle lukechilds/zsh-nvm
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-syntax-highlighting
antigen bundle Tarrasch/zsh-bd
antigen bundle changyuheng/zsh-interactive-cd
antigen bundle changyuheng/fz

# 套用 Antigen 設定
antigen apply

# 修改提示字顏色
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10'
```

只要 Zsh 設定檔有異動，必須使用以下指令來讓 Antigen 載入新的設定：

```bash
$ antigen reset
```

再重新載入 Zsh：

```bash
$ zsh
```

### 常用指令

| 指令 | 說明 |
|-----|------|
| `antigen help` | Antigen 使用說明 |
| `antigen reset` | 清除快取 |
| `antigen list` | 查看已安裝的 Zsh Plug-in |
| `antigen update` | 更新所有 Zsh Plug-in |
| `antigen update [plugin]` | 更新指定 Zsh Plug-in |
| `antigen cleanup` | 清除未使用 Zsh Plug-in 的 Git Repo |
| `antigen purge [plugin]` | 移除 Zsh Plug-in |

## 額外參考
* [Oh My Zsh - Plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)
* [Oh My Zsh - External plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/External-plugins)
* [Oh My Zsh - Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)
* [Oh My Zsh - External themes](https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes)
* [Powerlevel9k - Install Instructions](https://github.com/bhilburn/powerlevel9k/wiki/Install-Instructions)
* [Antigen - Commands](https://github.com/zsh-users/antigen/wiki/Commands)
