## 1. 大致流程
- ping
- 扫端口
	- ftp
		- Anonymous
	- http
		- 哪种服务器
		- 扫目录
			- 常规扫
			- `-x .zip` 可能泄露源码
		- `robots.txt`
		- CMS

### 1.1 目录扫描
`dirsearch`
`gobuster`
### 1.2 端口扫描:
`nmap` 
`rustscan`
### 1.3 漏洞利用
`exploit database`
`searchspoilt`
RCE > Injection > 信息泄露
显示正在配置中的网站可能有大漏洞

## 2. 默认配置
### 2.1 Linux目录
flag 经常在 `/var` `/www` `usr/share` `/home`里
`/etc/passwd` 一些用户信息，可以看某些***突兀的用户名***
`/etc/shadow`  存放用户密码
### 2.2 CMS (Content Management System)
#### 2.2.1 `Wordpress`
`wp-config.php` file is one of the core WordPress files which contains the information about database, name, host (typically localhost), username, and password.

利用 plugin 来 get shell
[https://github.com/wetw0rk/malicious-wordpress-plugin ](https://github.com/wetw0rk/malicious-wordpress-plugin)
### 2.3 服务器
Apache (Debian/Ubuntu) 日志默认路径: `/var/log/apache2/access.log`

### 2.4 FTP
FTP 配置文件 `/etc/vsftpd.conf`
FTP 默认路径 `/var/ftp/pub/`


常看 `robots.txt`

传工具传到 `/tmp` 目录下

认真找回显点


