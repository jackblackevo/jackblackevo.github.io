---
layout: post
title: JavaScript 事件流
---

JavaScript 是以事件驅動的程式語言，因此 Event Flow 就成為了 JS 必備重要的觀念。

## 事件處理模型

### DOM Level 0 Event
分為 Inline model 以及 Traditional model 兩種處理模型。

#### Inline model
以 HTML DOM 元素的屬性（attribute）來註冊事件處理函式，事件處理函式的 `this` 為 `window`。

#### Traditional model
使用 `HTMLElement.onevent` 型式的屬性（Property）註冊事件處理函式，事件處理函式的 `this` 為觸發事件之元素。

### DOM Level 2 Event
`EventTarget.addEventListener('event', callback)` 能註冊多個事件處理函式。

## 事件流
當使用者於 EventTarget 物件所在的元素上觸發事件時，瀏覽器會產生記錄事件資訊的事件物件。先自最上層的元素節點開始，將事件物件依序傳遞給至目標節點（觸發此事件的元素節點）過程中經過的每一個節點，再由目標節點向上依序傳遞給至最上層節點中間的所有節點。

1. 瀏覽器偵測到事件發生，產生記錄事件資訊的事件物件（會使用相關的事件介面來建立）。
2. 瀏覽器會從最上層的元素節點開始，將事件物件依序傳遞給至目標節點（觸發此事件的節點）過程中經過的每一個節點，此過程為捕捉階段（Capture Phase），瀏覽器會依序呼叫傳遞過程每個節點中註冊的捕捉階段事件監聽器。
3. 自上層傳遞到目標節點（觸發此事件的節點）時，為命中階段（Target Phase），瀏覽器會依目標節點中的註冊順序來呼叫事件監聽器。
4. 由目標節點向上依序傳遞給至最上層節點中間的所有節點，此過程為冒泡階段（Bubbling Phase），瀏覽器會依序呼叫傳遞過程每個節點中註冊的冒泡階段事件監聽器。

![W3C - Graphical representation of an event dispatched in a DOM tree using the DOM event flow](https://www.w3.org/TR/DOM-Level-3-Events/images/eventflow.svg)

## 事件物件與事件對象

### Event

#### currentTarget
總會指向現在正處理該事件的事件監聽器所註冊的 DOM 物件。

#### target
永遠指向觸發事件的 DOM 物件。

#### eventPhase
表示事件物件目前於事件流（Event Flow）中傳遞的進度為哪一個階段。

#### preventDefault()
如果事件可以被取消，就取消事件（即取消事件的預設行為）。但不會影響事件的傳遞，事件仍會繼續傳遞。

__特別注意：__
瀏覽器的事件預設行為是待事件流結束之後才會執行運作，所以子元素的事件在捕捉階段或冒泡階段時被父元素的事件監聽器將 `Event.preventDefault()` 的話，也會因此被取消事件的預設行為！

#### stopPropagation()
停止事件繼續傳遞。

### EventTarget

#### addEventListener()、removeEventListener()
{% highlight javascript %}
EventTarget.addEventListener(type, listener[, useCapture])
EventTarget.removeEventListener(type, listener[, useCapture])
{% endhighlight %}

* `type`：事件名稱
* `listener`：監聽器，也就是事件處理函式
* `useCapture`：布林值，預設為 `false`（註冊或移除冒泡階段的事件監聽器），`true` 則為註冊或移除捕捉階段的事件監聽器。此參數對目標節點（事件對象）本身無效，捕捉階段的事件監聽器在目標節點中被視為命中階段的事件監聽器，即僅依註冊順序來呼叫（監聽器執行的順序按照註冊時的先後決定）

冒泡階段和捕捉階段的監聽器是各自獨立的。假如一個監聽器分別在同一個物件上註冊了冒泡階段與捕捉階段，若要移除，也必須分別移除。

#### dispatchEvent()
{% highlight javascript %}
EventTarget.dispatchEvent(event)
{% endhighlight %}

* `event`：要被觸發的事件物件

於 `EventTarget` 上觸發指定的事件。