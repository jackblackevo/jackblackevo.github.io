---
layout: post
title: Blog 建立步驟
---

使用 [Jekyll Now](https://github.com/barryclark/jekyll-now) 主題，於本地端建立。第一次接觸，還很不熟悉 😅

環境為 macOS，使用系統內建的 Ruby（若要更新版本，請參考文末連結）。

1\. 使用 Ruby 的套件管理工具 `gem` 更新系統中已安裝的 Ruby 套件（如果沒有更新，可能會在建立 Jekyll 專案時出現 `No such file or directory - getcwd` 錯誤）：

```bash
$ gem update
```

2\. 依照 [Jekyll](https://jekyllrb.com/) 官網說明，安裝 Jekyll 和 [Bundler](http://bundler.io/)：

```bash
$ gem install jekyll bundler
```

3\. 切換到要放置 Blog 檔案的目錄，建立 Jekyll 專案：

```bash
$ cd target_folder
$ jekyll new .
```

4\. 將 jekyll-now 主題的檔案複製過來：

```bash
$ cp -r ~/XXX/theme_fileFolder/ .
```

或也可以直接加入 jekyll-now 的 repo 再 merge squash 過來：

```bash
$ git remote add jekyll-now git@github.com:barryclark/jekyll-now.git
$ git merge --squash jekyll-now/master --allow-unrelated-histories
```

5\. 執行 Bundler 指令（還不知道詳細用處，猜是和 Ruby 套件的相依性有關）：

```bash
$ bundler install
```

6\. 最後以 Bundler 的設定啟動 Jekyll：

```bash
$ bundler exec jekyll serve
```
	
打開瀏覽器至 `http://localhost:4000` 就可以看到初始的 Blog 了。

## 額外參考
* [更新 macOS 中的 Ruby](https://jackblackevo.github.io/upgrade-ruby-on-macos)