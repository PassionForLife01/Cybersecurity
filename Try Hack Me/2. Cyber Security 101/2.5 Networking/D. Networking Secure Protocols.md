# D. Networking Secure Protocols
## Intro

We learned a lot about protocols in the previous room, but they cannot protect the confidentiality, integrity and authenticity. 

- confidentiality: everyone cannot understand your packet even though they intercept it. Cryptography.
- integrity: everyone cannot change your contents of the transferred data.
- authenticity: you are talking with the correct server, not a fake one.

TLS comes in, stand for Transport Layer Security.

Telnet connection is insecure.
SSH was created to provide a secure way to access remote systems.

SSL (Secure Sockets Layer) -> TLS (Transport Layer Security)
## TLS
### Intro
TLS is a cryptographic protocol operating at the OSI model's transport layer. It allows secure communication between a client and a server over an insecure network.

Many protocols are upgraded with the addition of TLS.

How TLS is set up and used.
 [Network Security Protocols](https://tryhackme.com/r/room/networksecurityprotocols)

### Technical Background

- If you want to identify yourself, the first step is to get a signed TLS certificate.
- The server admin creates a CSR and submits it to a CA. 
	CSR, stands for Certificate Signing Request
	CA, stands for Certificate Authority
- CA verifies the CSR and issue a digital certificate.
- Once the certificate is received, it can be used to identify the server (or the client) to others, who confirm the validity of the signature.
- For a host to confirm the validity of a signed certificate, the certificates of the signing authorities need to be installed on the host.

The screenshot below shows the trusted authorities installed on a web browser.

![[Pasted image 20250104152700.png]]

Get a certificate signed requires a annual fee. But [Let’s Encrypt](https://letsencrypt.org/) allows you to get your certificate signed for free.

Self-signed certificate cannot prove the authenticity, as no third party has confirmed it.

## HTTPS
### HTTP
After resolving the domain name, before we see a web page:
- Establish a TCP three-way handshake with the target server.
- Communicate using the HTTP protocol. `GET / HTTP/1.1`

![[Pasted image 20250104153807.png]]

### HTTP Over TLS

HTTPS, stand for Hyper Text Transfer Protocol Secure.

After resolving the domain name, before we see a web page:
- Establish a TCP three-way handshake with the target server.
- Establish a TLS session.
- Communicate using the HTTP protocol. `GET / HTTP/1.1`

As the below screenshot:
TCP session, marked with 1.
TLS session, marked with 2.
HTTP application data, marked with 3.

![[Pasted image 20250104154643.png]]

Notice the third part, the info is Application Data, because there is no way to understand the contents without the encryption key.

If someone tries to open the packet, they will only get gibberish:

![[Pasted image 20250104154904.png]]

### Getting the Encryption Key

The all contents will be encrypted by a private key. If we get the key, we can decrypt the message.

The screenshot shows the contents after decryption:

![[Pasted image 20250104155831.png]]

We can see the plaintext `GET`.

![[Pasted image 20250104155954.png]]

The key takeaway is that TLS offered security for HTTP without requiring any changes in the lower or higher layer protocols. In other words, TCP and IP were not modified, while HTTP was sent over TLS the way it would be sent over TCP.

## SMTPS, POP3S and IMAPS

Using these protocols over TLS is no different than using HTTP over TLS.

And the ports that they and their insecure version are using are different. 

|Protocol|Default Port Number|
|---|---|
|HTTP|80|
|SMTP|25|
|POP3|110|
|IMAP|143|

|Protocol|Default Port Number|
|---|---|
|HTTPS|443|
|SMTPS|465 and 587|
|POP3S|995|
|IMAPS|993|
TLS can be added to many other protocols; the reasoning and advantages would be similar.

## SSH

Telnet -> SSH-1 -> SSH-2 -> OpenSSH (implementation of the SSH protocol)

Nowadays, when you use an SSH client, it is most likely based on OpenSSH libraries and source code.

OpenSSH offers several benefits. We will list a few key points:
- Secure Authentication: Besides password, SSH supports public key and two-factor authentication.
- Confidentiality: OpenSSH provides end-to-end encryption. 
- Integrity: protect integrity.
- Tunneling: SSH can create a secure "tunnel" to route other protocols through SSH, like a VPN connection.
- X11 Forwarding: If you connect to a Unix-like system with a graphical user interface, SSH allows you to use the graphical application over the network.

Log in
```sh
ssh username@hostname
ssh hostname # if the username on host is same as your logged-in username
```
However, if public-key authentication is used, you will be logged in immediately.

Log in, supporting running graphical interfaces.
```sh
ssh 192.168.124.148 -X
```

While the TELNET server listens on port 23, the SSH server listens on port 22.

## SFTP and FTPS

SFTP, stands for SSH File Transfer Protocol. It is a part of SSH protocol suite and shares the same port number 22.
If enabled in the OpenSSH server configuration, you can connect using:
```sh
sftp username@hostname
get filename
put filename
```

FTPS, a secure version of FTP, over TLS.
FTP listens on port 21, FTPS listens on port 990, all by default.

It requires certificate setup, and it can be tricky to allow over strict firewalls as it uses separate connections for control and data transfer.

## VPN

Virtual Private Protocol.

Allow all computers lie in a same subnet, and create a private environment.
All of this is set up by Internet connectivity and a VPN server and client.

The network diagram below shows an example of two remote branches connecting to the main branch.
- A VPN client in the remote branch is expected to connect to the VPN server in the main branch.
- In this case, the VPN client will encrypt the traffic and pass it to the main branch via the established VPN tunnel (show in blue).
- The VPN traffic is limited in blue line; the green lines would carry the decrypted VPN traffic.

![[Pasted image 20250104164947.png]]

The VPN client can be a single device:

![[Pasted image 20250104165416.png]]

The benefits:

- VPN can connect different branches together.

- VPN route your traffic, so you can hide your actual IP address, substituted with VPN server's IP address. But some VPN servers only allow you to access private network, not routing.

- VPN can encrypt your traffic in the tunnel across the Internet.

## Closing Notes

3 main approaches to secure network traffic:
- Over TLS: HTTPS, SMTPS, POP3S, IMAPS and FTPS.
- SSH: Creating a secure tunnel for remote control and transfer file
- VPN: Connect two company branches, encrypt data and route traffic

Log the session's TLS keys:
```sh
chromium --ssl-key-log-file=~/ssl-key.log
```
Dump the TLS keys to the `ssl-key.log`

`chromium` is a free and open-source web browser.

Right-click a packet using TLS protocol, then follow the steps below:
![[Pasted image 20250104183931.png]]

Then click the `Browse..` button, select the `ssl-key.log` file in `~/Document`. 
![[Pasted image 20250104184108.png]]

Then sort these packets by protocol, find the keyword "login", and open the `DATA` packet under the `HEADER` packet, finally find the flag.

