# Directory Scanning

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