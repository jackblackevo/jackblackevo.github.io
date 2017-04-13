---
layout: post
title: Flow-Typed
---

打開 Flow 的型別檢查功能後，馬上可以發現一些第三方套件因為沒有撰寫 Flow 的型別註解（Type Annotation）使得 Flow 跳出了檢查錯誤。我們可以利用 [Flow-Typed](https://github.com/flowtype/flow-typed) 下載對第三方套件的型別定義檔案。

## 安裝 Flow-Typed
由於我們僅使用 Flow-Typed 下載專案相依的第三方套件型別定義檔，所以只須安裝至全域來使用 `flow-typed` 指令：
{% highlight shell %}
$ npm install flow-typed --global
{% endhighlight %}

## 使用 Flow-Typed 下載第三方套件型別定義檔
於專案根目錄中輸入指令：
{% highlight shell %}
$ flow-typed install
{% endhighlight %}

Flow-Typed 便會開始搜尋專案所依賴的套件，並下載型別定義檔案至新建立的 flow-typed 目錄中。

## 設定 .gitignore
Flow-Typed 會於專案中建立 flow-typed 目錄放置第三方套件型別定義檔，我們可以設定 Git 排除此目錄。

將目錄名稱加入 .gitignore 設定檔中：
{% highlight txt %}
flow-typed/
{% endhighlight %}

## 額外參考
* [Flow - Introducing Flow-Typed](https://flow.org/blog/2016/10/13/Flow-Typed/)
* [Flow - Library Definitions](https://flow.org/en/docs/libdefs/)
* [Flow - .flowconfig [libs]](https://flow.org/en/docs/config/libs/)
* [Flow](https://jackblackevo.github.io/flow/)
* [建立基本的 ES6 專案](https://jackblackevo.github.io/simple-es6-project/)