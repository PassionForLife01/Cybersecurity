# F. Tcpdump Basics
## Intro

The best study aid would be capturing network traffic and taking a closer look at the various protocols. 

The `tcpdump` tool and its `libpcap` are written in C and C++, and were released for Unix-like systems in the 1980s or early 1990s.

`libpcap` library is the foundation for various network tools today, including `wireshark`.

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

Just the operations you know, and(`&`), or (`|`) and not (`!`).

And each operation connotes a logic table, it defines the operation.

### Header Bytes

We will focus on TCP flags to filter the packets.

Using `pcap-filter` , `tcpdump` allows you to refer to the contents of any byte in the header using the following syntax `proto[expr:size]`, where:
- `proto` refers to the protocol, `arp`, `ether`, `icmp`, `ip`, `ip6`, `tcp`, and `udp`
- `expr` indicates the byte offset where `0` refers to the first byte.
- `size` indicates the number of bytes that interest us, which can be one, two or four. It is optional and is **one by default**.

Examples:
- `ether[0] & 1 != 0` takes the first byte in the Ethernet header and the decimal number 1 (i.e., `0000 0001` in binary) and applies the `&` (the And binary operation). It will return true if the result is not equal to the number 0 (i.e., `0000 0000`). The purpose of this filter is to show packets sent to a multicast address. A multicast Ethernet address is a particular address that identifies a group of devices intended to receive the same data.  
    
- `ip[0] & 0xf != 5` takes the first byte in the IP header and compares it with the hexadecimal number F (i.e., `0000 1111` in binary). It will return true if the result is not equal to the (decimal) number 5 (i.e., `0000 0101` in binary). The purpose of this filter is to catch all IP packets with options.

Don't worry if you find the above two examples complex. We will focus on filtering TCP packets based on the set TCP flags.

Use `tcp[tcpflags]` to refer to the TCP flags field of bits. The following TCP flags are available to compare with:
- `tcp-syn` TCP SYN (Synchronize)
- `tcp-ack` TCP ACK (Acknowledge)
- `tcp-fin` TCP FIN (Finish)
- `tcp-rst` TCP RST (Reset)
- `tcp-push` TCP Push.

Actually, you can consider the `tcp[tcpflags]` as a group of bits, it contains a few flags, and these flags will either be 1 or be 0. Like the flag field below:

![[Pasted image 20250119200537.png]]

And the TCP flags which are available to compare can be considered a group of bits that has the same length with `tcp[tcpflags]` but only the right place has been set as 1.

`tcp-syn` -- `00000010`

So this is the foundation of filtering TCP packets by binary operation.

Examples:

- `tcpdump "tcp[tcpflags] == tcp-syn"` to capture TCP packets with **only** the SYN (Synchronize) flag set, while all the other flags are unset.
- `tcpdump "tcp[tcpflags] & tcp-syn != 0"` to capture TCP packets with **at least** the SYN (Synchronize) flag set.
- `tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"` to capture TCP packets with **at least** the SYN (Synchronize) **or** ACK (Acknowledge) flags set.

## Displaying Packets

`tcpdump` has many options to customize how the packets are printed and displayed.

Just a few options.
- `-q`: Quick output; print brief packet information
- `-e`: Print the link-level header
- `-A`: Show packet data in ASCII
- `-xx`: Show packet data in hexadecimal format, referred to as hex
- `-X`: Show packet headers and data in hex and ASCII

### `-q`

Just brief packet information, without many like `seq`, `ack` these things.

### `-e`

Output the MAC address, allows you to learn protocols like DHCP and ARP.

### `-A`

Show bytes in ASCII format, friendly for English.

### `-xx`

Show bytes in hexadecimal format.

### `-X`

Show bytes both in hexadecimal format and ASCII format.
### Summary

| Command       | Explanation                                                          |
| ------------- | -------------------------------------------------------------------- |
| `tcpdump -q`  | Quick and quite: brief packet information, without many `seq`, `ack` |
| `tcpdump -e`  | Include MAC addresses                                                |
| `tcpdump -A`  | Print packets as ASCII encoding                                      |
| `tcpdump -xx` | Display packets in hexadecimal format                                |
| `tcpdump -X`  | Show packets in both hexadecimal and ASCII formats                   |

## Closing Notes

You maybe want to compare this with `tshark`. 