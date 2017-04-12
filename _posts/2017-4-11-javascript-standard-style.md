---
layout: post
title: JavaScript Standard Style
---

JavaScript 本身對程式碼的撰寫風格（coding style）並沒有太多的規範，也因此容易寫出雜亂而難以維護的程式。

Lint 工具可以幫助我們以訂定好的規則來對程式碼進行檢查，除了能夠統一撰寫風格，也可以標示出語法結構上潛在的問題，協助提升程式碼品質（code quality）。

JavaScript 的語法檢查工具有許多種，其中大部分都要事先進行相當多的規則設定。而 [JavaScript Standard Style](https://standardjs.com/)（StandardJS）是一個整理出目前 JS 社群中所流行 coding style 的 Lint 工具，採直接固定規則（不須、也不可自行設定），並且能夠自動修正不合規範的程式碼。

## Flow 支援度
由於 StandardJS 目前供 Webpack 使用的 standard-loader 對 [Flow](https://flow.org/) 的支援度不佳。我們可以改用 [ESLint](http://eslint.org/) 搭配 StandardJS 風格指引的方式，達到一模一樣的效果，並方便之後結合 Flow 工具。

請參考：[ESLint](https://jackblackevo.github.io/eslint/)

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

vscode-standardjs 必須先於全域或專案開發環境中安裝好 StandardJS，否則不會啟用。

並且須到 VSCode 的「喜好設定 -> 設定」（settings.json）將 VSCode 內建的 JavaScript 驗證關閉：
{% highlight json %}
{
  "javascript.validate.enable": false
}
{% endhighlight %}

## 搭配 Webpack 2
先將 StandardJS 安裝為專案開發環境相依套件：
{% highlight shell %}
$ npm install standard --save-dev
{% endhighlight %}

再安裝 [standard-loader](https://www.npmjs.com/package/standard-loader) 為專案開發環境相依套件：
{% highlight shell %}
$ npm install standard-loader --save-dev
{% endhighlight %}

於 Webpack 2 設定檔中的 `module.rules` 加入模組處理規則：
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
* [eddychang.me - JavaScript裡的語句用分號結尾是個選項嗎](http://eddychang.me/blog/javascript/97-js-semicolon.html)
* [建立基本的 ES6 專案](https://jackblackevo.github.io/simple-es6-project/)