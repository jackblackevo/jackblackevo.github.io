---
layout: post
title: 環境變數
---

很多學習 Java 書籍的第一章常是介紹如何設定環境變數，但這也是許多人無法理解的步驟，到底「環境變數」是什麼呢？

## Environment Variables

環境變數是於系統環境中，可以被呼叫讀取的一種變數，通常記錄了一些系統環境的設定值。

macOS、Linux 可以使用以下指令來查看目前的所有環境變數及其值：

```bash
$ env
```

Windows 可以使用：

```bash
> set
```

### `PATH` 變數

不管是 macOS、Linux 還是 Windows，系統文字介面中的每個指令都是獨立的執行檔。當使用者送出指令時，作業系統便會呼叫該指令的執行檔來執行使用者輸入的工作。而作業系統尋找指令執行檔的路徑，便是依據其中一個環境變數：`PATH`。

如果沒有把指令執行檔的路徑設定在 `PATH` 變數中的話，則執行該指令就必須有明確的位置：

* 於執行檔所在的目錄底下執行指令
* 輸入指令執行檔的完整路徑

macOS、Linux 可以使用以下指令來查看目前的 `PATH` 變數值：

```bash
$ echo $PATH
```

Windows 可以使用：

```bash
> echo %path%
```

_小提示：macOS、Linux 對大小寫敏感，Windows 則是大小寫不敏感（不論大小寫皆視為相同）。_

### Scope

環境變數可再細分為系統環境變數及使用者環境變數：

* __使用者環境變數__：目前登入使用者的獨立設定
* __系統環境變數__：為整體系統的設定（所有的使用者都會套用這個設定）

系統環境變數的設定，會套用至每個使用者，而使用者環境變數則只影響個別使用者。

* macOS、Linux 的使用者環境變數優先度大於系統變數（先載入系統 bash 設定檔，後載入使用者 bash 設定檔）
* Windows 的系統環境變數優先度大於使用者環境變數（以「系統環境變數 + 使用者環境變數」型式讀入）

## 額外參考
* [良葛格學習筆記 - PATH 是什麼？](http://openhome.cc/Gossip/JavaEssence/WhatPath.html)
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH - Bash shell 的操作環境](http://linux.vbird.org/linux_basic/0320bash.php#settings)