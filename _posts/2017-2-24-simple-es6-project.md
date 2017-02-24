---
layout: post
title: 建立基本的 ES6 專案
---

ECMAScript 2015 正式版本已經推出一年多了，雖然各大瀏覽器尚未完全支援，但是我們仍可以透過 NPM 建立專案，再搭配 Webpack、Babel 等工具來輔助開發。

## 建立 NPM 專案
於專案目中輸入指令：
{% highlight shell %}
$ npm init
{% endhighlight %}
依提示說明的步驟建立 package.json 設定檔。

## 安裝 Webpack 及開發伺服器
這裡我們直接安裝 Webpack 2，先使用 `npm install --global` 將 Webpack、Webpack Dev Server 安裝至全域中，以便我們使用 `webpack` 指令（已安裝可省略）：
{% highlight shell %}
$ npm install webpack --global
$ npm install webpack-dev-server --global
{% endhighlight %}

再搭配 `--save-dev` 參數安裝至專案目錄中：
{% highlight shell %}
$ npm install webpack --save-dev
$ npm install webpack-dev-server --save-dev
{% endhighlight %}
`--save-dev` 同時也會於 package.json 中將安裝的套件加入為開發環境相依套件。只要有設定好相依套件的 package.json，即可使用 `npm install` 再次安裝所有開發環境套件。

## 安裝 Babel 及 ES6 支援
搭配 `--save-dev` 參數安裝至專案目錄中：
{% highlight shell %}
$ npm install babel-loader --save-dev
$ npm install babel-core --save-dev
$ npm install babel-preset-es2015 --save-dev
{% endhighlight %}

## 專案目錄結構
※bundle.js 為轉譯後產出的檔案
{% highlight text %}
project
├── dist
│    ├── css
│    ├── js
│    │    └── (bundle.js)
│    └── index.html
├── src
│    └── index.js
├── node_modules
├── .babelrc
├── .gitignore
├── package.json
└── webpack.config.js
{% endhighlight %}

## webpack.config.js
Webpack 2 的設定檔和舊版不同，要特別注意！
{% highlight javascript %}
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // __dirname 代表此設定檔（webpack.config.js）的所在目錄
  context: path.join(__dirname, 'src'),
  // entry（進入點）檔案
  entry: './index.js',
  // 輸出設定
  output: {
    // 目標路徑
    path: path.join(__dirname, 'dist/js'),
    // 輸出檔名
    filename: 'bundle.js',
    // Webpack Dev Server 運作時
    // 並不會真的產出檔案，而是存放在記憶體中
    // 記憶體中的檔案，路徑會對應 publicPath 所設定的位置
    publicPath: '/js'
  },
  // entry 依賴的模組
  module: {
    // 設定模組規則
    rules: [{
      // 表示為所有的 *.js 檔案（正則表示法）
      test: /\.js$/,
      // 排除 node_modules 目錄（正則表示法）
      exclude: /node_modules/,
      use: [{
        // 使用的 loader（在 Webpack 2 不可簡寫成 'babel'）
        loader: 'babel-loader'
      }]
    }]
  }
};
{% endhighlight %}

## .babelrc
Babel 設定檔加上 ES6 轉譯規則：
{% highlight json %}
{
  "presets": ["es2015"]
}
{% endhighlight %}

## package.json
在 package.json 中的 `scripts` 加入：
{% highlight json %}
"start": "webpack-dev-server --progress --colors --inline --hot --content-base ./dist",
"build": "webpack --progress --colors"
{% endhighlight %}
方便我們使用 `npm run start`、`npm run build` 來執行自訂的指令：
* `npm run start` 啟動 Webpack Dev Server
* `npm run build` 使用 Webpack 進行打包

## .gitignore
專案相依套件的安裝目錄（node_modules）以及開發時所產生的檔案，能在 .gitignore 中設定讓 Git 排除，可以直接至 [gitignore.io](https://www.gitignore.io/) 取得製作好的[設定檔](https://www.gitignore.io/api/node)。