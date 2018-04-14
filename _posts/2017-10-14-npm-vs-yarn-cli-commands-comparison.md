---
layout: post
title: NPM vs. Yarn：指令對照
---

[Yarn](https://yarnpkg.com) 是 Facebook 所發布的 Node.js 套件管理工具，相較於 [NPM](https://www.npmjs.com/) 有多項改進。最重要的是，Yarn 安裝套件的速度快上不少，也可以相容於 NPM。

## 常用指令對照

| NPM                                  | Yarn                              | 指令說明                                     |
| ------------------------------------ | --------------------------------- | ---------------------------------------- |
| `npm init`                           | `yarn init`                       | 建立 package.json 設定檔                      |
| `npm install`                        | `yarn install`                    | 依 package.json 安裝相依套件                    |
| `npm install [package]`              | （不支援）                             | 僅安裝但不加入為相依套件（NPM：v5 之後預設行為等同於加 `--save` 參數） |
| `npm install --save [package]`       | `yarn add [package]`              | 安裝並加入為正式環境相依套件 （package.json 中的 `dependencies` 屬性） |
| `npm install --save-dev [package]`   | `yarn add [package] [--dev/-D]`   | 安裝並加入為開發環境相依套件（package.json 中的 `devDependencies` 屬性） |
| `npm install --global [package]`     | `yarn global add [package]`       | 安裝套件至全域中（方便使用套件提供之指令）                    |
| `npm uninstall [package]`            | （不支援）                             | 僅移除但不一併移除該套件於 package.json 中的相依（NPM：v5 之後預設行為等同於加 `--save` 參數） |
| `npm uninstall --save [package]`     | `yarn remove [package]`           | 移除且一併移除該套件於 package.json 中的正式環境相依（`dependencies` 屬性） |
| `npm uninstall --save-dev [package]` | `yarn remove [package]`           | 移除且一併移除該套件於 package.json 中的開發環境相依（`devDependencies` 屬性） |
| `npm outdated`                       | `yarn outdated`                   | 檢查過期（已有新版）的套件                            |
| `npm update`                         | `yarn upgrade`                    | 更新所有專案相依套件                               |
| `npm update [package]`               | `yarn upgrade [package]`          | 更新指定的專案相依套件                              |
| `npm update --global`                | `yarn upgrade --global`           | 更新所有全域套件                                 |
| `npm update --global [package]`      | `yarn upgrade --global [package]` | 更新指定的全域套件                                |
| `npm run [script]`                   | `yarn run [script]`               | 執行專案自訂指令（package.json 中的 `scripts` 屬性）   |

## 額外參考
* [Yarn - Migrating from npm](https://yarnpkg.com/en/docs/migrating-from-npm)
* [Yarn - Docs](https://yarnpkg.com/en/docs)
* [NPM - documentation](https://docs.npmjs.com/)