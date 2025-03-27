# Sniffing
## Tcpdump

It is based on `libpcap`

Examples:
```sh
tcpdump -i any tcp port 22
tcpdump -i wlo1 udp port 123
tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap
sudo tcpdump -r traffic.pcap -n | wc # calculate the number of packets
```

Specify network interface card
```sh
tcpdump -i eth0
tcpdump -i any
```

Save and read
```sh
tcpdump -w traffic # won't see packet scrolling
tcpdump -r traffic.pcap
```

Limit the number of received packets.
```sh
tcpdump -c 5
```

Don't resolve IP with DNS, or both IP and Port
```sh
tcpdump -n
tcpdump -nn
```

Verbose output
```sh
tcpdump -v/-vv/-vvv
```

Filtering by host
```sh
sudo tcpdump host IP/HOSTNAME
sudo tcpdump src host IP/HOSTNAME
sudo tcpdump dst host IP/HOSTNAME
```

Filtering by port
```sh
sudo tcpdump port PORT
sudo tcpdump src port PORT
sudo tcpdump dst port PORT
```

Filtering by protocol
```sh
sudo tcpdump icmp
sudo tcpdump arp
```

Logical operators
```sh
sudo tcpdump host 1.1.1.1 and tcp
sudo tcpdump port udp or icmp
sudo tcpdump not tcp
```

Filtering by length
```sh
sudo tcpdump -r traffic.pcap greater 1000
sudo tcpdump -r traffic2.pcap less 500
```

Advanced operation

```sh
proto[expr:size]
tcpdump "tcp[tcpflags] == tcp-syn"
tcpdump "tcp[tcpflags] & tcp-syn != 0"
tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"
```

Displaying packets

| Command       | Explanation                                                          |
| ------------- | -------------------------------------------------------------------- |
| `tcpdump -q`  | Quick and quite: brief packet information, without many `seq`, `ack` |
| `tcpdump -e`  | Include MAC addresses                                                |
| `tcpdump -A`  | Print packets as ASCII encoding                                      |
| `tcpdump -xx` | Display packets in hexadecimal format                                |
| `tcpdump -X`  | Show packets in both hexadecimal and ASCII formats                   |

