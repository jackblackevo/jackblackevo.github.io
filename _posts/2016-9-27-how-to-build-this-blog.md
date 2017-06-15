---
layout: post
title: Blog 建立步驟
---

使用 [jekyll-now](https://github.com/barryclark/jekyll-now) 主題，於本地端建立。第一次接觸，還很不熟悉 😅

環境為 macOS，已安裝 [Homebrew](http://brew.sh/index_zh-tw.html)。

1\. macOS 系統內建的 Ruby 版本比較舊，直接用 Homebrew 裝上新版：

```bash
$ brew install ruby
```

2\. 將新安裝的 Ruby 路徑加到環境變數中，並重載入 `.bash_profile` 設定檔：

```bash
$ echo "export PATH=$(brew --prefix ruby)/bin:$PATH" >> ~/.bash_profile
$ source ~/.bash_profile
```

3\. 使用 Ruby 的套件管理工具 `gem` 更新系統中已安裝的 Ruby 套件（如果沒有更新，可能會在建立 Jekyll 專案時出現 `No such file or directory - getcwd` 錯誤）：

```bash
$ gem update
```

4\. 依照 [Jekyll](https://jekyllrb.com/) 官網說明，安裝 Jekyyll 和 [Bundler](http://bundler.io/)：

```bash
$ gem install jekyll bundler
```

5\. 切換到要放置 Blog 檔案的目錄，建立 Jekyll 專案：

```bash
$ cd targetFolder
$ jekyll new .
```

6\. 將 jekyll-now 主題的檔案複製過來：

```bash
$ cp -r ~/XXX/themeFileFolder/ .
```

7\. 執行 Bundler 指令（還不知道詳細用處，猜是和 Ruby 套件的相依性有關）：

```bash
$ bundler install
```

8\. 最後以 Bundler 的設定啟動 Jekyll：

```bash
$ bundler exec jekyll serve
```
	
打開瀏覽器至 `http://localhost:4000` 就可以看到初始的 Blog 了。