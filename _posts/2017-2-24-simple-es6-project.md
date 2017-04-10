---
layout: post
title: 建立基本的 ES6 專案
---

ECMAScript 2015 正式版本已經推出一年多了，雖然各大瀏覽器尚未完全支援，但是我們仍可以透過 NPM 建立專案，再搭配 Webpack、Babel 等工具來輔助開發。

## 建立專案目錄
建立專案專案目錄，並建立子目錄 dist、src：
* dist 為開發完畢時要發布的檔案所在目錄，放置 HTML、CSS 以及轉譯後 JavaScript
* src 則是撰寫 ES6 的開發目錄

{% highlight text %}
project
├── dist
└── src
{% endhighlight %}

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

再搭配 `--save-dev` 參數（待下方說明）安裝至專案目錄中：
{% highlight shell %}
$ npm install webpack --save-dev
$ npm install webpack-dev-server --save-dev
{% endhighlight %}

## 安裝 Babel 及 ES6 支援
搭配 `--save-dev` 參數安裝至專案目錄中：
{% highlight shell %}
$ npm install babel-loader --save-dev
$ npm install babel-core --save-dev
$ npm install babel-preset-es2015 --save-dev
{% endhighlight %}

## 相依套件
`npm install` 的 `--save-dev` 參數除了安裝套件外，同時也會於 package.json 中將安裝的套件加入為開發環境相依套件（package.json 中的 `devDependencies` 屬性）。

另外，如果專案中有使用外部套件（如：React），則可以使用 `--save` 參數來安裝並加入為正式環境相依套件（package.json 中的 `dependencies` 屬性）。

`devDependencies` 與 `dependencies` 的差別在於：
* `devDependencies`：僅開發時使用
* `dependencies`：開發時期及正式產品皆須要使用

只要有設定好相依套件屬性的 package.json，即可透過 `npm install` 再次安裝開發及正式環境依賴的所有套件。

## 建立設定檔案
現在專案目錄中多了 package.json 設定檔，以及放置安裝套件的 node_modules 目錄。接下來要在專案目錄下建立：webpack.config.js、.babelrc、.gitignore 設定檔。

{% highlight text %}
project
├── dist
├── src
├── node_modules
├── .babelrc
├── .gitignore
├── package.json
└── webpack.config.js
{% endhighlight %}

### webpack.config.js
Webpack 2 的設定檔和舊版不同，要特別注意！
{% highlight javascript %}
// 載入 Node.js 的 path 模組
const path = require('path')
// 載入 webpack 模組
const webpack = require('webpack')

// Webpack 2 設定值
// 定義開發與正式共用的設定值
const webpackConfig = {
  // 專案根目錄路徑（本機路徑，須為絕對路徑）
  // 預設值為 webpack 指令作用的工作目錄（current working directory, CWD）
  // __dirname 為此 Webpack 2 設定檔模組的所在目錄
  context: path.join(__dirname, 'src'),
  // Entry（進入點）檔案路徑（基於 context）
  // 專案應用程式會由 Entry 啟動，並引入依賴模組
  entry: {
    index: './index.js'
  },
  // 輸出設定
  output: {
    // 輸出檔的目標位置（本機路徑，須為絕對路徑）
    path: path.join(__dirname, 'dist', 'js'),
    // 輸出檔名
    filename: 'bundle.js',
    // 輸出檔於伺服器公開位置中的絕對路徑
    // 開啟 Hot-Reload 時，此選項為必要設定
    // 因 Hot Module Replacement 須由此位置檢查更新的檔案
    publicPath: '/js/'
  },
  // 模組設定
  // Webpack 將專案中所有的資源（asset）檔案皆視為模組
  // 在此設定如何處理專案中各種不同類型的資源模組（即檔案）
  module: {
    // 模組處理規則
    rules: [
      {
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
        // Loader 可以載入指定的資源，並進行輸出轉換
        // Webpack 本身只支援 JavaScript 模組
        // 是藉由 Loader 來支源其它不同類型的資源
        // Loader 最後會將資源輸出為字串，Webapck 再包裝成 JavaScript 模組
        // 作用的順序是由陣列中最末項的 Loader 開始，再依序往前
        // 將轉換過的結果交由前一項索引的 Loader 繼續處理
        use: [
          {
            // Loader 名稱在 Webpack 2 不可省略 '-loader' 後綴
            loader: 'babel-loader'
          }
        ]
      }
    ]
  },
  // 插件
  plugins: [
    // 宣告一個全域的常數並賦值，讓進入點及其相依的模組使用
    new webpack.DefinePlugin({
      // 將 Node.js 環境的 process.env.NODE_ENV 宣告為全域常數
      // 讓瀏覽器環境也可以取用 process.env.NODE_ENV
      // 實際上 DefinePlugin 是以直接替換文字的方式運作
      // 所以賦值的時候，值若是字串則寫法必須特別處理
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
    })
  ]
}

if (process.env.NODE_ENV === 'production') {
  // 若要編譯成正式產品，使用以下設定值：
  // 加入插件
  webpackConfig.plugins.push(
    // 最小化 JavaScript 檔案
    new webpack.optimize.UglifyJsPlugin(),
    new webpack.optimize.OccurrenceOrderPlugin()
  )

} else {
  // 開發階段執行，則使用以下設定值：
  // 產生原始碼映射表（Source Map），方便開發時除錯
  webpackConfig.devtool = 'cheap-module-eval-source-map'
  // Webpack Dev Server（WDS）設定
  webpackConfig.devServer = {
    // 伺服器根目錄位置（本機路徑，建議使用絕對路徑）
    contentBase: path.join(__dirname, 'dist'),
    // 開啟 inline mode（檔案有更新時自重整頁面）
    inline: true,
    // 開啟 Hot-Reload
    // 檔案有更新時，僅熱抽換該模組（支援的檔案才有效果）
    // 須搭配 Hot Module Replacement 插件
    hot: true,
    // 自動開啟瀏覽器
    open: true,
    port: 9000,
    // 因 Webpack Dev Server 運作時
    // 並不會真的產出轉換後的檔案，而是存放在記憶體中
    // 記憶體中的檔案，路徑會對應 devServer.publicPath 所設定的位置
    // 建議與 output.publicPath 一致
    // 若開啟 Hot-Reload，則必須與 output.publicPath 一致
    publicPath: '/js/',
    stats: {
      colors: true
    }
  }
  // 加入插件
  webpackConfig.plugins.push(
    // Hot Module Replacement（HMR）
    new webpack.HotModuleReplacementPlugin(),
    // Hot-Reload 時在瀏覽器 Console 顯示更新的檔案名稱
    new webpack.NamedModulesPlugin()
  )

}

// 將全部設定輸出為 Node.js 模組，供 Webpack 2 使用
module.exports = webpackConfig
{% endhighlight %}

### .babelrc
Babel 設定檔加上 ES6 轉譯規則：
{% highlight json %}
{
  "presets": [
    [
      "es2015",
      {
        // 不將 ES2015 模組轉譯成 CommonJS 模組
        "modules": false
      }
    ]
  ]
}
{% endhighlight %}

### package.json
設定 package.json 中的 `scripts` 屬性（若未有此屬性可自行新增）值為：
{% highlight json %}
{
  "dev": "webpack-dev-server --progress",
  "build": "NODE_ENV=production webpack --progress"
}
{% endhighlight %}
方便我們使用 `npm run dev`、`npm run build` 來執行自訂的指令：
* `npm run dev` 啟動 Webpack Dev Server
* `npm run build` 使用 Webpack 進行編譯打包

透過 `npm run` 實際呼叫的是安裝在專案中的 Webpack、Webpack Dev Server，而不是全域中的套件。

### .gitignore
專案相依套件的安裝目錄（node_modules）以及開發時所產生的檔案，能在 .gitignore 中設定讓 Git 排除，可以直接至 [gitignore.io](https://www.gitignore.io/) 取得製作好的[設定檔](https://www.gitignore.io/api/node)。

## 完整專案結構
完成所有設定之後，便可以將 HTML、CSS 置於 dist 中，並在 src 開始撰寫 ES6 程式。

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

※bundle.js 為轉譯後產出的檔案

### 開啟 Hot-Reload 支援
除了 webpack.config.js 中的設定，index.js 也必須加上 HMR 的 API 呼叫：
{% highlight javascript %}
if (module.hot) {
  module.hot.accept('./es6module', () => {
    // 設定當指定的模組（範例為 es6module.js）在 HMR 完成後要執行的事情
    // 通常為重新讀取這個模組並更新網頁畫面

  })

}
{% endhighlight %}
讓 HMR 偵測 index.js 所相依的模組有更新檔案內容時，可以依我們的設定進行處理。若是 HMR 未被啟用，則此段程式便不會執行。

## 完整範例專案
本篇完整的範例專案可以在 GitHub 上看到：[simple-es6-webpack-project](https://github.com/jackblackevo/simple-es6-webpack-project)

## 額外參考
* [Webpack - Concepts](https://webpack.js.org/concepts/)
* [Webpack - Configuration](https://webpack.js.org/configuration/)
* [Webpack - Migrating from v1 to v2](https://webpack.js.org/guides/migrating/)
* [Webpack - DefinePlugin](https://webpack.js.org/plugins/define-plugin/)
* [Webpack - Devtool](https://webpack.js.org/configuration/devtool/)
* [Webpack - DevServer](https://webpack.js.org/configuration/dev-server/)
* [Webpack - Hot Module Replacement - React](https://webpack.js.org/guides/hmr-react/#using-webpack-with-a-config)
* [Webpack - hot module replacement with webpack](https://github.com/webpack/docs/wiki/hot-module-replacement-with-webpack)
* [Node.js Documentation - path.join([...paths])](https://nodejs.org/api/path.html#path_path_join_paths)
* [Node.js Documentation - __dirname](https://nodejs.org/docs/latest/api/globals.html#globals_dirname)
* [Source Map(原始碼映射表) - eddychang.me](http://eddychang.me/blog/javascript/76-source-map.html)