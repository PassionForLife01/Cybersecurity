# 安装 pip
## 1. 使用别人的下载脚本
```sh
curl -O https://bootstrap.pypa.io/get-pip.py
```

Pip 会安装到与本机 python 解释器相同的路径下。
```sh
python get-pip.py
```
## 2. 包管理器
Debian/Ubuntu
```sh
sudo apt update
sudo apt install python3-pip
```