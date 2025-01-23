# C. Hashing Basics

## Introduction

The technique hashing can be used to check a file's integrity. If two file's hash values are the same, we can say with very high certainty that the two files are identical.

A hash value is a fixed-size string or characters that is computed by a hash function.
A hash function takes an input of an arbitrary size and returns an output of fixed length, i.e., a hash value.

Note about terminology: We favoured the terms hash function and hash value. However, we occasionally use the word **_hash_** as a _verb_ to mean _calculate_ the hash value; moreover, we occasionally use the word **_hash_** by itself as a _noun_ to refer to the hash value.

## Hash Functions

There is no key, and it's meant to be impossible (or computationally impractical) to go from the output back to the input.

A hash function takes some input data of any size and creates a summary or digest of that data. The output has a fixed size. 

A good hashing algorithms:
- relatively fast to compute and prohibitively slow to reverse.
- any slight change in the input data, even a single bit, should cause a significant in the output.

Check the `U` and `T`, with `hexdump`, `md5sum`, `sha1sum` and `sha256sum`. To see how different the hash values they are.

![[Pasted image 20250123144445.png]]

The output of a hash function is typically raw bytes, which are then encoded. Common encodings are base64 or hexadecimal. `md5sum`, `sha1sum`, `sha256sum` and `sha512sum` produce their outputs in hexadecimal format. 
Remember that hexadecimal format prints each raw bytes as two hexadecimal digits.


Hashing helps protect data's integrity and ensure password confidentiality.
As for integrity, as we prepare our message, we calculate its hash value and encrypt it with our private key to form a signature. And then send message and signature to others, to prove we are whom we say we are. 
As for confidentiality, when you register an account on a website, the server shouldn't record your raw password, instead, it will hash your raw password and store it in the database. When you try to log into a website, the server will check if the hash value of your password equals to the stored hash value. If they are the same, you will be passed in, since the same input for a hash function will produce the same output. 

You may wondered what if the different input for a hash function will get the same output? That means two different password, will get the same hash value, and on the server side, the server will let them in. It may causes some security problems.
So a good hash function should avoid this problem as much as possible.


A hash collision is when two different inputs give the same output, because there is a pigeonhole effect.

Since the input is infinite types, but output is fixed types, so there must be that some different input will inevitably get the same output.

A good hash function should down the probability of collision.

And MD5 and SHA1 have been proved that they have the property of collision, and are now considered insecure. 

MD5 collision examples:  [MD5 Collision Demo](https://www.mscs.dal.ca/~selinger/md5collision/) page
Details of SHA1 collision attack:  [Shattered](https://shattered.io/)

## Insecure Password Storage for Authentication 

### Store Password in Plaintext

the `rockyou.txt` dictionary is originated from the data breach of a company RockYou, which developed social media applications and widgets, store password in plaintext.

### Using an Insecure Encryption Algorithm

Adobe put the password hint in the plaintext, sometimes containing the password itself.

### Using an Insecure Hash Function

LinkedIn used an insecure hashing algorithm, the SHA-1, to store user passwords, without salting.

Password salting refers to adding a salt, i.e., a random value, to the password before it is hashed.

## Using Hashing for Secure Password Storage

### Using Hashing to Store Passwords

As a good security practice, the web server will store hash values of password instead of plaintext. 

But what if two user's password are same? Then server will get the same hash value, and store them into database if it doesn't salt them. Consider the collision and salting, when we get the same hash values from a database, we can confirm there is high certainty that they are the same plaintext before they are hashed.

A **Rainbow Table** is a lookup table of hashes to plaintexts, so you can quickly find out what password a user had just from the hash. (if they aren't salted)

|Hash|Password|
|---|---|
|02c75fb22c75b23dc963c7eb91a062cc|zxcvbnm|
|b0baee9d279d34fa1dfd71aadb908c3f|11111|
|c44a471bd78cc6c2fea32b9fe028d30a|asdfghjkl|
|d0199f51d2728db6011945145a1b607a|basketball|
|dcddb75469b4b4875094e14561e573d8|000000|
|e10adc3949ba59abbe56e057f20f883e|123456|
|e19d5cd5af0378da05f63f891c7467af|abcd1234|
|e99a18c428cb38d5f260853678922e03|abc123|
|fcea920f7412b5da7be0cf42b8c93759|1234567|
|4c5923b6a6fac7b7355f53bfe2b8f8c1|inS3CyourP4$$|

### Protecting Against Rainbow Tables

To protect against rainbow tables, we add a salt to the passwords. 
The salt is a randomly generated value stored in the database and should be unique to each user. 

In theory, you could use the same salt for all users, but duplicate passwords would still have the same hash and a rainbow table could still be created for passwords with that salt.

The salt is added to either the start or the end of the password before it’s hashed, and this means that every user will have a different password hash even if they have the same password. Hash functions like `Bcrypt` and `Scrypt` handle this automatically. Salts don’t need to be kept private.

### Example of Security Storing Passwords

1. We select a secure hashing function, such as Argon2, Scrypt, Bcrypt, or PBKDF2.
2. We add a unique salt to the password, such as `Y4UV*^(=go_!`
3. Concatenate the password with the unique salt. For example, if the password is `AL4RMc10k`, the result string would be `AL4RMc10kY4UV*^(=go_!`
4. Calculate the hash value of the combined password and salt. In this example, using the chosen algorithm, you need to calculate the hash value of `AL4RMc10kY4UV*^(=go_!`.
5. Store the hash value and the unique salt used (`Y4UV*^(=go_!`).

### Using Encryption to Store Passwords

Why don't we encrypt passwords instead of all these cumbersome steps?
It's because even if we encrypt the passwords, we still need a key. If the key is disclosed, the password will become insecure. 

Maybe you can invent a symmetric cipher for your own, and then even if the attacker find the key, they cannot decrypt it by using common symmetric cipher 🤔?

## Recognizing Password Hashes

If we start with a hash, how can we recognize its type, eventually crack it, and recover the original password ?

Automated hash recognition can helps us, for hashes that have a prefix. But it often get these hash types mixed up, highlighting the importance of teaching yourself.

### Linux Passwords

On Linux, password hashes are stored in `/etc/shadow`, which is normally only readable by root. They used to be stored in `/etc/passwd`, which was readable by everyone.

`/etc/shadow`
contains the password information, whose each line is divided into 9 fields. The first two fields are the login name and the encrypted password. More info can be found by `man 5 shadow`.

The structure of the encrypted password:
```
$prefix$options$salt$hash
$y$j9T$pNL0qt77LnYoBWCnA/mIp0$kiM3c7EdQAKDZgtGsm2TN4oqLZpLqY6UAuKf7cvH.u8
```
The prefix specifies the hashing algorithm used to generate the hash.

Here's a table about the prefix: (more can be found by `man 5 crypt`)

| Prefix                         | Algorithm                                                                                                                                                                                        |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `$y$`                          | yescrypt is a scalable hashing scheme and is the default and recommended choice in new systems                                                                                                   |
| `$gy$`                         | gost-yescrypt uses the GOST R 34.11-2012 hash function and the yescrypt hashing method                                                                                                           |
| `$7$`                          | scrypt is a password-based key derivation function                                                                                                                                               |
| `$2b$`, `$2y$`, `$2a$`, `$2x$` | bcrypt is a hash based on the Blowfish block cipher originally developed for OpenBSD but supported on a recent version of FreeBSD, NetBSD, Solaris 10 and newer, and several Linux distributions |
| `$6$`                          | sha512crypt is a hash based on SHA-2 with 512-bit output originally developed for GNU libc and commonly used on (older) Linux systems                                                            |
| `$md5`                         | SunMD5 is a hash based on the MD5 algorithm originally developed for Solaris                                                                                                                     |
| `$1$`                          | md5crypt is a hash based on the MD5 algorithm originally developed for FreeBSD                                                                                                                   |

### Modern Linux Example

```
$prefix$options$salt$hash
$y$j9T$pNL0qt77LnYoBWCnA/mIp0$kiM3c7EdQAKDZgtGsm2TN4oqLZpLqY6UAuKf7cvH.u8
```

- `y` indicates the hash algorithm used, **yescrypt**
- `j9T` is a parameter passed to the algorithm.
- `pNL0qt77LnYoBWCnA/mIp0` is the salt used.
- `kiM3c7EdQAKDZgtGsm2TN4oqLZpLqY6UAuKf7cvH.u8` is the hash value.

### MS Windows Passwords

MS Windows passwords are hashed using NTLM, a variant of MD4. They’re visually identical to MD4 and MD5 hashes, so it’s very important to use context to determine the hash type.

On MS Windows, password hashes are stored in the SAM (Security Accounts Manager). MS Windows tries to prevent normal users from dumping them, but tools like `mimikatz` exist to circumvent MS Windows security. Notably, the hashes found there are split into NT hashes and LM hashes.

Find more hash formats:  [Hashcat Example Hashes](https://hashcat.net/wiki/doku.php?id=example_hashes) 

For other hash types, you’ll typically need to check the length or encoding or even conduct some research into the application that generated them. Never underestimate the power of research.

## Password Cracking

When you want to crack a hash without salting, the rainbow table attack is a good choice. But what if there's a salt involved?

You can also create a rainbow table about a specific salt and a specific hash function, but it's unnecessary, since the better choice is just brute force it with a dictionary. Maintain a rainbow table needs some costs.

You can crack the hashes by hashing many different inputs (such as `rockyou.txt`), potentially adding the salt if there is one and comparing it to the target hash. Once it matches, you know what the password was.

And here are some tools.
- `hashcat`
- `Jhon the Ripper`

### Cracking Passwords with GPUs

You can use GPUs to crack the hash, as they are very good at some mathematical calculations involved in hash functions. 

But some algorithms, such as Bcrypt, are designed so that hashing on a GPU does not provide any speed improvement over using a CPU. 

### Cracking on VMs? 

It's not recommended, since it will degrade the performance of tools.

`hashcat` provide GPU acceleration, and MS Windows are available, you can run it from PowerShell.

`Jhon the Ripper` uses CPU by default. If you want to get better performance, please run it on your host, not VMs.

### Time to Crack Some Hashes

- online rainbow tables

```sh
hashcat -m <hash_type> -a <attack_mode> hashfile wordlist
```

- `-m <hash_type>`. `-m 1000` is for NTLM. `man hashcat` or [example page](https://hashcat.net/wiki/doku.php?id=example_hashes).
- `-a <attack_mode>`. `-a 0` is for straight, i.e., trying one password from the wordlist after the other.
- `hashfile`
- `worlist`. `rockyou.txt`

Offline:
```sh
hashcat -m 1400 -a 0 hash2.txt /usr/share/wordlists/rockyou.txt
```
Check with `man hashcat` and [example page](https://hashcat.net/wiki/doku.php?id=example_hashes).