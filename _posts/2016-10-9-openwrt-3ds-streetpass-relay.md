---
layout: post
title: 利用 OpenWrt 建立 Nintendo 3DS 擦身中繼站
---

任天堂 3DS 可以利用 AP 來當作擦身中繼站，但是同一個 AP 的 MAC Address 限制每次擦身都要相隔八小時，那我們不斷的換 MAC Address 總可以吧 XD

1\. 使用 ssh 連上 AP：
{% highlight shell %}
$ ssh root@192.168.1.1
{% endhighlight %}

2\. 以 root 登入 OpenWrt 之後，於 `/usr/bin` 建立 Shell Script 檔案：
{% highlight shell %}
$ vim /usr/bin/homepass
{% endhighlight %}

這邊參考〈[OpenWrt实现3DS的StreetPass Relay擦肩HomePass](http://demon.tw/hardware/openwrt-3ds-streetpass-relay.html)〉的範例：
{% highlight shell %}
#!/usr/bin/lua

require("uci")

c = "wireless"
s = "attwifi"
m = "XX:XX:XX:XX:XX:XX" --Your 3DS MAC Address

-- HomePass for OpenWrt
-- By Demon
-- http://demon.tw

x = uci.cursor()
if not x:get(c, "3ds") then
    x:set(c, "3ds", "wifi-iface")
    x:set(c, "3ds", "device", "radio0")
    x:set(c, "3ds", "network", "lan")
    x:set(c, "3ds", "mode", "ap")
    x:set(c, "3ds", "ssid", s)
    x:set(c, "3ds", "encryption", "none")
    x:set(c, "3ds", "macfilter", "allow")
    x:set(c, "3ds", "maclist", {m})
    x:set(c, "3ds", "profile", "0")
end

a = {}
for i = 0, 255 do
    table.insert(a, string.format("4E:53:50:4F:4F:%02X", i))
end

i = x:get(c, "3ds", "profile")
i = i and tonumber(i) or 1
i = i + 1
i = (i > #a) and 1 or i
print("Setting profile " .. tonumber(i) .. 
". Using ssid " .. s .. " for mac " .. a[i])
x:set(c, "3ds", "profile", tostring(i))
x:set(c, "3ds", "macaddr", a[i])

x:commit(c)
os.execute("wifi reload radio0")
{% endhighlight %}

記得要把自己的 3DS MAC Address 填入，限制只讓 3DS 連線。

SSID 已改用「attwifi」（實際測試過「NZ@McD1」、「NZ@McD」皆無法正常擦身）。

3\. 儲存後，必須修改權限讓檔案可被讀取及執行：
{% highlight shell %}
$ chmod 755 /usr/bin/homepass
{% endhighlight %}

4\. 再將腳本加入排程中定時執行：
{% highlight shell %}
$ crontab -e
{% endhighlight %}

加入每隔兩分鐘就執行一次的設定：
{% highlight shell %}
*/2 *  *   *   *  /usr/bin/homepass | logger -t homepass
{% endhighlight %}

5\. 最後啟用排程：
{% highlight shell %}
$ /etc/init.d/cron start
$ /etc/init.d/cron enable
{% endhighlight %}

現在闔蓋待機的 3DS 就會自動透過擦身中繼 AP 和全世界的玩家擦身囉～

<br />

額外參考：

* [利用 crontab 來做 Linux 固定排程](http://code.kpman.cc/2015/02/11/%E5%88%A9%E7%94%A8-crontab-%E4%BE%86%E5%81%9A-Linux-%E5%9B%BA%E5%AE%9A%E6%8E%92%E7%A8%8B/)