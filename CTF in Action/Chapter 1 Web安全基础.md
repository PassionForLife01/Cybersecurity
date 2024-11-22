# Chapter 1 Web安全基础
## 伪造 IP
`X-Forwarded-For` `X-Client-Ip` `X-Real-Ip` 
## Dirsearch
枚举目录。
```sh
dirsearch -u <URL> -e <extensions> -w <path/to/dict>
```
## 1.5 信息泄露
### GitHacker
临时添加到 PATH 中。
```sh
export PATH="$HOME/.local/bin:$PATH"
```
尝试下载源码。
```sh
githacker --url <.../.git> --output-folder <folder>
```
### svnExploit
检验是否 SVN 源码泄露。
```sh
SvnExploit -u <URL/.svn>
```
下载下来。
```sh
SvnExploit -u <URL/.svn> --dump
```
### 打包放根目录
```
www.zip/tar.gz/rar
bak.zip/tar.gz/rar
web.zip/tar.gz/rar
index.php.bak
```
### vi 恢复
vi 编辑文件时，会生成一个交换文件。
如果文件是 `index.php` 那么交换文件有可能是 `.index.php.swp`、`.index.php.swo`、`.index.php.swn`

vi 恢复文件
```sh
vi -r .index.php.swp
```
### DS_Store 文件泄露
使用 maxOS 编写网站，打包上传到服务器或者发送给别人时，当前目录下会多出一个 `.DS_Store` 文件。存储着当前目录文件下的摆放位置。
利用：
```sh
ds_store_exp https://url/.DS_Store
```
