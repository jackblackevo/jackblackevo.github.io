---
layout: post
title: 關鍵轉譯路徑
---
關鍵轉譯路徑（Critical Rendering Path）也被譯作「關鍵渲染路徑」，表示瀏覽器在渲染網頁時的關鍵步驟。

所謂「關鍵」指的是在所有會影響頁面渲染速度的因素中，影響比重較高的關鍵資源（critical resource）。我們可以從下面的範例中，觀察到瀏覽器從開始載入網頁到渲染完成後顯示於畫面的整個過程。

## 瀏覽器渲染流程
1. 瀏覽器發送請求（request）至伺服器要求取得網頁 HTML 檔案
2. 取得 HTML 檔案內容之後，瀏覽器便開始解析（parse）並建構（build）頁面的文件物件模型（DOM，Document Object Model）
   * Bytes -> Characters -> Tokens -> Nodes -> DOM
3. 在解析 HTML 的過程中，發現引用的外部資源時，便會發送請求來取得
   * 例如 HTML 標籤：`<link>`、`<script>`、`<img>` 等
   * 開始請求 JavaScript 檔案時，便會因為「禁止剖析器（parser blocking）」而立即停止建構 DOM
   * 但即使先請求 JS 資源，瀏覽器還是可以不受 parser blocking 的影響，緊接著發送請求 CSS。這是因為瀏覽器有 Preload Scanner 機制，可以事先找到需要請求的資源檔案
4. 取得 CSS 後，瀏覽器即會解析、建立 CSSOM（CSS Object Model）
   * Bytes -> Characters -> Tokens -> Nodes -> CSSOM
   * 若 CSSOM 尚未建構完畢，會等待 CSSOM 完成後才執行 JavaScript（若 `<script>` 標籤在 CSS 之後則不受此限制影響）
5. 取得 JavaScript 後，瀏覽器開始執行 JS 時會停止建構 DOM（同前面所述之 parser blocking）
6. 建立轉譯樹狀結構（Render Tree）
7. 瀏覽器依據 Render Tree 進行計算後再繪製出網頁畫面

### DOM
* 文件物件模型（DOM，Document Object Model）為 HTML 所有元素的階層結構樹狀模型
* DOM 不會馬上建立完畢，而是逐步建立
* 在建立 DOM 時，遇到需要的資源（JavaScript、CSS、圖片等）便同時發出請求取得相關資源

{% highlight html %}
<!DOCTYPE html>
<html lang="zh-TW">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, user-scalable=0">
    <title>Hello World</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <p>Hello World!</p>
    <div>
      <span>Image version:</span>
      <img src="image.jpg">
    </div>
    <script src="script.js"></script>
  </body>
</html>
{% endhighlight %}

![Document Object Model](/assets/images/dom.png)

### CSSOM
* 瀏覽器會解析所有 CSS 樣式規則，並以其選擇器階層去建立樣式規則的樹狀結構（即用每一條 CSS 樣式規則設定的選擇器，建立所有樣式規則的繼承疊層）
* CSSOM 中只有 CSS 樣式規則，並不包含 HTML 元素
* 在 CSSOM 建立前，JavaScript 是無法執行的（除非外部 JS 以 `async` 方式載入，或 JS 較 CSS 先行引入）

{% highlight css %}
p {
  color: red;
}

div {
  color: blue;
}

div span {
  font-size: 16px;
}

img {
  float: right;
}
{% endhighlight %}

![CSS Object Model](/assets/images/cssom.png)

### Render Tree
* 轉譯樹狀結構（Render Tree）為一個由在頁面布局（layout）中實際佔位的 DOM 元素關連到 CSSOM 的樹狀結構模型
* HTML 和 CSS 都是會影響 Render Tree 的資源，在 DOM 及 CSSOM 建構完成之前，皆會因「禁止轉譯（render blocking）」而禁止瀏覽器建構 Render Tree
* 瀏覽器在關連 DOM 與 CSSOM 時，是以 CSS 選擇器（CSS Selector）「右到左」的方向來提高效率。如 `div div p em`，直接找到 `em` 去檢查其上層是否為 `p`、`div`、`div`）
* 每個會顯示出來的 DOM 元素皆以一個像方塊盒子般的方式呈現，而每一個盒子就是一個表示位置、大小、顏色等等顯示資訊的物件模型（此模型 WebKit 稱為：Renderer、Gecko 則叫：Frame）
* 實際要繪製（painting 或稱為 rasterizing）網頁畫面之前，瀏覽器會依照 Render Tree 中物件模型的顯示資訊計算實際座標幾何資訊、像素大小等（此動作 WebKit 稱為：Layout、Gecko 則叫：Reflow）

![Render Tree](/assets/images/render_tree.png)

## 額外參考
* [Google Developers Web Fundamentals - 關鍵轉譯路徑](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/?hl=zh-tw)
* [30 天 Progressive Web App 學習筆記 - Critical Render Path](http://ithelp.ithome.com.tw/articles/10187285)