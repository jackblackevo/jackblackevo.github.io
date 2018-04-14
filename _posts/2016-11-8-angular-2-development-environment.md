---
layout: post
title: Angular 2 開發環境
---

初學 [Angular 2](https://angular.io/) 的第一件事就是建構開發環境了，記錄一些自己踩到的雷吧 😚

## Node.js 衝突
如果有使用 Homebrew（或其它套件管理工具）以及 Node Version Manager（NVM），要特別注意是否在兩邊都裝了 Node.js。因為 Homebrew 在安裝套件時會一併安裝依賴的套件，有可能會和原本 NVM 所管理的 Node.js 衝突，導致在執行 `node`、`npm`、`yarn` 時搞混到底是哪一邊的 Node.js。移除 Homebrew 擅自安裝的 Node.js，保留 NVM 管理的版本就好。

## angular-cli 錯誤

### 錯誤訊息：
```
It seems like you're using a project generated using an old version of the Angular CLI.
The latest CLI now uses webpack and includes a lot of improvements, include a simpler
workflow, a faster build and smaller bundles.

To get more info, including a step-by-step guide to upgrade the CLI, follow this link:
https://github.com/angular/angular-cli/wiki/Upgrading-from-Beta.10-to-Beta.14
```

### 發生原因：
工作目錄被視為舊版 angular-cli 所產生的專案，可尋找該目錄或其父目錄中的 node_modules 資料夾，將此資料夾刪除即可。

-----------

### 錯誤訊息：
```
You cannot use the new command inside an angular-cli project.
```

### 發生原因：
同上一案例，可尋找 package.json 刪除之。