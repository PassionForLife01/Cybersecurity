
下载
```
sudo apt install proxychains
```

配置文件
```
/etc/proxychains.conf
```

配合 clash 使用，则改为：
```
socks5  127.0.0.1 7890
```

使用：
```sh
proxychains command
```