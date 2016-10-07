---
layout: post
title: Maven 基礎概念
---

[Apache Maven](http://maven.apache.org/) 是 Java 專案常使用的自動化編譯工具，它可以幫助我們簡化開發過程中要不斷重覆的編譯、測試等等流程。

在 macOS 下使用 Homebrew 安裝 Maven 只需要：
{% highlight shell %}
$ brew install maven
{% endhighlight %}

連環境變數都不用設定，因為 Homebrew 已經自動在 `/usr/local/bin` 下建立 symbolic link 指向安裝路徑了。

## Maven 運作概念要能夠正確設定 POM，最好先瞭解 Maven 如何運作。Maven 的基本架構為三個階層：
* __Build Lifecycle__：代表的是想要執行的管理種類（編譯、清理、還是產生站台文件）	* __Phase__：負責「執行順序」上的依賴，以及簡化設定的使命		* __Goal__：真正執行功能的小螺絲釘（隸屬於 Plugin）也就是像：* Build LifecycleA	* Phase1→PluginA:GoalA or PluginB:GoalA	* Phase2→PluginA:GoalB	* Phase3→PluginC:GoalA and PluginE:GoalA	* Phase4→PluginC:GoalB* Build LifecycleB	* Phase1→PluginD:GoalA	* Phase2→PluginD:GoalB### Build Lifecycle
Maven 有三個「獨立」建構（build）的生命週期：
* __Clean Lifecycle__：在真正的建構前，進行清理。* __Default Lifecycle__：建構的核心行為，包括編譯、測試、打包、部署等。* __Site Lifecycle__：生成建構的報表。### Phase
Maven 的每個生命週期都是由不同的階段（Phase）所組成。執行某一階段的動作時，在這個階段前的所有階段都會被執行。例如：
{% highlight shell %}
$ mvn clean
{% endhighlight %}

就等同於：
{% highlight shell %}
$ mvn pre-clean clean
{% endhighlight %}

每一個 Phase 都會對應（binding）一或多個 Goals（Plugins 的功能），Maven 會依據 POM 中的 packaging 元素來決定 Phase 要使用哪一個 Goal。

### Plugins & Goals#### PluginsMaven 核心做的事很少，主要為解析 XML、追蹤生命週期、控制 Plugins，大部分的工作實際上都是由 Plugins 的 Goals 負責執行。內建的 Plugins 包括 clean、compile、package⋯⋯等等。

Plugins 大致上分為兩類：
* 建構類（build）* 報表類（reporting）
即 POM 中 build、reporting 的部分。在使用 Maven 時，最主要的步驟之一就是在 POM 中設定要用到的 Plugins 的設定值，這樣就可以為各預設的 Phase 加上額外的 Goals。Maven 在執行的順序會是：1. Phase 內建的 Goals（若在 POM 中有設定，會取代預設的設定值）2. 自行加入的 Goals（自行加入的順序則依在 POM 中的順序）Plugins 通常會有一個 help 的 Goal，可以查看該 Plugin 的說明。如查看 complier 的說明，可使用以下指令：
{% highlight shell %}$ mvn compiler:help{% endhighlight %}

#### Goals每個 Plugin 都會提供一或多個 Goals（功能），而每一個 Goal 各自代表一個獨立的任務，是 Maven 執行工作的最小單位。例如：
`compiler:compile`即表示 compiler 這個 Plugin 下的 compile 功能（Goal）。