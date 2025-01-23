# C. Networking Core Protocols
## DNS 53

DNS (Domain Name System) will map a domain name to an IP address.
DNS traffic uses UDP port 53 by default and TCP port 53 as a default fallback (Server listens on port 53, and client will chose a high port).

- **A Record**: The A (address) record maps a hostname to one or more IPv4 addresses.
- **AAAA Record**: Similar to A record, but it is for IPv6.
- **CNAME Record**: The CNAME (Canonical) Record maps a domain name to another domain name. For example, `www.example.com` can be mapped to `example.com` or even to `example.org`.
- **MX Record**: The MX (Mail Exchange) Record specifies the mail server responsible for handling emails for a domain.

In other words, when you type `example.com` in your browser, your browser tries to resolve this domain name by querying the DNS server for the A record. However, when you try to send an email to `test@example.com`, the mail server would query the DNS server to find the MX record.

If you want to look up the IP address of the domain from the command line, you can use `lookup`.

```
┌──(passionforlife㉿kali)-[~/thm]
└─$ nslookup www.example.com
Server:         192.168.111.2
Address:        192.168.111.2#53

Non-authoritative answer:
Name:   www.example.com
Address: 93.184.215.14
Name:   www.example.com
Address: 2606:2800:21f:cb07:6820:80da:af6b:8b2c
```

The query above led to four packets. Look up for A Record and AAAA Record.
We can see the `tshark`.

![[Pasted image 20250103205018.png]]

The first and third packets send DNS queries for the A and AAAA Record. The second and fourth packets show the DNS query responses.

## WHOIS

If you want to set any records on a DNS server, you should have authority by providing accurate contact information.
This information is public, available via WHOIS records.
You can hide all of your information by using one of the privacy services.

Online: [whois](https://www.whois.com/whois) 
Command line on Linux: `whois`

想要有自己的域名，就要往 DNS Server 里面加一条记录，这通常由注册局 (registry) 和注册商组成。

 **常见的域名注册局**
 **国际注册局（ICANN 管理的 gTLD）**

- **Verisign**：负责 .com 和 .net。
- **PIR（Public Interest Registry）**：负责 .org。
- **Donuts**：管理多个新顶级域名（如 .studio、.email 等）。

 **国家/地区代码顶级域名（ccTLD）注册局**

- **CNNIC**（中国互联网络信息中心）：负责 .cn 和 .中国。
- **Nominet**：负责 .uk（英国）。
- **Afnic**：负责 .fr（法国）。

 **常见的域名注册商**
一些知名的域名注册商包括：

- 国际：GoDaddy、Namecheap、Google Domains、Bluehost。
- 中国：阿里云（万网）、腾讯云、华为云。

它们的区别：

| 功能       | 注册局                | 注册商              |
| -------- | ------------------ | ---------------- |
| **职责**   | 管理和维护顶级域名及其数据库     | 提供用户注册和域名管理服务    |
| **服务对象** | 域名注册商              | 最终用户（个人或企业）      |
| **技术维护** | 维护域名解析系统和 DNS 基础设施 | 提供增值服务，如隐私保护、SSL |
自己注册域名几乎是不可能的。域名注册是一个专业化、规范化的流程。大多数注册局只与注册商合作。少数注册局可以直接注册，不通过注册商。

如果你已经有了 `foo.com`，那么可以通过注册商弄个添加子域名的 DNS 记录，比如 `ctf.foo.com`。

## HTTP(S) 80 8080 443 8443

HTTP, stand for Hyper Text Transfer Protocol, and S for secure.
It relies on TCP.

Common request methods:
- `GET`: retrieves data from server, such as HTML files or an image.
- `POST`: Submit new data to server, such as a form or a file
- `PUT`: Create a new resource on the server
- `DELETE`: delete something

HTTP - listens on TCP port 80, less commonly 8080
HTTPS - listens on TCP port 443, less commonly 8443

What happens behind the web page?

![[Pasted image 20250104114215.png]]

`GET` example:
```http
GET /flag.html HTTP/1.1
Host: 10.10.79.168
```

## FTP 21

File Transfer Protocol.
FTP is designed to transfer file, and more efficient than HTTP when all conditions are equal.

- `USER` input username.
- `PASS` input password.
- `RETR` (retrieve) a file from FTP server to client.
- `STOR` (store) upload a file from client to FTP server.

FTP uses two tunnel:
- control tunnel
	FTP server listens on TCP port 21 by default, for receiving commands and responding client.
- data transfer tunnel
	FTP server listens on TCP port 20 (positive mode) or dynamic port (passive mode)

About transfer tunnel
Positive mode
	After client sends commands to server, server connect the designated client port with its TCP port 20.
Passive mode
	Client request server open a random high port for data transfer, client positively connect the high port.

Let's using `ftp` command, it will use the FTP command automatically like a translator.

Examples:
```sh
ftp 10.10.79.168
anonymnous # without password
ls
type ascii
get coffe.txt
quit
```

What happened behind ?
![[Pasted image 20250104120310.png]]

Noticed that `ftp` command translate our operations to FTP command, and the data transfer uses a separate connection.

## SMTP 25
Simple Mail Transfer Protocol.
Listens on TCP port 25.

As with browsing the web and downloading files, sending mails needs its own protocol.
SMTP defines the mail client and mail server how to talk with each other.

You might be asked to show your identity card.

Common commands used by mail client.
- `HELO client.domain` or `EHLO` ,  initiates a SMTP session.
- `MAIL FROM: <user@foo.com>`, specifies the sender's email address.
- `RCPT TO: <admin@bar.com>`, specifies the recipient's email address.
- `DATA`, begin sending the content of message.
- `.`, end the session.

![[Pasted image 20250104121746.png]]

![[Pasted image 20250104121759.png]]

Noticed that the recipient's information should be routable in the mail server.

## POP3 110
Post Office Protocol version 3.
Listens on TCP port 110.

The email will be sent to the recipient's mail server, not the recipient. Recipients should retrieve the email by communicating with his or her mail server, where POP3 comes in.

An email client sends its message by relying on SMTP and retrieves them using POP3. 

Common command 
```sh
USER <username> 
PASS <password>
STAT # request the number of messages and total size
LIST # list all messages and their size
RETR <message_number> # retrieves the specified message
DELE <message_number> # marks a message for deletion
QUIT # quit and save changes
```

![[Pasted image 20250104124427.png]]

![[Pasted image 20250104124451.png]]

Noticed that the messages can be intercepted, and someone can see the password.

## IMAP
Internet Message Access Protocol.
Listens on TCP port 143 by default.

IMAP allows synchronizing read, moved, and deleted messages. 
Unlike POP3 which tends to minimize server storage as email is downloaded and deleted from the remote server, IMAP tends to use more storage as email is kept on the server and synchronized across the email clients.

Commands:
```sh
LOGIN <username> <password>
SELECT <mailbox> # selects the mailbox folder to work with
FETCH <mail_number> <data_item_name>  # fetch 3 []body to fetch message number 3, header and body
MOVE <sequence_set> <mailbox> # moves the specified messages to another mailbox
COPY <sequence_set> <mailbox> # copies the specified messages to another mailbox
LOGOUT
```

 **IMAP 与 POP3 的区别**

| 特性         | IMAP          | POP3            |
| ---------- | ------------- | --------------- |
| **邮件存储位置** | 邮件保留在服务器上     | 邮件下载到本地后从服务器删除  |
| **多设备访问**  | 支持            | 通常不支持           |
| **同步功能**   | 支持            | 不支持             |
| **操作灵活性**  | 支持在服务器上直接管理邮件 | 不支持             |
| **离线访问**   | 部分支持（需先同步）    | 支持（已下载的邮件可离线访问） |
## Conclusion

| **Protocol** | **Transport Protocol** | **Default Port Number** (Server Side) |
| ------------ | ---------------------- | ------------------------------------- |
| TELNET       | TCP                    | 23                                    |
| DNS          | UDP or TCP             | 53                                    |
| HTTP         | TCP                    | 80                                    |
| HTTPS        | TCP                    | 443                                   |
| FTP          | TCP                    | 21                                    |
| SMTP         | TCP                    | 25                                    |
| POP3         | TCP                    | 110                                   |
| IMAP         | TCP                    | 143                                   |
Although the TELNET protocol uses TCP port 23, `telnet` is a TCP client tool and can connect to TCP port, communicating server by the service.