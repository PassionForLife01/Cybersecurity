# G. Nmap Basics
## Intro

How can we learn that how many hosts are alive, or what services they are running ?

Focus on first question, we maybe use `ping` or `arp-scan` to check if a host is alive. But `ping` won't work if the target system's firewall blocks ICMP traffic. And `arp-scan` can only be used on the same network, which can be limited if we want to perform a remote hacking. Nonetheless the time we are wasting by typing them all.

And discovering the running services on a specific host is equally time-consuming if one relies on manual solutions or inefficient scripts. `telnet`.

Here comes the `nmap`, which is a powerful network scanner, used to discover live hosts, find open ports, and detect service versions.

## Host Discovery

`nmap` use various sophisticated ways to discover live hosts.

The ways `nmap` used to specify its targets:
- IP range using `-`. `192.168.0.1-10`
- IP subnet using `/`. `192.168.0.1/24`, is equivalent to `192.168.0.0-255`
- Hostname. `example.thm`

`nmap` offers `-sn` option, i.e. , ping scan, to discover online hosts on a network. 
But it's more powerful than `ping`.

When we are using `nmap`, we should note that we are either running `nmap` as `root` or `sudo`. Because without `root` privilege, we cannot take some fundamental types of scans such as ICMP echo and TCP connect scans. 

### Scanning Local Network

It will report IP address and MAC address of of live host by using ARP request and reply.

```sh
sudo nmap -sn 192.168.111.128/24
```

```
┌──(passionforlife㉿kali)-[~]
└─$ sudo nmap -sn 192.168.111.128/24
[sudo] password for passionforlife: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-20 15:39 HKT
Nmap scan report for 192.168.111.1
Host is up (0.00066s latency).
MAC Address: 00:50:56:C0:00:08 (VMware)
Nmap scan report for 192.168.111.2
Host is up (0.00032s latency).
MAC Address: 00:50:56:E9:B6:10 (VMware)
Nmap scan report for 192.168.111.254
Host is up (0.00011s latency).
MAC Address: 00:50:56:EF:77:72 (VMware)
Nmap scan report for 192.168.111.128
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.01 seconds
```

### Scanning Remote Network

In this context, "remote" means that at least one router separates our system from this network.
As a result, all our traffic to the target systems must go through one or more routers. We cannot send an ARP request to the target.

```sh
sudo nmap -sn 192.168.11.0/24
```

Since we are in different subnet, we can only use TCP, ICMP these things.
- ICMP echo requests.
- ICMP timestamp requests.
- TCP packets to port 443 with the SYN flag set.
- TCP packets to port 80 with the ACK flag set.

More technically, we can specify the ways we discover a remote network by
- `-PS[portlists]`. Detect TCP SYN by given port.
- `-PA[portlists]`. Detect TCP ACK by given port.
- `-PU[portlists]`. Detect UDP by given port.

But in most scenarios, it is not necessary to specify the ways, which is beyond the scope of this room.

`-sL` lists the targets to scan without actually scanning them. 

## Port Scanner

By network service, we mean any process that is listening for incoming connections on a TCP or UDP port. 
Web server usually listens on TCP port 80 or 443.
DNS server usually listens on UDP port 53 or TCP port 53.

### Scanning TCP Ports 
#### Connect Scan

`-sT` will let `nmap` to complete the TCP three-way handshake with every target TCP port.

The traffic below show how `nmap` complete the TCP connection.

![[Pasted image 20250120165601.png]]

Our IP address is `192.168.124.148`.

The part marked with 1 shows how a TCP three-way handshake would be done. And the connection was torn down by sending a TCP RST-ACK packet.

And the part marked with 2 shows what will happen when we attempt to connect to a closed port. We will get a TCP RST-ACK packet.  

#### SYN Scan (Stealth)

`-sS`

In this mode of scan, we just send a TCP SYN packet to the host. It is expected to lead to fewer logs as the connection is never established. But most modern firewall can still detect it with the proper configuration.

Details are shown below:

![[Pasted image 20250120170527.png]]

The part marked with 1 just send a TCP SYN, waiting for response if the service on this port is open. After receiving the TCP SYN-ACK packet, `nmap` will send a TCP RST packet to tear down the connection.

The part marked with 1 just attempt to connect to a closed port, sending TCP SYN, and receiving RST-ACK.

### Scanning UDP Ports

`-sU`

Many useful protocols are using UDP ports.
- DNS
- DHCP
- NTP (Network Time Protocol)
- SNMP (Simple Network Management Protocol)

Details are shown below:

![[Pasted image 20250120171802.png]]

If `nmap` send a UDP packet to a closed port, it will get the ICMP Destination unreachable packet (port unreachable). 

### Limiting the Target Ports

`nmap` scans the most common 1,000 ports by default. But we can configure it further more.

- `-F`. Fast mode, only scans the 100 most common ports
- `-p[range]`. allows you to specify a range of ports
	`-p10-1024` scans from port 10 to port 1024.
	`-p-25` scans from port 1 to port 25.
	`-p-` scans all the ports and is equivalent to `-p1-65535`.

| Option      | Explanation                                                   |
| ----------- | ------------------------------------------------------------- |
| `-sT`       | TCP connect scan – complete three-way handshake               |
| `-sS`       | TCP SYN – only first step of the three-way handshake          |
| `-sU`       | UDP scan                                                      |
| `-F`        | Fast mode – scans the 100 most common ports                   |
| `-p[range]` | Specifies a range of port numbers – `-p-` scans all the ports |

Templates:
SYN scan for all ports
```sh
sudo nmap <ip> -sS -p-
```

Connect scan
```sh
sudo nmap <ip> -sT -p-
```

UDP scan
```sh
sudo nmap <host> -sU -p-
```

## Version Detection

### OS Detection

`-O`

It will detect target's OS, relying on various indicators to make an educated guess about the target OS.

However, there is no perfectly accurate OS detector. 

![[Pasted image 20250120183239.png]]

### Service and Version Detection

`-sV`

It will show not only the service listening on a specific port, but also the version of it.

`-A`

It integrates OS detection, version scanning, and traceroute, among other things.

### Forcing the Scan

`-Pn`

When `nmap` is scanning a host by sending ICMP echo requests but without ICMP echo reply, it will mark the host as offline.

But sometimes the firewall will block ICMP packet, but the host still alive.

So we use `-Pn` mode to assume that host is alive, and scan the ports immediately, skipping the process of host discovering.

### Summary

| Option | Explanation                                          |
| ------ | ---------------------------------------------------- |
| `-O`   | OS detection                                         |
| `-sV`  | Service and version detection                        |
| `-A`   | OS detection, version detection, and other additions |
| `-Pn`  | Scan hosts that appear to be down                    |

Templates:

```sh
sudo nmap <host> -sS -p- -A -Pn
```

## Timing
### Templates
`nmap` provides various options to control the scan speed and timing.

Test with
```sh
sudo nmap <ip> -sS -F -T0
```

These are templates for you. 

| Timing          | Total Duration |
| --------------- | -------------- |
| T0 (paranoid)   | 9.8 hours      |
| T1 (sneaky)     | 27.53 minutes  |
| T2 (polite)     | 40.56 seconds  |
| T3 (normal)     | 0.15 seconds   |
| T4 (aggressive) | 0.13 seconds   |
| T5 (insane)     | No data        |

Below are testing with SYN scan mode.
`-T0`
5 minutes per port.

`-T1`
15 seconds per port.

`-T2`
0.4 seconds per port.

`-T3`
`nmap` appeared to be running as fast as it could. 
### Parallel Probes
If you want to customize your scan speed, here are some other options.

A second helpful option is the number of parallel service probes. 
`--min-parallelism <numprobes>`
`--max--parallelism <numprobes>`

The parallel probes mean that how many TCP or UDP ports will be scanned simultaneously.

By default, `nmap` will automatically control the number of parallel probes: if the network is performing poorly, i.e., dropping packets the probes might fall to one; if the network performs flawlessly, the number of parallel probes can reach several hundred.
### Packets per Second
A similar helpful option which can control the number of packets per second. This would be applied to the whole scan, not single host, if you give it a list of hosts.
`--min-rate <number>`
`--max-rate <number>`
### Host Timeout
The last option can specifies the maximum time you are willing to wait, which is suitable for slow hosts with slow network connections.
`--host-timeout <time>` 

The `<time>` is in millisecond by default.

```sh
sudo nmap <ip> --host-timeout 2ms
sudo nmap <ip> --host-timeout 2s
sudo nmap <ip> --host-timeout 2m
sudo nmap <ip> --host-timeout 2h
```

|Option|Explanation|
|---|---|
|`-T<0-5>`|Timing template – paranoid (0), sneaky (1), polite (2), normal (3), aggressive (4), and insane (5)|
|`--min-parallelism <numprobes>` and `--max-parallelism <numprobes>`|Minimum and maximum number of parallel probes|
|`--min-rate <number>` and `--max-rate <number>`|Minimum and maximum rate (packets/second)|
|`--host-timeout`|Maximum amount of time to wait for a target host|

## Output

### Verbosity and Debugging

`-v`

You can know how `nmap` works, best with the `wireshark`.
You can see how `nmap` is moving from one stage to another: ARP ping scan, parallel DNS resolution, and finally, SYN stealth scan for every live host.

Wanna more verbosity?
```
-v
-vv
-vvvv
-v2
-v4
```
You can even increase the verbosity level by pressing "v" after the scan already started.

more verbosity? You must consider the debug mode
```
-d
-d9 # max level
```
And you can also press "b" after the scan already started.

You should be ready for thousands of information and debugging lines.

### Saving Scan Report

The three most useful output format are normal output, XML output, and grepable. 

- `-oN <filename>` - Normal output
- `-oX <filename>` - XML output
- `-oG <filename>` - `grep`-able output (useful for `grep` and `awk`)
- `-oA <basename>` - Output in all major formats

## Conclusion and Summary

You should have the `root` or `sudo` privilege to scan, as the SYN scan needs `root` privilege and by default, and if not, it will perform connect scan as local user by default.

[Network Security](https://tryhackme.com/module/network-security) has four `nmap` rooms dedicated for `nmap`, if you have interests, it maybe introduce some advanced skills to use `nmap`.
But in this room, it just introduce the most common usage you should know.

| Option                                                              | Explanation                                                                                        |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `-sL`                                                               | List scan – list targets without scanning                                                          |
| **_Host Discovery_**                                                |                                                                                                    |
| `-sn`                                                               | Ping scan – host discovery only                                                                    |
| **_Port Scanning_**                                                 |                                                                                                    |
| `-sT`                                                               | TCP connect scan – complete three-way handshake                                                    |
| `-sS`                                                               | TCP SYN – only first step of the three-way handshake                                               |
| `-sU`                                                               | UDP Scan                                                                                           |
| `-F`                                                                | Fast mode – scans the 100 most common ports                                                        |
| `-p[range]`                                                         | Specifies a range of port numbers – `-p-` scans all the ports                                      |
| `-Pn`                                                               | Treat all hosts as online – scan hosts that appear to be down                                      |
| **_Service Detection_**                                             |                                                                                                    |
| `-O`                                                                | OS detection                                                                                       |
| `-sV`                                                               | Service version detection                                                                          |
| `-A`                                                                | OS detection, version detection, and other additions                                               |
| **_Timing_**                                                        |                                                                                                    |
| `-T<0-5>`                                                           | Timing template – paranoid (0), sneaky (1), polite (2), normal (3), aggressive (4), and insane (5) |
| `--min-parallelism <numprobes>` and `--max-parallelism <numprobes>` | Minimum and maximum number of parallel probes                                                      |
| `--min-rate <number>` and `--max-rate <number>`                     | Minimum and maximum rate (packets/second)                                                          |
| `--host-timeout`                                                    | Maximum amount of time to wait for a target host                                                   |
| **_Real-time output_**                                              |                                                                                                    |
| `-v`                                                                | Verbosity level – for example, `-vv` and `-v4`                                                     |
| `-d`                                                                | Debugging level – for example `-d` and `-d9`                                                       |
| **_Report_**                                                        |                                                                                                    |
| `-oN <filename>`                                                    | Normal output                                                                                      |
| `-oX <filename>`                                                    | XML output                                                                                         |
| `-oG <filename>`                                                    | `grep`-able output                                                                                 |
| `-oA <basename>`                                                    | Output in all major formats                                                                        |
## Tools Usage

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
