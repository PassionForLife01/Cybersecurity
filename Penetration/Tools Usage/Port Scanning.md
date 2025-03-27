# Port Scanning

## Nmap
经典。扫描端口
```sh
nmap -p- -A -sC -sV 192.168.1.1 -VV -T5 //nmap常规扫描

sudo nmap -sU -sV -vv -oA quick_udp 192.168.200.149 //UDP扫描

sudo nmap -sS -sV -A -n 192.168.246.209

nmap -p 22 192.168.246.209 // 单扫端口
```

Host discovery
```sh
sudo nmap -sn 192.168.1.1/24
```

Connect Scan (TCP)
```sh
sudo nmap -sT <ip> -p- -A -Pn -v
```

SYN scan (TCP)
```sh
sudo nmap -sS <ip> -p- -A -Pn -v
```

UDP scan (UDP)
```sh
sudo nmap -sU <ip> -p- -A -Pn -v
```

Timing
```sh
sudo nmap ... -T0 # paranoid
sudo nmap ... -T1 # sneaky
sudo nmap ... -T2 # polite
sudo nmap ... -T3 # normal
```

Output
```sh
sudo nmap ... -oN scan
sudo nmap ... -oX scan
sudo nmap ... -oG scan
sudo nmap ... -oA scan
```

## Rustscan
扫描端口。***可能会扫漏，可以过几分钟再扫一次***
```sh
rustscan -a 192.168.152.72 --range 1-65535

rustscan -a 192.168.216.223 --range 1-65535 --ulimit 5000 -- -A
```