# Cracking

## Hashcat

```sh
hashcat -m <hash_type> -a <attack_mode> hashfile wordlist
```

`man hashcat`

[example page](https://hashcat.net/wiki/doku.php?id=example_hashes)

```sh
hashcat -m 1400 -a 0 hash2.txt /usr/share/wordlists/rockyou.txt
```

## John
### Start
```sh
john [options] [file path]

john --show hash.txt # if you forgot the result
```

### Installation
It is installed on Kali Linux.
For other Linux distribution, follow this guide [official installation guide](https://github.com/openwall/john/blob/bleeding-jumbo/doc/INSTALL)

For windows, just download either 64-bit systems [here](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip) or for 32-bit systems [here](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win32.zip). The tools are in `run` directory.
Remember add the `run` directory into your environment variable `PATH`.

### Wordlists
online:  [SecLists](https://github.com/danielmiessler/SecLists) 
offline (on kali linux): `/usr/share/wordlists`

`rockyou.txt`. You can get the `rockyou.txt` from the [SecLists](https://github.com/danielmiessler/SecLists) repository under the `/Passwords/Leaked-Databases` subsection.

### Automatic cracking
```sh
john --wordlist=/usr/share/wordlist/rockyou.txt hash.txt
```

### Identify hashes
online: [this site](https://hashes.com/en/tools/hash_identifier)
offline: GitLab [page](https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py) to get `hash-id.py`. (built-in on kali linux)

```sh
echo [hash] | hash-identifier
```

### Get the format
```sh
john --list=formats
john --list=formats | grep -iF "md5"
```

### Format-Specific Cracking 
(you can use `hashcat` as an alternative):
```sh
john --format=[format] --wordlist=[path to wordlist] [path to file]

john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

### Cracking Windows Authentication Hashes
```sh
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
```

How can we get NThash/NTLM hashes? 
- use a tool like `mimikatz`. [mimikatz](https://github.com/ParrotSec/mimikatz) 
- use the Active Directory database: `NTDS.dit`. 

Cracking `/etc/shadow` Hashes:
```sh
unshadow local_passwd local_shadow > unshadowed.txt

john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```

### Single Crack

In `hash.txt`:
From `1efee03cdcb96d90ad48ccc7b8666033`
To `mike:1efee03cdcb96d90ad48ccc7b8666033`

```sh
john --single --format=raw-sha256 hash.txt
```

### Custom Rules

Configuration:
```sh
/opt/john/john.conf  # or
/etc/john/john.conf  
```

In `john.conf`
```
[List.Rules:PoloPassword]
cAz"[0-9][!£$%@]"
```

Modifiers:
- `Az`: Takes the word and appends it with the characters you define
- `A0`: Takes the word and prepends it with the characters you define
- `c`: Capitalizes the character positionally.

Character Sets (follow the modifier patterns inside double quotes `" "`) :
- `[0-9]`: Will include numbers 0-9  
- `[0]`: Will include only the number 0
- `[A-z]`: Will include both upper and lowercase  
- `[A-Z]`: Will include only uppercase letters
- `[a-z]`: Will include only lowercase letters

- `[a]`: Will include only `a`
- `[!£$%@]`: Will include the symbols `!`, `£`, `$`, `%`, and `@`

Usage:
```sh
john --wordlist=/usr/share/wordlist/rockyou.txt --rule=PoloPassword hash.txt
```

Detailed Documentation:  [here](https://www.openwall.com/john/doc/RULES.shtml)
To see rules created by jumbo john, go to around line 678 in `john.conf`.

### Cracking Zip Files

```sh
zip2john [options] [zip file] > [output file]

zip2john zipfile.zip > zip_hash.txt
```

```sh
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
```

### Cracking RAR Archives

```sh
rar2john [rar file] > [output file]

/opt/john/rar2john rarfile.rar > rar_hash.txt
```

```
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
```

```sh
unrar x secure.rar
unrar l secure.rar
```

### Cracking SSH Keys

```sh
ssh2john [id_rsa private key file] > [output file]

/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt
```

```sh
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```

