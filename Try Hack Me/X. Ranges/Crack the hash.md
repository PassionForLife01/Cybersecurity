# Crack the hash

## Level 1

```sh
48bb6e862e54f2a795ffc4e541caed4d

echo 48bb6e862e54f2a795ffc4e541caed4d | hash-identifier 
# md5

hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

```sh
CBFDAC6008F9CAB4083784CBD1874F76618D2A97

python hash-id.py
# sha-1

hashcat -m 100 -a 0 .\hashes\hash1.txt .\rockyou.txt
```

```
1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt hash3.txt
```

```
$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

https://hashes.com/en/decrypt/hash
```

```
279412f945939ba78ce0758d3fd83daa

https://crackstation.net/
```

## Level 2

Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
https://crackstation.net/

Hash: 1DFECA0C002AE40B8619ECF94819CC1B
https://hashes.com/en/decrypt/hash

Hash: \$6\$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Salt: aReallyHardSalt

sha-512 crypt
https://hashes.com/en/decrypt/hash

Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Salt: tryhackme

In `hmac.txt`
```
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme
```
Follow the [example](https://hashcat.net/wiki/doku.php?id=example_hashes) pattern


```sh
hashcat -m 160 -a 0 hmac.txt /usr/share/wordlists/rockyou.txt
```


