# A. Cryptography Basics
## Introduction

By secure, we mean that no one can read or alter the exchanged data; furthermore, we can be confident that we are connecting with the real server. Thanks to cryptography, these requirements are satisfied.

## Importance of Cryptography

Cryptography's ultimate purpose is to ensure secure communication in the presence of adversaries. 

The term secure includes confidentiality and integrity of the communicated data. 

- When you log in to TryHackMe, your credentials are encrypted and sent to the server so that no one can retrieve them by snooping on your connection.
- When you connect over SSH, your SSH client and the server establish an encrypted tunnel so no one can eavesdrop on your session.
- When you conduct online banking, your browser checks the remote server’s certificate to confirm that you are communicating with your bank’s server and not an attacker’s.
- When you download a file, how do you check if it was downloaded correctly? Cryptography provides a solution through hash functions to confirm that your file is identical to the original one.

Cryptography is everywhere while you almost didn't see it.

When handling credit cards, the company must follow PCI DSS (Payment Card Industry Data Security Standard). If you check the [PCI DSS for Large Organizations](https://www.pcisecuritystandards.org/documents/PCI_DSS_for_Large_Organizations_v1.pdf), you will learn that the data should be encrypted both while being stored (at rest) and while being transmitted (in motion).

And handling medical records requires complying with their respective standards. 
- HIPAA, HITECH in USA
- GDPR in the EU
- DPA in the UK

These laws and regulations show that cryptography is a necessity that should be present yet usually be hidden from direct use access.

## Plaintext to Ciphertext

We begin with the plaintext that we want to encrypt.

Plaintext, which means anything readable like "hello", a cat photo, credit card information, or medical records, is what is waiting to be encrypted from a cryptography perspective.

The plaintext is passed through the encryption function along with a proper key.

The encryption function returns a ciphertext.

A cipher is an algorithm to convert a plaintext into a ciphertext. 

![[Pasted image 20250121152948.png]]

To recover the plaintext, we must pass the ciphertext along with the proper key via the decryption function, which would give us the original plaintext. This is shown in the illustration below.

![[Pasted image 20250121153809.png]]

New terms:
- **Plaintext** is the original, readable message or data before it’s encrypted. It can be a document, an image, a multimedia file, or any other binary data.
- **Ciphertext** is the scrambled, unreadable version of the message after encryption. Ideally, we cannot get any information about the original plaintext except its approximate size.
- **Cipher** is an algorithm or method to convert plaintext into ciphertext and back again. A cipher is usually developed by a mathematician.
- **Key** is a string of bits the cipher uses to encrypt or decrypt data. In general, the used cipher is public knowledge; however, the key must remain secret unless it is the public key in asymmetric encryption. We will visit asymmetric encryption in a later task.
- **Encryption** is the process of converting plaintext into ciphertext using a cipher and a key. Unlike the key, the choice of the cipher is disclosed.
- **Decryption** is the reverse process of encryption, converting ciphertext back into plaintext using a cipher and a key. Although the cipher would be public knowledge, recovering the plaintext without knowledge of the key should be impossible (infeasible).

## Historical Cipher

- Caesar Cipher
- The Vigenère cipher from the 16th century
- The Enigma machine from World War II
- The one-time pad from the Cold War

## Types of Encryption

Two main types of encryption are symmetric and asymmetric.

### Symmetric Encryption

Symmetric encryption, also known as symmetric cryptography or private key cryptography, uses the same key to encrypt and decrypt the data. 

![[Pasted image 20250121155701.png]]

Consider the simple case you want to send a password-protected document to your colleague. It is OK to send the encrypted document to he/she, but you maybe don't wanna send he/she the password, because anyone has the access to his/her mailbox, they will get the plaintext, and the data would be disclosed. 

Therefore, you need think of a different way, i.e., channel to share the password. Unless you find a secure, accessible channel, one solution would be to meet in person and communicate the password to them.

Examples of symmetric encryption:
- **DES** (Data Encryption Standard) was adopted as a standard in 1977 and uses a 56-bit key. With the advancement in computing power, in 1999, a DES key was successfully broken in less than 24 hours, motivating the shift to 3DES.
- **3DES** (Triple DES) is DES applied three times; consequently, the key size is 168 bits, though the effective security is 112 bits. 3DES was more of an ad-hoc solution when DES was no longer considered secure. 3DES was deprecated in 2019 and should be replaced by AES; however, it may still be found in some legacy systems.
- **AES** (Advanced Encryption Standard) was adopted as a standard in 2001. Its key size can be 128, 192, or 256 bits.

### Asymmetric Encryption

Asymmetric encryption, also known as asymmetric cryptography or public key cryptography, uses a pair of keys, one to encrypt and the other to decrypt.
We use public key to encrypt the data, and private key to decrypt the data.
Your private key needs to be kept private, hence the name.

![[Pasted image 20250121160905.png]]

Examples:
 - RSA 
 - Diffie-Hellman 
 - Elliptic Curve cryptography (ECC)

Asymmetric encryption tends to be slower, and many asymmetric encryption ciphers use larger keys than symmetric encryption. 
- RSA uses 2048-bit, 3072-bit, and 4096-bit keys.2048-bit is the recommended minimum key size.
- Diffie-Hellman also has a recommended minimum key size of 2048 bits but uses 3072-bit and 4096-bit keys for enhanced security.
- ECC can achieve equivalent security with shorter keys. For example, with a 256-bit key, ECC provides a level of security comparable to a 3072-bit RSA key.

Asymmetric encryption is based on a particular group of mathematical problems that are easy to compute in one direction but extremely difficult to reverse.
The security rely on a mathematical problem that would take a very long time, for example, millions of years, to solve using today's technology.

### New terms

- **Alice and Bob** are fictional characters commonly used in cryptography examples to represent two parties trying to communicate securely. 
- **Symmetric encryption** is a method in which the same key is used for both encryption and decryption. Consequently, this key must remain secure and never be disclosed to anyone except the intended party. 
- **Asymmetric encryption** is a method that uses two different keys: a public key for encryption and a private key for decryption.

## Basic Math

### XOR Operation

XOR, short for "exclusive OR", 

See the truth table:

| $A$ | $B$ | $A ⊕ B$ |
| --- | --- | ------- |
| 0   | 0   | 0       |
| 0   | 1   | 1       |
| 1   | 0   | 1       |
| 1   | 1   | 0       |

Properties:
Assume $A$ is a binary value

One key property is that applying XOR to a value with itself results in 0, and applying XOR to any value with 0 leaves it unchanged.
$$
\begin{align}
A \oplus A &= 0 \\
A \oplus 0 &= A
\end{align}
$$

Additionally, XOR is commutative and associative.
$$
\begin{align}
A \oplus B &= B \oplus A \\
(A \oplus B) \oplus C &= A \oplus (B \oplus C)
\end{align}
$$
Prove:
It is obvious that XOR is commutative.
And the table below proves that XOR is associative, as in any condition of $A$, $B$, and $C$, the $(A \oplus B) \oplus C$ and $A \oplus (B \oplus C)$ have the same value.

| $A$ | $B$ | $C$ | $(A \oplus B) \oplus C$ | $A \oplus (B \oplus C)$ |
| --- | --- | --- | ----------------------- | ----------------------- |
| 1   | 1   | 1   | 1                       | 1                       |
| 1   | 1   | 0   | 0                       | 0                       |
| 1   | 0   | 1   | 0                       | 0                       |
| 1   | 0   | 0   | 1                       | 1                       |
| 0   | 1   | 1   | 0                       | 0                       |
| 0   | 1   | 0   | 1                       | 1                       |
| 0   | 0   | 1   | 1                       | 1                       |
| 0   | 0   | 0   | 0                       | 0                       |

We will demonstrate how XOR can be used as a basic symmetric encryption algorithm.
Consider the binary values $P$ and $K$, where $P$ is the plaintext, and $K$ is the secret key. The ciphertext is $C = P \oplus K$.

If we know $C$ and $K$, we can recover the $P$ with $P = C \oplus K$. Because:
$$
\begin{align}
C \oplus K &= (P \oplus K) \oplus K \\
&= P \oplus (K \oplus K) \\
&= P \oplus 0 \\
&= P
\end{align}
$$

But what if we wanna process multiple bits, because in most scenarios, we almost didn't just send a binary bit, but more bits, and these bits can represent many things thanks to computer science?

So let's start define how two group of bits can perform XOR operation.

Set $P_n = p_1p_2...p_n$ ,and $p_i$ is a bit.
Also set $C_n = c_1c_2...c_n$, and $c_i$ is a bit.
Also set $K_n = k_1k_2...k_n$  , and $k_i$ is a bit.

Define $P_n \oplus C_n$ (just take each bit to execute XOR operation):
$$
\begin{align}
P_n \oplus C_n &= (p_1p_2...p_n) \oplus (c_1c_2...c_n) \\
&= (p_1 \oplus c_1)(p_2 \oplus c_2)...(p_n \oplus c_n)
\end{align}
$$

So now consider multiple bits encryption followed by the above pattern. Set the $P_n$ as plaintext, and $K_n$ as key.
$$
\begin{align}
C_n &= P_n \oplus K_n \\
&= (p_1p_2...p_n) \oplus (k_1k_2...k_n) \\
&= (p_1 \oplus k_1)(p_2 \oplus k_2)...(p_n \oplus k_n) \\
&= c_1c_2...c_n
\end{align}
$$

And if we have $C_n$ and $K_n$ , we can recover the $P_n$ .

$$
\begin{align}
C_n \oplus K_n &= (c_1c_2...c_n) \oplus (k_1k_2...k_n) \\
&= (c_1 \oplus k_1)(c_2 \oplus k_2)...(c_n \oplus k_n) \\
&= ((p_1 \oplus k_1) \oplus k_1)((p_2 \oplus k_2) \oplus k_2)...((p_n \oplus k_n) \oplus k_n) \\
&= (p_1 \oplus (k_1 \oplus k_1))(p_2 \oplus (k_2 \oplus k_2))...(p_n \oplus (k_n \oplus k_n)) \\
&= (p_1 \oplus 0)(p_2 \oplus 0)...(p_n \oplus 0) \\
&= p_1p_2...p_n \\
&= P_n
\end{align}
$$

So we get the $P_n$ .

Take each bit of the specific data, and apply XOR operation to these bits one by one, with the key. And once receive the key, take each bit again, and apply XOR operation to these bit one by one with the key.

Do you notice that? In this cipher, the length of plaintext, ciphertext and key are same, which maybe results in length attack.

### Modulo Operation

We call $X \bmod Y$ is the **remainder** of $X$ divided by $Y$. (or $X \% Y$)

In daily life, we often focuses on the result of division, but the remainder plays a significant role in cryptography.

You need to work with large numbers when solving some cryptography exercises. There are two choices:
- Python. It has a built-in type `int`, which would switch to larger types as needed.
- [WolframAlpha](https://www.wolframalpha.com/) online math calculator.

$$
\begin{align}
25 \bmod 5 &= 0 \\
23 \bmod 6 &= 5 \\
23 \bmod 7 &= 2
\end{align}
$$

An important thing to remember about modulo is that it's not reversible. If we are given equation $x \bmod 5 = 4$, infinite values of $x$ would satisfy this equation.

The modulo operation always returns a non-negative result less than the divisor. This means that for any integer $a$ and positive integer $n$, the result of $a \bmod n$ will always be in the range 0 to $n - 1$.

