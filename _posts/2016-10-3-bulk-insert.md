---
layout: post
title: BULK INSET
---

在寫 T-SQL 的 [BULK INSET](https://msdn.microsoft.com/zh-tw/library/ms188365.aspx) 時，很難理解 `MAXERRORS` 參數是針對全部的錯誤次數還是每一次 commit 的錯誤次數？

不知道那就自己動手寫[拋棄式玩具](http://online.ithome.com.tw/itadm/article.php?c=79755&s=2)來測試看看吧！

{% highlight sql %}
-- 以 DECLARE 來宣告變數（也可以同時賦值） --
DECLARE
@tableName as varchar(150) = 'dbo.TEST_TABLE',
@importFileName as varchar(150) = 'C:\import_data_file.txt',
@errorFileName as varchar(150) = 'C:\error_log_file_',
@todayDate as varchar(40),
@todayHour as varchar(40),
@todayMinu as varchar(40),
@todaySecd as varchar(40),
@sqlStmt as varchar(500);

-- SELECT 設定多個變數值 --
SELECT
@todayDate = CONVERT(varchar(10), GETDATE(), 112),
@todayHour = DATEPART(hh, GETDATE()),
@todayMinu = DATEPART(mi, GETDATE()),
@todaySecd = DATEPART(second, GETDATE());

-- ERRORFILE 參數的所指定的檔名若已存在，會產生錯誤 --
-- 為了方便我們多次執行來測試，可以以日期、時間來讓每次執行的檔名都不一樣 --

-- SET 設定一個變數值 --
SET
@errorFileName += 
@todayDate + 
'_' + 
@todayHour + 
@todayMinu + 
'_' +
@todaySecd + 
'.txt';

SET
@sqlStmt = 'BULK INSERT ' + @tableName +
' FROM ''' + @importFileName + ''' WITH (
BATCHSIZE = 1000,
CODEPAGE = ''950'',
FIELDTERMINATOR = ''|'',
MAXERRORS = 10,
ROWTERMINATOR = ''\n'',
TABLOCK,
ERRORFILE = ' + '''' + @errorFileName + '''' + ')';

-- 執行 BULK INERT --
EXECUTE (@sqlStmt);
{% endhighlight %}

我們可以發現：

| `BATCHESIZE` |幾筆資料 commit 一次|
| `MAXERRORS` |每一次 commit 可以接受的錯誤資料筆數上限|

假如有 100000 筆資料，

{% highlight sql %}
-- 每 1000 筆 commit 一次 --
BATCHESIZE = 1000,
-- 若其中一次 commit 錯超過 10 筆，則全部 insert（100000 筆）都會 rollback --
MAXERRORS = 10,
{% endhighlight %}

所以全部最多可以允許 (MAXERRORS = 10) * [100000 筆資料 / (BATCHESIZE = 1000)] 筆資料錯誤。

<br />

額外參考：

* [老蕭的網路筆記 - SQL Server 的 SELECT 與 SET](http://hccrichard.blogspot.tw/2010/12/sql-server-select-set.html)
* [MSDN - CAST 和 CONVERT](https://msdn.microsoft.com/zh-tw/library/ms187928.aspx)