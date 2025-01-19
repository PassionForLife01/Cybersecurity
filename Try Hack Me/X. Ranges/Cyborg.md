# Cyborg

```
10.10.44.59
```

```
22

80
- Apache
```

```
/admin/index.html
/admin/admin.html
/etc/squid
```

## 破解哈希
在 `/etc/squid/passwd`
```
music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.
```

```sh
hash-indentifier

MD5(APR)
```

```sh
hashcat --help | grep apr # get the mode code

hashcat -m 1600 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

squidward
```

## 分析压缩包

```
http://10.10.44.59/admin/archive.tar
```

```
tar -xvf archive.tar
```