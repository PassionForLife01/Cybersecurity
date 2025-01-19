# F. Tcpdump Basics
## Intro

The best study aid would be capturing network traffic and taking a closer look at the various protocols. 

The `tcpdump` tool and its `libpcap` are written in C and C++, and were released for Unix-like systems in the 1980s or early 1990s.

`libpcap` library is the foundation for various network tools today.

## Basic Packet Capture

run `tcpdump` just as for test.
In most scenarios, you should specify:
- what to listen to 
- where to write
- how to display

### Specify Network Interface

Specify network interface.
```sh
tcpdump -i INTERFACE
```

Listen to any interface.
```sh
tcpdump -i any
```

Show the interface.
```sh
ip address show
ip a s
```

### Save

with `-w`, won't see packets scrolling.
```sh
tcpdump -w FILE
```

### Read from a File

from a file
```sh
tcpdump -r FILE
```

### Limit the number of packets

limit
```sh
tcpdump -c COUNT
```

If don't limit, the capturing process wouldn't stop, until you press the `CTRL + C`.

### Don't Resolve IP and Port

without DNS
```sh
tcpdump -n
```

avoid converting `80` to `http`, both DNS lookup
```sh
tcpdump -nn
```

### Produce Verbose Output

```sh
tcpdump -v / -vv / -vvv
```

 -v     When parsing and printing, produce (slightly more) verbose output.  For example,  the  time  to live, identification, total length and options in an IP packet are printed.  Also enables additional packet integrity checks such as verifying the IP and ICMP header checksum.

  When writing to a file with the -w option and at the same time not reading from a file with the
  -r  option,  report  to  stderr,  once  per second, the number of packets captured. In Solaris,
  FreeBSD and possibly other operating systems this periodic update currently can cause  loss  of
  captured packets on their way from the kernel to tcpdump.

-vv    Even  more  verbose output.  For example, additional fields are printed from NFS reply packets,
	  and SMB packets are fully decoded.

-vvv   Even more verbose output.  For example, telnet SB ... SE options are printed in full.  With  -X
	  Telnet options are printed in hex as well.

### Summary and Examples

|Command|Explanation|
|---|---|
|`tcpdump -i INTERFACE`|Captures packets on a specific network interface|
|`tcpdump -w FILE`|Writes captured packets to a file|
|`tcpdump -r FILE`|Reads captured packets from a file|
|`tcpdump -c COUNT`|Captures a specific number of packets|
|`tcpdump -n`|Don’t resolve IP addresses|
|`tcpdump -nn`|Don’t resolve IP addresses and don’t resolve protocol numbers|
|`tcpdump -v`|Verbose display; verbosity can be increased with `-vv` and `-vvv`|
- `tcpdump -i eth0 -c 50 -v` captures and displays 50 packets by listening on the `eth0` interface, which is a wired Ethernet, and displays them verbosely.
- `tcpdump -i wlo1 -w data.pcap` captures packets by listening on the `wlo1` interface (the WiFi interface) and writes the packets to `data.pcap`. It will continue till the user interrupts the capture by pressing CTRL-C.
- `tcpdump -i any -nn` captures packets on all interfaces and displays them on screen without domain name or protocol resolution.

## Filtering Expressions

Given that how many packets network card captures at one second, we maybe cannot get the valuable info immediately, so the filter comes in.

### Filtering by Host

```sh
sudo tcpdump host IP/HOSTNAME
sudo tcpdump src host IP/HOSTNAME
sudo tcpdump dst host IP/HOSTNAME
```

It is important to note that capturing packets requires you to be logged-in as `root` or to use `sudo`.

### Filtering by Port

```sh
sudo tcpdump port PORT
sudo tcpdump src port PORT
sudo tcpdump dst port PORT
```

Examples, keep tack of DNS traffic
```sh
sudo tcpdump port 53
```

### Filtering by Protocol

You can limit your packet capture to a specific protocol, such as `ip`, `ip6`, `udp`, `tcp` and `icmp`.

Example: capture ICMP echo request and reply.
```sh
sudo tcpdump icmp
```

### Logical Operators

Three logical operators that can be handy:
- `and`. `sudo tcpdump host 1.1.1.1 and tcp`
- `or`. `sudo tcpdump port upd or icmp`
- `not`. `sudo tcpdump not tcp`

### Summary and Examples

| Command                                      | Explanation                                                           |
| -------------------------------------------- | --------------------------------------------------------------------- |
| `tcpdump host IP` or `tcpdump host HOSTNAME` | Filters packets by IP address or hostname                             |
| `tcpdump src host IP` or                     | Filters packets by a specific source host                             |
| `tcpdump dst host IP`                        | Filters packets by a specific destination host                        |
| `tcpdump port PORT_NUMBER`                   | Filters packets by port number                                        |
| `tcpdump src port PORT_NUMBER`               | Filters packets by the specified source port number                   |
| `tcpdump dst port PORT_NUMBER`               | Filters packets by the specified destination port number              |
| `tcpdump PROTOCOL`                           | Filters packets by protocol; examples include `ip`, `ip6`, and `icmp` |

Consider the following examples:

- `tcpdump -i any tcp port 22` listens on all interfaces and captures `tcp` packets to or from `port 22`, i.e., SSH traffic.
- `tcpdump -i wlo1 udp port 123` listens on the WiFi network card and filters `udp` traffic to `port 123`, the Network Time Protocol (NTP).
- `tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap` will listen on `eth0`, the wired Ethernet interface and filter traffic exchanged with `example.com` that uses `tcp` and `port 443`. In other words, this command is filtering HTTPS traffic related to `example.com`.

Count the total number of packets
```sh
sudo tcpdump -r traffic.pcap -n | wc

reading from file traffic.pcap, link-type EN10MB (Ethernet)
 252042 4576440 37398057
```

The first number is total lines, second number is total words, and third number is total characters.

## Advanced Filtering

In real-life situation, we need to filter through thousands or even millions of packets. It is dispensable able to express the exact packets to display.

Limit the displayed packets to those smaller or larger than a certain length, in bytes.

- `greater LENGTH`: greater than or equal to the specified length
- `less LENGHT`: less than or equal to the specified length

They recommend us check the `man pcap-filter`.
And in this room they will focus on one advanced option that allows us to filter packets based on the TCP flags. 
Understanding the TCP flags will make it easier to build on this knowledge and master more advanced filtering technologies.

### Binary Operations

