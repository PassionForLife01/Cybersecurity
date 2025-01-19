# 开启 kali linux SSH 连接

下载
```sh
sudo apt update

sudo apt install openssh-server
```

开启
```sh
sudo systemctl start ssh

sudo systemtcl enable ssh
```

查看
```sh
sudo systemctl status ssh
```

Configure
```sh
/etc/ssh/sshd_config

sudo systemctl restart ssh
```

