---
layout: post
title: 以 fish 做為預設的 shell
---

[fish shell](https://fishshell.com/) 是可以代替 macOS 預設 bash 的另一個 shell 選擇。fish 有語法高亮（syntax highlighting）以及指令提示等功能，對常使用 CLI 的開發者來說相當便利。

## 安裝 iTerm2
由於 macOS 預設的 Terminal 終端機過於陽春，所以我們使用 [iTerm2](https://www.iterm2.com/) 來搭配 fish shell。

使用 [Homebrew Cask](https://caskroom.github.io/) 安裝 iTerm2：
{% highlight shell %}
$ brew cask install iterm2
{% endhighlight %}

## 安裝 fish shell
{% highlight shell %}
$ brew install fish
{% endhighlight %}

## 將 fish 設為預設 shell
{% highlight shell %}
$ echo "/usr/local/bin/fish" | sudo tee -a /etc/shells
$ chsh -s /usr/local/bin/fish
{% endhighlight %}

### 只在 iTerm2 中使用 fish
如果只想在 iTerm2 中使用 fish，可以在：

iTerm 2 -> Preferences -> Profiles -> General

設定 Command 為 /usr/local/bin/fish

### 將預設 shell 改回 bash
若要將預設 shell 從 fish 改回 bash：
{% highlight shell %}
$ chsh -s /bin/bash
{% endhighlight %}

## 設定 iTerm2 顯示顏色
iTerm 2 -> Preferences -> Profiles -> Colors

選擇 Color Presets… 為 Solarized Dark

## 設定 fish shell 環境變數
由於 fish 並不相容 bash，所以環境變數的設定也不太一樣。

這裡以 Homebrew 安裝的 Ruby 做為範例，原本在 bash 的環境變數設定指令：
{% highlight shell %}
$ echo "export PATH=$(brew --prefix ruby)/bin:$PATH" >> ~/.bash_profile
{% endhighlight %}

在 fish 則是：
{% highlight shell %}
$ echo "set -g -x PATH (brew --prefix ruby)/bin $PATH" >> ~/.config/fish/config.fish
{% endhighlight %}

## 解決 NVM 無法在 fish shell 下執行問題
同樣因為 fish 不相容 bash，也導致 NVM 中的 nvm.sh 無法正常在 fish 中執行。我們可以透過 fish 的套件管理工具—[fisherman](https://fisherman.github.io/) 來安裝 [Fish-nvm](https://github.com/fisherman/nvm) 以支援 NVM。

假設已透過 Homebrew 安裝完 NVM，接著安裝 fisherman：
{% highlight shell %}
$ curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs git.io/fisher
{% endhighlight %}

透過 fisherman 安裝 Fish-nvm：
{% highlight shell %}
$ fisher nvm
{% endhighlight %}

建立 symbolic link 指向由 Homebrew 所安裝 NVM 的 nvm.sh，讓 Fish-nvm 能夠找到：
{% highlight shell %}
$ ln -s (brew --prefix nvm)/nvm.sh ~/.nvm/nvm.sh
{% endhighlight %}

設定 NVM 的 `NVM_DIR` 環境變數：
{% highlight shell %}
$ echo "set -g -x NVM_DIR $HOME/.nvm" >> ~/.config/fish/config.fish
{% endhighlight %}

再設定於 fish 載入時以 [Bass](https://github.com/edc/bass) 執行 nvm.sh，讓 NVM 自動使用預設 Node.js 版本：
{% highlight shell %}
$ echo "bass source (brew --prefix nvm)/nvm.sh" >> ~/.config/fish/config.fish
{% endhighlight %}
※這是為了解決在執行 `node` 之前，`npm` 指令無法使用的問題：
{% highlight text %}
env: node: No such file or directory
{% endhighlight %}

設定完畢後重新載入 fish shell 設定檔即可：
{% highlight shell %}
$ source ~/.config/fish/config.fish
{% endhighlight %}