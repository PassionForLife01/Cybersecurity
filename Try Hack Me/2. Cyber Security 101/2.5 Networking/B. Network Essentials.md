# B. Network Essentials
## DHCP 67

When we connect to the network, we need configure the following:
- IP address along with the subnet mask
- Router or gateway
- DNS server

DHCP (Dynamic Host Configuration Protocol) will give us a unique IP address in the subnet.
This protocol relies on UDP.
Server listens on UDP port 67, and client sends from UDP port 68.

DHCP follows 4 steps: Discover, Offer, Request, Acknowledge

1. **DHCP Discover**: The client ==broadcasts== a DHCPDISCOVER message seeking the local DHCP server if one exists.
2. **DHCP Offer**: The server responds with a DHCPOFFER message with an IP address available for the client to accept.
3. **DHCP Request**: The client responds with a DHCPREQUEST message to indicate that it has accepted the offered IP.
4. **DHCP Acknowledge**: The server responds with a DHCPACK message to confirm that the offered IP address is now assigned to this client.

![[Pasted image 20250103091007.png]]

Examples:
```
tshark -r DHCP-G5000.pcap -n

1 0.000000 0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Discover - Transaction ID 0xfb92d53f 
2 0.013904 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP Offer - Transaction ID 0xfb92d53f 
3 4.115318 0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Request - Transaction ID 0xfb92d53f 
4 4.228117 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP ACK - Transaction ID 0xfb92d53f
```

Noticed:
- In the discover and request, the client hasn't use the IP address offered by DHCP server, it sends packets from `0.0.0.0` to broadcast IP address `255.255.255.255`. It only has a MAC address.
- As for link layer, in the first and third packet, the client sends to the broadcast MAC address, `ff:ff:ff:ff:ff:ff`. The DHCP server offers an available IP address along with the network configuration in the DHCP offer. It uses the client’s destination MAC address.

After the end process of DHCP, we complete the configuration to access the Internet. We expect that DHCP server has provided us with the following:
- Leased IP address
- The gateway or router to route our packets outside the local network
- A DNS server to resolve domain names.

## ARP

Two common data link layer we use are Ethernet and Wi-Fi.  

==Two devices on the same network cannot communicate without knowing each other's MAC addresses.==

The Ethernet frame contains:
- Destination MAC address
- Source MAC address
- Type (IPv4 in this case)

![[Pasted image 20250103143014.png]]

ARP (Address Resolution Protocol) makes it possible to find devices in the same network, given IP address.

- ARP Request: Broadcast in the network, ask who has the IP.
- ARP Reply: Reply the requester with MAC address.

```
user@TryHackMe$ tshark -r arp.pcapng -Nn
    1 0.000000000 cc:5e:f8:02:21:a7 → ff:ff:ff:ff:ff:ff ARP 42 Who has 192.168.66.1? Tell 192.168.66.89
    2 0.003566632 44:df:65:d8:fe:6c → cc:5e:f8:02:21:a7 ARP 42 192.168.66.1 is at 44:df:65:d8:fe:6c
```
Notice the `ff:ff:ff:ff:ff:ff` is the MAC broadcast address.


```
user@TryHackMe$ tcpdump -r arp.pcapng -n -v
17:23:44.506615 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.66.1 tell 192.168.66.89, length 28
17:23:44.510182 ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.168.66.1 is-at 44:df:65:d8:fe:6c, length 28
```

ARP Request or Reply is not encapsulated with an UDP or even IP packet; it is directly encapsulated within an Ethernet frame.

It can be considered as layer 2 or layer 3.

In conclusion, ARP allows the translation from layer 3 addressing to layer 2 addressing.

## ICMP
### Intro
ICMP (Internet Control Message Protocol) is mainly used for network diagnostics and error reporting. 

Two popular commands rely on ICMP, and they are instrumental in troubleshooting and network security.

- `ping` Check if we can reach the target and target can reach us, and measures the round-trip time (RTT). 
- `traceroute` or `tracert` It uses ICMP to discover the route from you to the target.

### Ping

The `ping` command sends a ICMP Echo Request (ICMP Type `8`).

![[Pasted image 20250103145434.png]]

The receiver responds with an ICMP Echo Reply (ICMP Type `0`)

![[Pasted image 20250103145718.png]]

If the target is offline or shutdown, or the firewall prevent the ICMP packet, we cannot get a response. 

Limit the number of packets, avoid ping all the time :
```sh
ping 192.168.11.1 -c 4
```

### Traceroute

Internet Protocol has a filed called TTL (Time To Live)
TTL: The maximum number of routers a packet can travel through before it is dropped.
Each router received the packet will decrement TTL by 1, and route it to next router.
When TTL equals to 0, the packet will be dropped, and sends an ICMP Time Exceeded message (ICMP Type 11).

In this context, time is measured by the number of routers, not seconds.

So we can use the TTL, set it to different values to control to search depth, and when the packet is dropped, the router will return ICMP Time Exceeded message, along with its IP or domain name. And thus we can get the info about path.

![[Pasted image 20250103151354.png]]

Some routers don’t respond; in other words, they drop the packet without sending any ICMP messages. Routers that belong to our ISP might respond, revealing their private IP address. Moreover, some routers respond and show their public IP address, and this would let us look up their domain name and discover their geographic location. Finally, there is always a possibility that an ICMP Time Exceeded message gets blocked and never reaches us.

### Conclusion About ICMP

| ICMP Packet                | Type | Usage        |
| -------------------------- | ---- | ------------ |
| ICMP Echo Request          | 8    | ping request |
| ICMP Echo Reply            | 0    | ping reply   |
| ICMP Time Exceeded message | 11   | `traceroute` |
## Routing

![[Pasted image 20250103185811.png]]

How to route the packets to the proper place? Here comes the routing algorithm.

- OSPF (Open Shortest Path First)
- EIGRP (Enhanced Interior Gateway Routing Protocol)
- BGP (Border Gateway Protocol)
- RIP (Routing Information Protocol)

They both share the information about networks they are in.
Routing table support this.

## NAT

We cannot give every computer an IPv4 address nowadays.
One solution to address depletion is Network Address Translation.

The idea behind NAT lies in using one public address to provide the Internet access to many private IP addresses.

The NAT-supporting routers maintain a table translating network address between internal and external networks.
The internal network would use a private IP address range, while the external network would use a public IP address.

![[Pasted image 20250103192104.png]]

You should notice that this is a one-way connection, which means that other device that wanna reach you will be failed without port forwarding.

