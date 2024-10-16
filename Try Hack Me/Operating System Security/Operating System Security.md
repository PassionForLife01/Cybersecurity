## A. Introduction
### 1. Hardware
![[Pasted image 20240815190602.png]]
- Screen, keyboard, the printer, and the USB flash memory
- Desktop board, CPU(a central processing unit), RAM (memory chips), storage device (HDD or SSD). Hard Dish Drive, and Solid State Drive
However, hardware components by themselves are useless if you want to run your favorite programs and applications. We need an Operating System to control and “drive” them.
### 2. Operating System
The Operating System (OS) is the layer sitting between the hardware and the applications and programs you are running. The operating system allows these programs to access the hardware according to specific rules.

The image below shows the popularity of the different operating systems used to browse the Internet according to [Statcounter](https://gs.statcounter.com/os-market-share) based on the data collected during January 2022.
![[Pasted image 20240815191221.png]]
### 3. Security Pillars
- Confidentiality: You want to ensure that secret and private files and information are only available to intended persons.
- Integrity: It is crucial that no one can tamper with the files stored on your system or while being transferred on the network.
- Availability: You want your laptop or smartphone to be available to use anytime you decide to use it.
![[Pasted image 20240815191916.png]]

## B. Common Examples of OS Security
### 1. Authentication and Weak Passwords
Authentication is the act of verifying your identity, be it a local or a remote system. Authentication can be achieved via three main ways:
- Something you know, such as a password or a PIN code.
- Something you are, such as a fingerprint.
- Something you have, such as a phone number via which you can receive an SMS message.

Since passwords are the most common form of authentication, they are also the most attacked.
`https://www.ncsc.gov.uk/static-assets/documents/PwnedPasswordsTop100k.txt`

This is top 20 passwords used.

| Rank | Password   |
| ---- | ---------- |
| 1    | 123456     |
| 2    | 123456789  |
| 3    | qwerty     |
| 4    | password   |
| 5    | 111111     |
| 6    | 12345678   |
| 7    | abc123     |
| 8    | 1234567    |
| 9    | password1  |
| 10   | 12345      |
| 11   | 1234567890 |
| 12   | 123123     |
| 13   | 000000     |
| 14   | iloveyou   |
| 15   | 1234       |
| 16   | 1q2w3e4r5t |
| 17   | qwertyuiop |
| 18   | 123        |
| 19   | monkey     |
| 20   | dragon     |
### 2. Weak File Permissions
Proper security dictates the principle of ***least privilege***.
Weak file permissions make it easy for the adversary to attack confidentiality and integrity. They can attack confidentiality as weak permissions allow them to access files they should not be able to access. Moreover, they can attack integrity as they might modify files that they should not be able to edit.
### 3. Access to Malicious Programs
- Trojan
- Ransomware
## C. Practical Example of OS Security
### 1. Basic Command
#### 1.1 `ssh`
- Introduction
Secure Shell (SSH) refers to a cryptographic network protocol used in secure communication between devices. SSH encrypts data using cryptographic algorithms, such as Advanced Encryption System (AES) and is often used when logging in remotely to a computer or server.
Set up remote connection.
- Usage
```shell
ssh USERNAME@10.10.247.243
```
- Tip
The first time we connect to a server over SSH, we will get a warning about the server’s authenticity and SSH key. We need to answer “Are you sure you want to continue connecting (yes/no)?” with yes.
 **When you are typing the SSH password, you won't see stars, dots, or any indicator on the screen that the password is being typed**.
#### 1.2 `whoami`
- Introduction
Check the current user.
- Usage
```shell
whoami
```
#### 1.3 `ls`
- Introduction
Short for *list*.  This command will s
how all the files in the current directory unless they are hidden.
#### 1.4 `cat`
- Introduction
Short for concatenate. Check content of file.
- Usage
```shell
cat FILENAME
```
#### 1.5 `history`
- Introduction
This command prints the commands used by the current user.
- Usage
```shell
history
```
### 2. Ways to switch user
- If you are **not** logged in as `sammie` or any other user, you can use `ssh johnny@10.10.247.243` and manually try one password after the next to see which password works for `johnny`.
- If you are logged in as `sammie` or any other user, you can use `su - johnny` and manually try one password after the next to see which password works for `johnny`.