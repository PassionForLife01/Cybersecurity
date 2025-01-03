# A. Networking Concepts
## OSI Model
### Intro
The OSI (Open System Interconnection) Model is a conceptual model.
OSI define a framework for computer network communication.

Then 7 layers
1. Physical Layer
2. Data Link Layer
3. Network Layer
4. Transport Layer
5. Session Layer
6. Presentation Layer
7. Application Layer
“Please Do Not Throw Sausage Pizza Away.” from bottom to top
### Layer 1: Physical Layer

Physical connection between devices.
- medium
Wi-Fi radio bands, the 2.4 GHz band, the 5 GHz band, and the 6 GHz band.
- wire
Fiber cable
- 0's and 1's
Electrical, optical or wireless signal
### Layer 2: Data Link Layer

Layer 1 offer physical fundamentals for connection, like medium, wire and data.
Layer 2 is responsible for how to interconnect with other nodes in the same network segment, including same protocols.
A network segment refers to a group of networked device using a shared medium or channel for information transfer.
	 For example, consider a company office with ten computers connected to a network switch; that’s a network segment.

The most popular feature in this layer is MAC (Media Access Control) address.
It is comprised of 6 bytes, for connection in the same network segment.
![[Pasted image 20241231143551.png]]
![[Pasted image 20241231143627.png]]

### Layer 3: Network Layer

Data-link layer focuses on sending data between two nodes on the same network segment.
Network Layer is concerned with sending data between different networks, responsible for logical addressing and routing.
	In the data link layer, we gave an example of one company office with ten computers, where the data link layer is responsible for providing a connection between them. Let’s say that this company has multiple offices distributed across various cities, countries, or even continents. The network layer is responsible for connecting the different offices together.

![[Pasted image 20241231144246.png]]

Examples: Internet Protocol (IP), Internet Control Message Protocol (ICMP), Virtual Private Network (VPN).

### Layer 4: Transport Layer

Transport layer enables end-to-end communication between running applications on different hosts.

Examples: Transmission Control Protocol (TCP), User Datagram Protocol (UDP)

### Layer 5: Session Layer

The session layer is responsible for establishing, maintaining and synchronizing communication between running applications on different hosts.

Examples: Network File System (NFS), Remote Procedure Call (RPC).

### Layer 6: Presentation Layer

Presentation layer ensures that data is delivered in a form that the application layer can understand.
This layer handles data encoding, compression, and encryption.

Examples: MIME (Multipurpose Internet Mail Extensions)

### Layer 7: Application Layer

The application layer provides network services directly to end-user applications.

Examples: HTTP, FTP, DNS, POP3, SMTP (Simple Mail Transfer Protocol) and IMAP (Internet Message Access Protocol)

### Summary

|Layer Number|Layer Name|Main Function|Example Protocols and Standards|
|---|---|---|---|
|Layer 7|Application layer|Providing services and interfaces to applications|HTTP, FTP, DNS, POP3, SMTP, IMAP|
|Layer 6|Presentation layer|Data encoding, encryption, and compression|Unicode, MIME, JPEG, PNG, MPEG|
|Layer 5|Session layer|Establishing, maintaining, and synchronising sessions|NFS, RPC|
|Layer 4|Transport layer|End-to-end communication and data segmentation|UDP, TCP|
|Layer 3|Network layer|Logical addressing and routing between networks|IP, ICMP, IPSec|
|Layer 2|Data link layer|Reliable data transfer between adjacent nodes|Ethernet (802.3), WiFi (802.11)|
|Layer 1|Physical layer|Physical data transmission media|Electrical, optical, and wireless signals|
## TCP/IP Model

It allows a network to continue to function as parts of it are out of service, for instance, due to a military attack.

In TCP/IP Model:
- Application Layer: The OSI model application, presentation, and session.
- Transport Layer: layer 4
- Internet Layer: layer 3
- Link Layer: layer 2

The table below shows how the TCP/IP model layers map to the ISO/OSI model layers.

| Layer Number | ISO OSI Model      | TCP/IP Model (RFC 1122) | Protocols                                        |
| ------------ | ------------------ | ----------------------- | ------------------------------------------------ |
| 7            | Application Layer  | Application Layer       | HTTP, HTTPS, FTP, POP3, SMTP, IMAP, Telnet, SSH, |
| 6            | Presentation Layer |                         |                                                  |
| 5            | Session Layer      |                         |                                                  |
| 4            | Transport Layer    | Transport Layer         | TCP, UDP                                         |
| 3            | Network Layer      | Internet Layer          | IP, ICMP, IPSec                                  |
| 2            | Data Link Layer    | Link Layer              | Ethernet 802.3, WiFi 802.11                      |
| 1            | Physical Layer     |                         |                                                  |

Many modern textbooks show the TCP/IP model as five layers instead as four. Add a physical layer.

## IP Addresses and Subnets
### Intro

IP address is the unique identifier in the world of network for each host.
When it comes to the IP address without version, we expect it to mean IPv4.

`192.168.1.1`
- `192.168.1.0` - network address. Clarify this is a network.
- `192.168.1.255` - broadcast address. Sending to the broadcast address targets all the hosts on the network.

### Network Configuration

`ipconfig` on Windows.
`ifconfig` or `ip a s` on Linux and UNIX-based systems

`192.168.1.1/24`
$24$ means that leftmost digit in subnet mask in binary is $1$.
`255.255.255.0`

### Private Address

With the NAT (Network Address Translation) model, there are two types of IP address.
- Public IP address
- Private IP address

RFC 1918 defines the following three ranges of private IP address:
- `10.0.0.0` - `10.255.255.255` (`10/8`)
- `172.16.0.0` - `172.31.255.255` (`172.16/12`)
- `192.168.0.0` - `192.168.255.255` (`192.168/16`)

Begin with `10`, `172.16 - 172.31`, `192.168`

The public IP address is your home postal address, but private IP address is the room in your house. The original idea is that it cannot be reached from outside world.

### Routing

A router forwards data packets to the proper network.
## UDP and TCP
### Intro
Two ports that enable processes on networked hosts to communicate with other.
IP protocol helps us find that specific hosts. UDP and TCP determine the sending and receiving processes.
### UDP
Connectionless protocol, without confirmation.
It has "ports", which define which port will send or receive packets.
A port number uses two octets, ranging from $1$ to $65535$ ($2^{16} - 1$), $0$ is reserved.
### TCP
Advanced version of UDP.
- Establish a TCP connection before any data can be sent.
- Each data octet has a sequence number. On the receiver side, it will acknowledge data by sending an acknowledge number which specify the last received octet.

Three-way handshake. (SYN, ACK)
- SYN Packet: The client sends a SYN packet to server, including a randomly chosen initial sequence number.
- SYN-ACK Packet: The server responds to SYN packet with a SYN-ACK packet, which adds the initial sequence number in SYN packet.
- ACK Packet: Client sends the ACK packet to acknowledge the SYN-ACK packet, which completes the connection.

![[Pasted image 20250102211904.png]]

## Encapsulation

When you send some data to a remote server, there is a encapsulation which means that your data will be wrapped layer by layer below.

The process of encapsulation, from top to bottom.
![[Pasted image 20250102212821.png]]

This process has to be reversed on the receiving end until the application data is extracted.

## Telnet

The TELNET (Teletype Network) protocol is a network protocol for remote terminal connection.

==A `telnet` client allows you connect to and communicate with a remote system and issue text commands.== There are no applications help you automatically do some complex works, you can only manually type the commands to the server.

==And we can use `telent` to connect to any server listening on a TCP port.==

There are different services can be run at server.
- Echo Server (7): Echo everything you send.
- Daytime Server (13): Return the current time (PM UTC).
- HTTP(Web) Server (80): Respond to your HTTP request typed by yourself manually, following the HTTP protocol.

Connect to a TCP port.
```sh
telnet 10.10.78.53 7 # Echo Server
telnet 10.10.78.53 13
telnet 10.10.78.53 80
```

```http
GET / HTTP/1.1
Host: telnet.thm
```

Stop the connection, using `^]` (Ctrl + ])
```
Trying 10.10.78.53...
Connected to 10.10.78.53.
Escape character is '^]'.
Hi
Hi

^]
telnet> quit
Connection closed.
```

