---
layout: post
title: Maven 基礎概念
---

[Apache Maven](http://maven.apache.org/) 是 Java 專案常使用的自動化編譯工具，它可以幫助我們簡化開發過程中要不斷重覆的編譯、測試等等流程。

目前 Maven 3.3 必需在 JDK 1.7 以上的環境下執行。Oracle JDK for macOS 會在安裝時於 `/usr/bin` 下建立多個 symbolic link 指向安裝路徑中的指令執行檔，所以我們不必設定 `JAVA_HOME` 環境變數。

在 macOS 下使用 Homebrew 安裝 Maven 只需要：
{% highlight shell %}
$ brew install maven
{% endhighlight %}

和 JDK 一樣，連 `M2_HOME` 環境變數都不用設定，因為 Homebrew 也已經自動在 `/usr/local/bin` 下建立 symbolic link 指向安裝路徑了（自動執行 `brew link` 指令）。

## 運作概念

Maven 有三個「獨立」建構（build）的生命週期：

Maven 的每個生命週期都是由不同的階段（Phase）所組成。執行某一階段的動作時，在這個階段前的所有階段都會被執行。例如：
{% highlight shell %}
$ mvn clean
{% endhighlight %}

就等同於：
{% highlight shell %}
$ mvn pre-clean clean
{% endhighlight %}

而每一個 Phase 都會對應（binding）一或多個 Goals（Plugins 的功能）。

除了預設的 Goals，Phase 額外對應的 Goals，Maven 會以 Plugin 的角度，依據 POM 中 `<plugin>` 元素的 `<executions>` 設定其 Goals 對應到哪些 Phase。

### Plugins & Goals


Plugins 大致上分為兩類：



{% highlight shell %}

#### Goals

{% highlight shell %}

## 聚合與繼承
這裡要注意的是：聚合與繼承是兩個獨立的概念，彼此互不衝突，也可以同時使用。

### 聚合
在父模組 POM 中，將子模組組織好，Maven 會在執行時尋找子模組：
{% highlight xml %}
<modules>
	<!-- 子模組的「路徑」 -->
	<module>childA</module>
	<module>childB</module>
</modules>
{% endhighlight %}

父模組 POM 的 `<package>` 值為「pom」。

當 Maven 執行時（於父模組目錄執行），指令實際是作用在子模組上（所有模組聚合、整合成一個完整的專案）。若單獨於某一子模組目錄執行 Maven 指令，則只會依該子模組的 POM 來運作，並不會一起執行其它的模組（因為 Maven 不知道其它模組的存在）。

要小心指令運作的順序並不是依 `<modules>` 的順序，若子模組間有彼此依賴的情形，Maven 會自動按照依賴的實際狀況來先後執行。

例如：

1. 在父模組執行 `mvn package`
2. Maven 依 `<modules>` 設定，利用 Reactor 分析子模組依賴關係
3. 先在被依賴的子模組上執行此指令（待接下來給依賴它的其它子模組使用）
4. 再照依賴關係順序逐一於其它子模組執行指令（依賴的子模組就不是從資源庫中尋找）

### 繼承
在子 POM 中，指向父模組 POM：
{% highlight xml %}
<parent>
	<groupId>idv.jackblackevo.mavenTest</groupId>
	<artifactId>ProjectName</artifactId>
	<version>1.0-SNAPSHOT</version>
	<!— 若父 POM 與子 POM 的目錄是父子關係時可省略 relativePath -->
	<relativePath>../pom.xml</relativePath>
</parent>
{% endhighlight %}

子 POM 中的設定通常會取代父 POM 的相同設定，但一些特殊的設定是與父 POM 合併而不是取代：

* `<dependencies>`
* `<developers>`
* `<contributors>`
* `<plugin>`
* `<resources>`

另外，所有的 POM 都隱式繼承了 Maven 的 Super POM。