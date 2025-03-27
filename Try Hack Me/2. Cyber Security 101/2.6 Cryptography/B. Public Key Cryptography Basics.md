# B. Public Key Cryptography Basics
## Intro
- authentication. you are confirming the identity of who you are talking with.
- authenticity. you verify that the message genuinely comes from a specific sender. 
- integrity. ensuring that the data has not been altered or tampered with. 
- confidentiality. only the authorized parties can access the data.

When you are communicating with your business partner over an online messaging platform, you need to be sure of the following:

- **Authentication**: You want to be sure you communicate with the right person, not someone else pretending.
- **Authenticity**: You can verify that the information comes from the claimed source.
- **Integrity**: You must ensure that no one changes the data you exchange.
- **Confidentiality**: You want to prevent an unauthorized party from eavesdropping on your conversations.

Cryptography can provide solutions to above requirements.
Private key cryptography, i.e., symmetric cryptography, mainly protects confidentiality.
Public key cryptography, i.e., asymmetric cryptography, plays a significant role in authentication, authenticity, and integrity.

In this room, we will cover various asymmetric cryptosystems and applications that use them, including:
- RSA
- Diffie-Hellman
- SSH
- SSL/TLS Certificates
- PGP and GPG

## Common Use

- Exchanging keys for symmetric encryption.

Asymmetric encryption is relatively slow compared to symmetric encryption. Therefore, we rely on asymmetric encryption to negotiate and agree on encryption ciphers and keys.

How do we agree on a key with the server without transmitting the key for people snooping to see?

Imagine that you have a secret code that you wanna share it with your friend.
Then you ask your friend for a lock that only him can lock it.
You put your secret code into the box, locking it, and send it to your friend.
Then your friend unlock it.
And if anyone intercept the box, they cannot open it.

In this metaphor, the secret code represents a symmetric encryption and key, the lock represents the server's public key, and the key represents the server's private key.

| Analogy     | Cryptographic System                |
| ----------- | ----------------------------------- |
| Secret Code | Symmetric Encryption Cipher and Key |
| Lock        | Public Key                          |
| Lock’s Key  | Private Key                         |

In real world, we need authentication and authenticity, which means that we need more cryptography to verify that the person you're talking to is who they say they are. 

## RSA

### Math Principle

RSA is based on the mathematically difficult problem of factoring large number. 
Multiplying two large prime numbers is a straightforward operation; however, finding the factors of a huge number takes much more computing power.

$$
\begin{align}
113 \times 127 &= 14351 \\
982451653031 \times 169743212279 &= 166764499494295486767649
\end{align}
$$

Computer cannot factorize a number with more than 600 digits now. And we would agree that the multiplication of the two huge prime factors, each around 300 digits, would be easier than the factorization of their product.

[Fermat](https://www.britannica.com/science/number-theory/Pierre-de-Fermat)

### Numeric Example

As the image shown below, the public key is known to all correspondents and is used for encryption, while the private key is protected and used for decryption.

![[Pasted image 20250122140418.png]]

Let's see how RSA works:
1. Bob choose two prime numbers: $p = 157$ and $q = 199$. He calculates $n = p \times q = 31243$
2. With $\phi(n) = n - p - q + 1 = 31243 - 157 - 199 + 1 = 30888$, Bob selects $e = 163$ such that $e$ is relatively prime to $\phi(n)$; moreover, he selects $d = 379$, where $e \times d = 1 \bmod \phi(n)$, i.e., $e \times d = 61777$ and $61777 \bmod 30888 = 1$. The public key is $(n, e)$, i.e., $(31243, 163)$ and the private key if $(n, d)$, i.e., $(31243, 379)$. 
3. Let's say that the value they want to encrypt is $x = 13$, then Alice would calculate $y = x^e \bmod n = 13^{163} \bmod 31243 = 16341$ 
4. Bob will decrypt it by calculating $x = y^d \bmod n = 16341^{379} \bmod 31243 = 13$. This way, Bob recovers the value that Alice sent. 

In real world, $p$ and $q$ would be at least 300-digit prime number each.

Let's give it a quick review:
1. $Choose \space two \space primes \space p \space and \space q$ 
2. $n = p \times q$
3. $Calculate \space \phi(n) = n - p - q + 1$
4. $Choose \space e, \space where \space 1 < e < \phi(n)\ and\ prime\ to\ \phi(n)$
5. $Choose\ d,such\ that\ e\ \times d = 1 \bmod n$ 
6. $Generate \space public \space key \space (n,e)$ 
7. $Generate \space private \space key \space (n,d)$
8. $Encrypt \space plaintext \space m \space with \space c = m^e \bmod n$
9. $Decrypt \space ciphertext \space c \space with \space m = c^d \bmod n$ 

Notice that if you encrypt a message with private key, and you can also decrypt it with public key.
$$
\begin{align}
c &= m^e \bmod n \\
m &= c^d \bmod n \\
\\
s &= m^d \bmod n \\
m &= s^e \bmod n
\end{align}
$$
### RSA in CTFs

There are some excellent tools for defeating RSA challenges in CTFs.
- [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool)
- [rsatool](https://github.com/ius/rsatool)

The main variables for RSA in CTFs: $p,\ q,\ n,\ e,\ d,\ m$ and  $c$  
- $p$ and $q$ are large prime numbers.
- $n$ is the product of $p$ and $q$ 
- The public key is $n$ and $e$ 
- The private key is $n$ and $d$ 
- The plaintext is $m$ and ciphertext is $c$ 

CTF gives you with a set of these values, you should try to break the encryption and decrypt a message to retrieve the flag.

## Diffie-Hellman Key Exchange

Alice has secret $A$, and Bob has secret $B$. They want to generate a shared key for symmetric cryptography in an insecure channel, and don't wanna use asymmetric cryptography for the key exchange. This is where the Diffie-Hellman Key Exchange comes in.

They have some public materials; let's call this $C$.

We need to make assumptions:
- Whenever we combine secrets, they're practically impossible to separate.
- the order in which they're combined doesn't matter.
- Alice and Bob will combine their secrets with the common material to form $AC$ and $BC$.
- They will then send these to each other and combine the received part with their secret to create two identical keys, both ABC. 
- Now, they can use this key to communicate.

Let's investigate the exact process.

1. Choose the public variables: a large prime number $p$ and a generator $g$, where $0 < g < p$. In this case $p = 29$, $g = 3$.
2. Alice choose a private integer $a$, Bob choose a private integer $b$.  In this case, $a = 13$, $b = 15$,
3. Alice calculates $A = g^a \bmod p$, Bob calculates $B = g^b \bmod p$. And they send $A$ or $B$ to other. In this case, $A = 3^{13} \bmod 29 = 19$, $B = 3^{15} \bmod 29 = 26$ 
4. Once receive the $A$ or $B$. Alice calculates $B^a \bmod p$, Bob calculates $A^b \bmod p$ , they get the same $key = g^{ab} \bmod p$. In this case, $B^a \bmod p = 26^{13} \bmod 29 = 10$, $A^b \bmod p = 19^{15} \bmod 29 = 10$, and $g^{ab} \bmod p = 3^{13 \times 15} \bmod 29 = 10$ .

![[Pasted image 20250122155255.png]]

Short summary:
1. $Set\ p, g, where\ 0 < g < p\ and\ p\ is\ a\ large\ prime\ number$
2. $Set\ a, b$
3. $A = g^a \bmod p$, $B = g^b \bmod p$
4. $key = B^a \bmod p = A^b \bmod p = g^{ab} \bmod p$ 

In real life, we would consider much bigger numbers.

Diffie-Hellman Key Exchange is often used alongside RSA public key cryptography. 
RSA helps prove the identity of the person you're talking to via digital signing, as you can confirm based on their public key.

## SSH

### Authenticating the Server

![[Pasted image 20250122163511.png]]

In the above interaction, the SSH client confirms whether we recognize the server’s public key fingerprint. The ED25519 is the public-key algorithm used for digital signature generation and verification in this example. Our SSH client didn’t recognise this key and is asking us to confirm whether we want to continue with the connection. This warning is because a man-in-the-middle attack is probable; a malicious server might have intercepted the connection and replied, pretending to be the target server.

So this public key fingerprint should be distinct from other server, and if you wanna keep secure, you need find documentation or information about the right server like its public key fingerprint.

Once you answer with "yes", the SSH client will record this public key signature for this host as you confirm this might or must be the right server. In the future, you won't get this prompt except for server change it public key.

### Authenticating the Client

The most common way that authenticates the server is by checking the username and password, but they can be brute forced if with a weak credentials. 

At some point, one will surely hit a machine with SSH configured with key authentication instead. This authentication uses public and private keys to prove the client is a valid and authorized user on the server. By default, SSH keys are RSA keys. You can choose which algorithm to generate and add a passphrase to encrypt the SSH key.

![[Pasted image 20250122165245.png]]

The following is just for your information. At this stage, we recommend that you recognize their names only.

- **DSA (Digital Signature Algorithm)** is a public-key cryptography algorithm specifically designed for digital signatures.
- **ECDSA (Elliptic Curve Digital Signature Algorithm)** is a variant of DSA that uses elliptic curve cryptography to provide smaller key sizes for equivalent security.
- **ECDSA-SK (ECDSA with Security Key)** is an extension of ECDSA. It incorporates hardware-based security keys for enhanced private key protection.
- **Ed25519** is a public-key signature system using EdDSA (Edwards-curve Digital Signature Algorithm) with **Curve25519**.
- **Ed25519-SK (Ed25519 with Security Key)** is a variant of Ed25519. Similar to ECDSA-SK, it uses a hardware-based security key for improved private key protection.

So let's generate a key pair, for ed25519, which is a algorithm dedicated for digital signature generation and verification, with default file path and no passphrase.

```sh
user@ip-10-10-107-158:~$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519): 
Created directory '/home/user/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:zxSk7NYqsgBnIeRqNt/frRy7E7tW5evRCC72l8AQNyQ user@ip-10-10-107-158
The key's randomart image is:
+--[ED25519 256]--+
| .       Eo.     |
|o      . +.o     |
|...     o + .    |
|.. .   . o . .   |
|o+o     S =.o    |
|o+o .  . *.+..o  |
|  ..... .+*..oo. |
|   . o..++*  +.  |
|    .  ..B=oo.   |
+----[SHA256]-----+
```

And notice that we don't use the passphrase. If you set it, the passphrase will be the key of the private key, i.e., it encrypt the private key in a symmetric cipher system as the key. 

And let's see the public key and private key.
```sh
user@ip-10-10-107-158:~$ cat /home/user/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICTh7iPS+2pCbgfW8qLtuquOtrEVE4D/lvaoz8f3Ilf6 user@ip-10-10-107-158
user@ip-10-10-107-158:~$ cat /home/user/.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACAk4e4j0vtqQm4H1vKi7bqrjraxFR
-----END OPENSSH PRIVATE KEY-----
```

If we use `-t rsa`, the resulting keys would have been much longer.

### SSH Private Keys

Never share your private key with others.

And it's very important to use a complex passphrase, because with `John the Ripper`, we can attack an encrypted SSH key to attempt to find the passphrase.

When the machine enable key authentication by: 
```sh
cat /etc/ssh/sshd_config | grep -i "pubkeyauthentication"
PubkeyAuthentication yes
```
We can put our public key on the target machine's `~/.ssh/authorized_keys` file.
And `chmod 600 privateKeyFileName`, then `ssh -i privateKeyFileName user@host`.

```sh
echo "yourPuglicKey" >> ~/.ssh/authorized_keys # on target machine
chmod 600 privateKeyFileName # on your machine
ssh -i privateKeyFileName user@host # on your machine
```

A formal way is by `ssh-copy-id`, which need the username and password at first time.
```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host # on your machine
```
## Digital Signature and Certificates
### Digital Signature
Digital signature provide a way to verify the authenticity and integrity of a digital message or document. 

Using asymmetric cryptography, you produce a signature with your private key, which can be verified with your public key. 

In many modern countries, digital and physical signatures have the same legal value.

The simplest form of digital signature is encrypting the document with your private key, and if someone wants to verify this signature, they would decrypt it with your public key and check if the files match.
![[Pasted image 20250123112312.png]]

Why it proves a document's integrity. Imagine that Bob declare the hash function to the public. Then, Bob hash his document to get a hash value, and encrypt it with his private key. Bob sends his document and encrypted hash value to Alice, and Alice decrypt it with public key and check the hash of document with the decrypted hash value. If they are the same, then this proves the document's integrity.

Because Alice will perform a decryption operation, even though some attackers can hash the malicious document, they cannot encrypt the hash value with private key.
If they choose a random key to encrypt the hash value, the decrypted hash value would be different from the hash value of the document, since they are not the right key pair.

RSA can support this feature.
### Certificate

How does your web browser know that the server you're talking to is the real `tryhackme.com` ?

The answer lies in certificates. The web server has a certificate that says it is the real `tryhackme.com`. 

The certificates have a chain of trust, starting with a root CA (certificate authority). From install time, your device, operating system, and web browser automatically trust various root CAs. 
Certificates are trusted only when the Root CAs say they trust the organization that signed them. 

In a way, it is a chain; for example, the certificate is signed by an organization, the organization is trusted by a CA, and the CA is trusted by your browser. Therefore, your browser trusts the certificate. 

In general, there are long chains of trust. You can take a look at the certificate authorities trusted by Mozilla Firefox [here](https://wiki.mozilla.org/CA/Included_Certificates) and by Google Chrome [here](https://chromium.googlesource.com/chromium/src/+/main/net/data/ssl/chrome_root_store/root_store.md).

Let’s say you have a website and want to use HTTPS. This step requires having a TLS certificate. You can get one from the various certificate authorities for an annual fee. Furthermore, you can get your own TLS certificates for domains you own using [Let's Encrypt](https://letsencrypt.org/) for free. If you run a website, it’s worth setting up and switching to HTTPS, as any modern website would do.

## PGP and GPG

### Intro
PGP stands for Pretty Good Privacy. It's a software that implements encryption for encrypting files, performing digital signing, and more.

[GnuPG or GPG](https://gnupg.org/) is an open-source implementation of the OpenPGP standard. GPG is commonly used in email to protect the confidentiality of the email message. Furthermore, it can be used to sign an email message and confirm its integrity.

Below is an example of generating GPG. 
![[Pasted image 20250123120820.png]]

We need GPG to decrypt files in CTFs. With PGP/GPG, private keys can be protected with passphrases in a similar way that we protect SSH private keys.

If the private keys are protected, we can crack it using `John the Ripper` and `gpg2john`. 
The man page for GPG can be found online [here](https://www.gnupg.org/gph/de/manual/r1023.html).

### Practical Example

Now that you have your GPG key pair, you can share the public key with your contacts. Whenever your contacts want to communicate securely, they encrypt their messages to you using your public key. 
To decrypt the message, you will have to use your private key. Due to the importance of the GPG keys, it is vital that you keep a backup copy in a secure location.

Let’s say you got a new computer. All you need to do is import your key, and you can start decrypting your received messages again:

- You would use `gpg --import backup.key` to import your key from `backup.key`
- To decrypt your messages, you need to issue `gpg --decrypt confidential_message.gpg`

```sh
gpg --import backup.key
gpg --decrypt confidential_message.gpg
```

## Conclusion

- **Cryptography** is the science of securing communication and data using codes and ciphers.
- **Cryptanalysis** is the study of methods to break or bypass cryptographic security systems **without knowing the key**.
- **Brute-Force Attack** is an attack method that involves trying **every possible key or password** to decrypt a message.
- **Dictionary Attack** is an attack method where the attacker tries **dictionary words or combinations of them**.

