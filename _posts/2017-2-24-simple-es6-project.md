---
layout: post
title: 建立基本的 ES6 專案
---

ECMAScript 2015 正式版本已經推出一年多了，雖然各大瀏覽器尚未完全支援，但是我們仍可以透過 NPM 建立專案，再搭配 Webpack、Babel 等工具來輔助開發。

## 建立專案目錄
建立專案專案目錄，並建立子目錄 src：
* dist 為開發完畢時要發布的檔案所在目錄，放置 HTML、CSS 以及轉譯後 JavaScript
* src 則是撰寫 ES6 的開發目錄

```
project
└── src
```

## 建立 NPM 專案
於專案目中輸入指令：

```bash
$ npm init
```

依提示說明的步驟建立 package.json 設定檔。

## 安裝 Webpack 及開發伺服器
這裡我們直接安裝 Webpack 3，先使用 `npm install` 加上 `--global` 或 `-g` 參數將 Webpack、Webpack Dev Server 安裝至全域中，以便我們使用 `webpack` 指令（已安裝可省略）：

```bash
$ npm install webpack --global
$ npm install webpack-dev-server --global
```

※`npm install` 可偷懶簡寫成 `npm i`

再搭配 `--save-dev` 或 `-D` 參數（待下方說明）安裝至專案目錄中：

```bash
$ npm install webpack --save-dev
$ npm install webpack-dev-server --save-dev
```

## 安裝 HtmlWebpackPlugin
搭配 `--save-dev` 或 `-D` 參數安裝至專案目錄中：

```bash
$ npm install html-webpack-plugin --save-dev
```

## 安裝 Babel 及 ES6 支援
搭配 `--save-dev` 或 `-D` 參數安裝至專案目錄中：

```bash
$ npm install babel-loader --save-dev
$ npm install babel-core --save-dev
$ npm install babel-preset-es2015 --save-dev
```

## 相依套件
`npm install` 的 `--save-dev` 參數（簡寫為 `-D`）除了安裝套件外，同時也會於 package.json 中將安裝的套件加入為開發環境相依套件（package.json 中的 `devDependencies` 屬性）。如果僅用 `npm install [package]` 而未加參數，則只會單純的將套件安裝至專案中的 node_modules 目錄。

另外，如果專案中有使用外部套件（如：React），則可以使用 `--save` 參數來安裝並加入為正式環境相依套件（package.json 中的 `dependencies` 屬性）。

※從 NPM 5 開始，`npm install [package]` 預設即安裝為正式環境相依套件，因此可省略 `--save` 參數

`devDependencies` 與 `dependencies` 的差別在於：
* `devDependencies`：僅開發時使用
* `dependencies`：開發時期及正式產品皆須要使用

只要有設定好相依套件屬性的 package.json，即可透過 `npm install` 再次安裝開發及正式環境依賴的所有套件。

※從 NPM 5 開始，會自動建立 package-lock.json 檔案來記錄套件的相依資訊，並且會優先以此紀錄的套件資訊來安裝，避免每一次 `npm install` 時都要重新分析每一個套件的依賴關係，也降低了因版本範圍而安裝到次版號或修訂號之更新版本套件導致不可預期錯誤的機率。因此也要避免手動修改 package.json 中的專案依賴套件，應使用指令操作來管理專案套件，由 NPM 維護專案依賴套件資訊

## 建立設定檔案
現在專案目錄中多了 package.json 設定檔，以及放置安裝套件的 node_modules 目錄。接下來要在專案目錄下建立：webpack.config.js、.babelrc、.gitignore 設定檔。

※從 NPM 5 開始，會將 node_modules 目錄中所有套件的相關資訊記錄在 package-lock.json。未來若要更新套件至跨主版號（major version）之版本必須使用 `npm install [package@version]` 來指定新版本，非主版號之更新則可以使用 `npm update [package]`。NPM 會自動維護 package-lock.json 檔案

```
project
├── src
├── node_modules
├── .babelrc
├── .gitignore
├── package-lock.json
├── package.json
└── webpack.config.js
```

### webpack.config.js
Webpack 在 version 2 之後的設定檔與舊版不同，要特別注意！

```javascript
// 載入 Node.js 的 path 模組
const path = require('path')
// 載入 webpack 模組
const webpack = require('webpack')
// 載入 HtmlWebpackPlugin 插件
const HtmlWebpackPlugin = require('html-webpack-plugin')

// Webpack 設定值
// 定義開發與正式共用的設定值
const webpackConfig = {
  // 專案根目錄路徑（本機路徑，須為絕對路徑）
  // 預設值為 webpack 指令作用的工作目錄（current working directory, CWD）
  // __dirname 為此 Webpack 設定檔模組的所在目錄
  context: path.join(__dirname, 'src'),
  // Entry（進入點）檔案路徑（基於 context）
  // 專案應用程式會由 Entry 啟動，並引入依賴模組
  entry: {
    index: './js/index.js'
  },
  // 輸出設定
  output: {
    // 輸出檔的目標位置（本機路徑，須為絕對路徑）
    path: path.join(__dirname, 'dist'),
    // 輸出檔名（可包含路徑）
    filename: 'js/bundle.js',
    // 輸出檔於伺服器公開位置中的絕對路徑
    // 開啟 Hot-Reload 時，此選項為必要設定
    // 因 Hot Module Replacement 須由此位置檢查更新的檔案
    publicPath: '/'
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
        // 作用的順序是由陣列中最末項的 Loader 開始，再依序往前
        // 將轉換過的結果交由前一項索引的 Loader 繼續處理
        // Loader 最後會將資源輸出為字串，Webpack 再包裝成 JavaScript 模組
        use: [
          {
            // Loader 名稱在 Webpack 2 之後不可省略 '-loader' 後綴
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
    }),
    // 動態產生 HTML 並自動引入輸出後的 Entry 檔案
    new HtmlWebpackPlugin({
      // 依據的模板檔案路徑（基於 context）
      template: './index.html',
      // 要引入的 Entry 名稱
      chunks: ['index']
    })
  ]
}

if (process.env.NODE_ENV === 'production') {
  // 若要編譯成正式產品，使用以下設定值：
  // 加入插件
  webpackConfig.plugins.push(
    // 最小化 JavaScript 檔案
    new webpack.optimize.UglifyJsPlugin(),
    // 透過計算引入模組及 chunk（程式碼塊，被 Webpack 重新組合而成的一段一段程式碼）的次數
    // 進而減少整體輸出檔案的大小
    // 自 Webpack 2 開始，預設為開啟
    // new webpack.optimize.OccurrenceOrderPlugin()
  )
} else {
  // 開發階段執行，則使用以下設定值：
  // 產生原始碼映射表（Source Map），方便開發時除錯
  webpackConfig.devtool = 'cheap-module-eval-source-map'
  // Webpack Dev Server（WDS）設定
  webpackConfig.devServer = {
    // 伺服器根目錄位置（本機路徑，建議使用絕對路徑）
    contentBase: path.join(__dirname, 'dist'),
    // 開啟 inline mode（檔案有更新時自動重整頁面）
    // 使用 Hot Module Replacement 時建議開啟此模式
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
    publicPath: '/',
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

// 將全部設定輸出為 Node.js 模組，供 Webpack 使用
module.exports = webpackConfig

```

### .babelrc
Babel 設定檔加上 ES6 轉譯規則：

```json
{
  "presets": [
    [
      "es2015",
      {
        // 不將 ES2015 模組轉譯成 CommonJS 模組
        // （Webpack 2 開始已支援 ES6 模組）
        "modules": false
      }
    ]
  ]
}
```

### package.json
#### 跨平台 NODE_ENV 環境變數
為了讓 Windows、macOS、Linux 都可以正確設定 Node.js 的 NODE_ENV 環境變數，我們使用 [cross-env](https://www.npmjs.com/package/cross-env) 來統一解決。

將 cross-env 安裝為專案開發環境相依套件：

```bash
$ npm install cross-env --save-dev
```

#### npm-scripts
設定 package.json 中的 `scripts` 屬性（若未有此屬性可自行新增）值為：

```json
{
  "dev": "webpack-dev-server --progress",
  "build": "cross-env NODE_ENV=production webpack --progress"
}
```

方便我們使用 `npm run dev`、`npm run build` 來執行自訂的指令：
* `npm run dev` 啟動 Webpack Dev Server
* `npm run build` 使用 Webpack 進行編譯打包

透過 `npm run` 實際呼叫的是安裝在專案中的 Webpack、Webpack Dev Server，而不是全域中的套件。

### .gitignore
專案相依套件的安裝目錄（node_modules）以及開發時所產生的檔案，能在 .gitignore 中設定讓 Git 排除，可以直接至 [gitignore.io](https://www.gitignore.io/) 取得製作好的[設定檔](https://www.gitignore.io/api/node)。

## 完整專案結構
完成所有設定之後，便可以將 HTML、CSS 置於 src 中，並在 src/js 開始撰寫 ES6 程式。

```
project
├── (dist)
│    ├── css
│    ├── js
│    │    └── bundle.js
│    └── index.html
├── src
│    ├── css
│    ├── js
│    │    └── index.js
│    └── index.html
├── node_modules
├── .babelrc
├── .gitignore
├── package-lock.json
├── package.json
└── webpack.config.js
```

※dist 為輸出目錄，其中的 bundle.js 為轉譯後產出的檔案

### 開啟 Hot-Reload 支援
除了 webpack.config.js 中的設定，index.js 也必須加上 HMR 的 API 呼叫：

```javascript
if (module.hot) {
  module.hot.accept('./es6module', () => {
    // 設定當指定的模組（範例為 es6module.js）在 HMR 完成後要執行的事情
    // 通常為重新讀取這個模組並更新網頁畫面
  })
}
```

讓 HMR 偵測 index.js 所相依的模組有更新檔案內容時，可以依我們的設定進行處理。若是 HMR 未被啟用，則此段程式便不會執行。

## 完整範例專案
本篇完整的範例專案可以在 GitHub 上看到：[simple-es6-webpack-project](https://github.com/jackblackevo/simple-es6-webpack-project)

## 額外參考
* [Webpack - Concepts](https://webpack.js.org/concepts/)
* [Webpack - Configuration](https://webpack.js.org/configuration/)
* [Webpack - Migrating from v1 to v2](https://webpack.js.org/guides/migrating/)
* [Webpack - DefinePlugin](https://webpack.js.org/plugins/define-plugin/)
* [Webpack - HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/)
* [Webpack - Devtool](https://webpack.js.org/configuration/devtool/)
* [Webpack - DevServer](https://webpack.js.org/configuration/dev-server/)
* [Webpack - Concepts: Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/)
* [Webpack  - Guides: Hot Module Replacement](https://webpack.js.org/guides/hot-module-replacement/)
* [Webpack - hot module replacement with webpack](https://github.com/webpack/docs/wiki/hot-module-replacement-with-webpack)
* [Node.js Documentation - path.join([...paths])](https://nodejs.org/api/path.html#path_path_join_paths)
* [Node.js Documentation - __dirname](https://nodejs.org/docs/latest/api/globals.html#globals_dirname)
* [eddychang.me - Source Map(原始碼映射表)](http://eddychang.me/blog/javascript/76-source-map.html)
* [The npm Blog - v5.0.0](http://blog.npmjs.org/post/161081169345/v500)
* [老雷的实验室 - 说说 npm 5 的新坑](https://mp.weixin.qq.com/s?__biz=MjM5MDcyMzIwNQ==&mid=2447503211&idx=1&sn=ace8556f50d9e024ac961a35a81a6ed7&chksm=b25532818522bb9763fd079aec1aec45e5797174dde6f4a082eed21aed34504be3f69138600d#rd)
* [NPM - npm-package-locks](https://docs.npmjs.com/files/package-locks)
* [NPM vs. Yarn：指令對照](https://jackblackevo.github.io/npm-vs-yarn-cli-commands-comparison/)