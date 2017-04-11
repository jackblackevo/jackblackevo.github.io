---
layout: post
title: JavaScript Standard Style
---

JavaScript 對程式碼的撰寫風格（coding style）沒有太多的規範，也因此容易寫出雜亂難以維護的程式。

Lint 工具可以幫助我們以訂定好的規則來對程式碼進行檢查，除了能夠統一 code style，也可以標示出語法結構上潛在的問題，協助提升程式碼品質（code quality）。

JavaScript 的語法檢查工具有許多種，其中大部分都要事先進行相當多的規則設定。[JavaScript Standard Style](https://standardjs.com/)（StandardJS）是一個整理出目前 JS 社群流行 code style 的 Lint 工具，並且可自動修正不合規範的程式碼。

## 安裝 StandardJS
先將 StandardJS 安裝至全域中，以便我們使用 `standard` 指令：
{% highlight shell %}
$ npm install standard --global
{% endhighlight %}

安裝為專案開發環境相依套件：
{% highlight shell %}
$ npm install standard --save-dev
{% endhighlight %}

## Visual Studio Code extension
[Visual Studio Code](https://code.visualstudio.com/)（VSCode）可以再加裝 [vscode-standardjs](https://marketplace.visualstudio.com/items/chenxsan.vscode-standardjs) 插件，讓我們可以在撰寫程式碼時就即時看到檢查提示及可自動修正的選項。

vscode-standardjs 必須先於全域或專案開發環境中安裝好 StandardJS，否則不會啟用。並且須到 VSCode 的「喜好設定 -> 設定」（settings.json）將 VSCode 內建的 JavaScript 驗證關閉。

## 搭配 Webpack 2
先將 StandardJS 安裝為專案開發環境相依套件：
{% highlight shell %}
$ npm install standard --save-dev
{% endhighlight %}

再於 Webpack 2 設定檔中的 module.rules 加入模組處理規則：
{% highlight javascript %}
{
  // 設定為預先應用的規則（在其它規則前先作用）
  enforce: 'pre',
  // 資源檔案篩選條件
  resource: {
    // 須符合正則表示法條件
    test: [
      // 表示作用在所有 *.js 檔案
      /\.js$/
    ],
    // 須排除路徑條件（本機路徑，須為絕對路徑）
    exclude: [
      // 表示排除專案中的 node_modules 目錄
      path.join(__dirname, 'node_modules')
    ]
  },
  // 應用於此處理規則的 Loaders（轉換器）
  use: [
    {
      loader: 'standard-loader'
    }
  ]
}
{% endhighlight %}

## 額外參考
* [standard-loader](https://github.com/timoxley/standard-loader)
* [eddychang.me - JavaScript裡的語句用分號結尾是個選項嗎](http://eddychang.me/blog/javascript/97-js-semicolon.html)
* [建立基本的 ES6 專案](https://jackblackevo.github.io/simple-es6-project/)