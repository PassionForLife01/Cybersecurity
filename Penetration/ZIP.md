## 压缩
### 1. 使用 `tar` 进行打包并压缩

`tar` 是一个常用的命令，用于打包多个文件和目录。通常，`tar` 会与 `gzip` 或 `bzip2` 结合使用来压缩文件。
#### 压缩文件
- 使用 `tar` 和 `gzip`：
`tar -czvf archive_name.tar.gz /path/to/directory_or_file`
这里：
- `-c`：创建一个新的 tar 包。
- `-z`：使用 gzip 压缩。
- `-v`：显示详细信息。
- `-f`：指定压缩后的文件名。
#### 解压缩文件
- 解压 `.tar.gz` 文件：
`tar -xzvf archive_name.tar.gz`
- 解压 `.tar.bz2` 文件：
`tar -xjvf archive_name.tar.bz2`
这里：
- `-x`：解压 tar 包。
- `-j`：使用 bzip2 解压。
### 2. 使用 `gzip` 或 `bzip2` 单独压缩文件

- 使用 `gzip` 压缩单个文件：
`gzip filename`
这会生成一个 `.gz` 压缩文件，原始文件将被替换。
- 使用 `gzip` 解压文件：
`gzip -d filename.gz`
- 使用 `bzip2` 压缩文件：
`bzip2 filename`
这会生成一个 `.bz2` 文件，原始文件也会被替换。
- 使用 `bzip2` 解压文件：
`bzip2 -d filename.bz2`
### 3. 使用 `zip` 和 `unzip`
- 压缩文件或目录：
`zip -r archive_name.zip /path/to/directory_or_file`
这里的 `-r` 表示递归压缩目录及其子目录。
- 解压 `.zip` 文件：
`unzip archive_name.zip`
这些工具和命令可以满足大部分在 Linux 下的压缩和解压需求。
## 解压
1. **解压 `.tar` 文件：**
    `tar -xvf filename.tar`
2. **解压 `.tar.gz` 或 `.tgz` 文件：**
    `tar -xzvf filename.tar.gz`
3. **解压 `.tar.bz2` 文件：**
    `tar -xjvf filename.tar.bz2`
4. **解压 `.zip` 文件：**
    `unzip filename.zip`
5. **解压 `.rar` 文件：**
    `unrar x filename.rar`
6. **解压 `.7z` 文件：**
    `7z x filename.7z`
7. **解压 `rockyou.txt.gz`**
	`gunzip rockyou.txt.gz`
- `x` 选项表示解压。
- `v` 选项表示显示解压的文件名。
- `z` 选项表示处理 gzip 压缩。
- `j` 选项表示处理 bzip2 压缩。
- `f` 选项表示文件名。
## 爆破

```shell
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt spammer.zip
```

```shell
zip2john spammer.zip > hash.txt
john hash.txt
```



