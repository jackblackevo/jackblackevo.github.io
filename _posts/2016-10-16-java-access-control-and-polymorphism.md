---
layout: post
title: Java 存取控制與多型
---

Java 的存取修飾字 private 限制了類別的成員僅可於同一個類別內訪問，但是 PTT 上的[這個問題](http://www.ptt.cc/bbs/java/M.1475931276.A.45D.html)怎麼會發生子類別實體卻可以呼叫父類別私有方法的情況呢？

來看看這個範例：
{% highlight java %}
class ChildClass extends FatherClass {
	public void mehtodA() {
		System.out.println("it's ChildClass");
	}
}

public class FatherClass {
	private void mehtodA() {
		System.out.println("it's FatherClass");
	}
	
	public static void main(String[] args) {
		FatherClass objDeclaredByFather = new ChildClass();
		objDeclaredByFather.mehtodA();
		
		ChildClass objDeclaredByChild = new ChildClass();
		objDeclaredByChild.mehtodA();
	}
}
{% endhighlight %}

輸出結果：
{% highlight text %}
it's FatherClass
it's ChildClass
{% endhighlight %}

這裡有兩個問題：

1. `objDeclaredByFather.mehtodA()` 和 `objDeclaredByChild.mehtodA()` 輪出的結果為什麼不一樣？
2. `objDeclaredByFather.mehtodA()` 為什麼能通過編譯並執行？

## 問題一
Java 操作物件時是以宣告型別為主，在建立物件實體時會先建立父類別們的實體（直到 `Object` 類別）。因此以 `FatherClass` 宣告的物件 `objDeclaredByFather`，雖然實際上為 `ChildClass` 的實體，但型別仍被視為 `FatherClass`。

判斷是否為同個方式是依據「方法簽名」，方法簽名就是 methodName 以及方法參數的型別、數量與順序，方法簽名相同就會被視為同個方法。

操作物件時會以宣告的型別來尋找定義方法，如果子類別物件實體有「相同」的方法（Override 父類別），那就執行子類別版本，如果子類別物件實體中沒有相同的方法就往上層（父類別們的實體）找。但對 `ChildClass` 型別來說，`FatherClass` 的 `mehtodA` 是不可見的（無法存取）。所以在 `ChildClass` 定義的 `mehtodA` 雖然方法簽名和 `FatherClass` 的 `mehtodA` 一樣，仍然被視為不同的方法。

在這個例子中，`objDeclaredByFather` 呼叫的是 `FatherClass` 所定義的 `mehtodA`，`objDeclaredByChild` 呼叫的則是 `ChildClass` 定義的 `mehtodA`。因為 `FatherClass` 定義的 `mehtodA` 和 `ChildClass` 定義的 `mehtodA` 是不同的方法。

更簡單的判斷方式：直接使用 `@Override` 來提示 XD

## 問題二
存取修飾字的限定對象是「型別」，`private` 在同個型別內都是可以叫用的，不論是由同類別的其它方法呼叫：
{% highlight java %}
privateMethod();
// 等同於
this.privateMethod();
// 或是
privateStaticMethod();
// 等同於
ClassName.privateStaticMethod();
{% endhighlight %}

還是由此類別的物件實體呼叫：
{% highlight java %}
obj.privateMethod();
// 或是
obj.privateStaticMethod();
{% endhighlight %}

而程式進入點（`main` 方法）被定義在 `FatherClass` 類別中，`objDeclaredByFather` 又是以 `FatherClass` 宣告的 `ChildClass` 物件實體。所以滿足了存取限制的條件，因此 `objDeclaredByFather.mehtodA()` 可以正確編譯並執行。若是把 `main` 方法定義在 `ChildClass`，就會因為存取限制而無法呼叫了。

<br />

我在 PTT 的回答：

* [Re: [問題] 關於向上轉型](https://www.ptt.cc/bbs/java/M.1475950066.A.A65.html)
