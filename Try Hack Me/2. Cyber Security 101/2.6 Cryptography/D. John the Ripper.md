# D. John the Ripper

## Basic Terms

- **P (Polynomial Time)**: Class P covers the problems whose solution can be found in polynomial time. Consider sorting a list in increasing order. The longer the list, the longer it would take to sort; however, the increase in time is not exponential.
- **NP (Non-deterministic Polynomial Time)**: Problems in the class NP are those for which a given solution can be checked quickly, even though finding the solution itself might be hard. In fact, we don’t know if there is a fast algorithm to find the solution in the first place.

This room will focus on the most popular extended version of John the Ripper, **Jumbo John**.

## Set Up Your System
### Installation
It is installed on kali Linux and AttackBox. 

But on other Linux Distributions, if you want to install the Jumbo John version of `john`, you need to consider building from the source to access all the tools available via Jumbo John. 
Follow this guide [official installation guide](https://github.com/openwall/john/blob/bleeding-jumbo/doc/INSTALL)

On windows, just download either 64-bit systems [here](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip) or for 32-bit systems [here](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win32.zip). Remember add the path into your environment variable `Path`.

### Wordlists

online:  [SecLists](https://github.com/danielmiessler/SecLists) 
offline (on kali linux): `/usr/share/wordlists`

the infamous wordlist is `rockyou.txt`. You can get the wordlist from the [SecLists](https://github.com/danielmiessler/SecLists) repository under the `/Passwords/Leaked-Databases` subsection.

```sh
tar xzvf rockyou.tar.gz
```

## Cracking Basic Hashes

### Syntax

```sh
john [options] [file path]
```

- `john`
- `[options]`: Specifies the options you want to use
- `[file path]`: 

### Automatic Cracking

`john` can try to identify the type of a hash, and try to crack it by selecting appropriate rules and formats to crack it for you. 

It is maybe unreliable, but it can works when you cannot identify the hash's type.

```sh
john --wordlist=[path/to/wordlist] [path/to/file]
john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

### Identifying Hashes

The automatic mode of `john` usually perform poorly. We need to identify the type of a hash, and set the specific mode for `john`.

online: [this site](https://hashes.com/en/tools/hash_identifier)
offline: GitLab [page](https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py) to get `hash-id.py`
```sh
wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
```

### Format-Specific Cracking

```sh
john --format=[format] --wordlist=[path to wordlist] [path to file]

john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

To get the john's formats by
```sh
john --list=formats
john --list=formats | grep -iF "md5"
```

## Cracking Windows Authentication Hashes

Authentication hashes are the hashed versions of passwords stored by operating systems. 

As for Windows, NThash is the hash format modern Windows operating system machines use to store user and service password. It's also commonly referred to as NTLM, which references the previous version of Windows format for hashing passwords known as LM, thus NT/LM.

In Windows, SAM (Security Account Manager) is used to store user account info, including usernames and passwords.

How can we get NThash/NTLM hashes? 
- use a tool like `mimikatz`. [mimikatz](https://github.com/ParrotSec/mimikatz) 
- use the Active Directory database: `NTDS.dit`. 

Get the NTLM (failed)

Crack
```sh
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
```

## Cracking `/etc/shadow` Hashes

We need adjust the format of data that needs to be put in `john`.
We can do this with a tool called `unshadow`.

### Unshadowing

```sh
unshadow [path to passwd] [path to shadow]
```

- `[path to passwd]`: The file that contains the copy of the `/etc/passwd` file you’ve taken from the target machine
- `[path to shadow]`: The file that contains the copy of the `/etc/shadow` file you’ve taken from the target machine

```sh
unshadow local_passwd local_shadow > unshadowed.txt
```

What the `unshadow` do is just replace the blank password filed in `/etc/passwd` with the hash of `/etc/shadow`.

### Cracking

We should not need to specify a mode here as we have made the input specifically for John; however, in some cases, you will need to specify the format as we have done previously using: `--format=sha512crypt`

```sh
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```

If you crack a hash successfully, it won't be cracked again to save the resource.
If you forget about it, and wanna see it again, please using:
```sh
john --show unshadowed.txt
```

## Single Crack

`john` has a magic code called Single Crack, which means it uses only the info provided in the username to try and work out possible passwords heuristically by slightly changing the letters and numbers contained within the username.

### Word Mangling

To see an example:

Consider the username "Markus".
Some possible passwords could be:

- Markus1, Markus2, Markus3 (etc.)
- MArkus, MARkus, MARKus (etc.)
- Markus!, Markus$, Markus* (etc.)

`john` can uses a set of rules called "mangling rules", which define how it can mutate the word it started with to generate a wordlist based on relevant factors for the target you're trying to crack.

### GECOS

GECOS, stands for General Electric Comprehensive Operating System. 
When it comes to the GECOS field of `/etc/passdow`, we say the fifth part in a line which is divided by colon `:`, is that field.
It stores general information about the user, such as the user’s full name, office number, and telephone number, among other things.

We can take these info to `john` to effectively crack hash by single crack mode.

### Using Single Crack Mode

```sh
john --single --format=[format] [path to file]

john --single --format=raw-sha256 hashes.txt
```

We need adjust the structure of input when we use single crack mode to specify the user's username.
From `1efee03cdcb96d90ad48ccc7b8666033`
To `mike:1efee03cdcb96d90ad48ccc7b8666033`

```sh
john --format=raw-md5 --single hash07.txt
```

## Custom Rules

### Intro

With the single crack mode, we can mangle the given username to crack it. And we can even define our own mangling rules with `john`.

Imagine that many admins will require user to adjust the level of password complexity, which can combat dictionary attacks.
For example, they may enforce that:
- Lowercase letter
- Uppercase letter
- Number
- Symbol

So even the password become more complex, we can exploit the password requirements (or password complexity predictability) to create our mangling rules.

We can predict how the password change by following the above requirements.
From `polopassword`
To `Polopassword1!` 

### Configuration

```sh
/opt/john/john.conf  # or
/etc/john/john.conf  
```

Detailed documentation: [here](https://www.openwall.com/john/doc/RULES.shtml)

`[Lists.Rules:THMRules]` is used to define the name of your rule; this is what you will use to call your custom rule a John argument.

Most common modifiers here:
- `Az`: Takes the word and appends it with the characters you define
- `A0`: Takes the word and prepends it with the characters you define
- `c`: Capitalizes the character positionally.

These can be used in combination to define where and what in the word you want to modify.

Lastly, we should define what characters should be appended, or prepended.
We do this by adding character sets in square brackets `[]`.
These follow the modifier patterns inside double quotes `" "`. Here are some examples:

- `[0-9]`: Will include numbers 0-9  
- `[0]`: Will include only the number 0
- `[A-z]`: Will include both upper and lowercase  
- `[A-Z]`: Will include only uppercase letters
- `[a-z]`: Will include only lowercase letters

- `[a]`: Will include only `a`
- `[!£$%@]`: Will include the symbols `!`, `£`, `$`, `%`, and `@`

To generate a wordlist from the rule that would match the example password `Polopassword1!`, we could create a rule entry that looks like this:

```
[List.Rules:PoloPassword]
cAz"[0-9][!£$%@]"
```

Utilizes the following:

- `c`: Capitalizes the first letter
- `Az`: Appends to the end of the word
- `[0-9]`: A number in the range 0-9
- `[!£$%@]`: The password is followed by one of these symbols

### Using Custom Rules

```sh
--rule=PoloPassword
```

A full command:
```sh
john --wordlist=[path to wordlist] --rule=PoloPassword [path to file]
```

Jumbo john already has an extensive list of custom rules, you can use it for reference, around line 678.

## Cracking Zip Files

### `zip2john`

Similarly to the `unshadow` tool we used previously, we will use the `zip2john` to convert the Zip file into a hash format that John can understand and hopefully crack.

```sh
zip2john [options] [zip file] > [output file]

zip2john zipfile.zip > zip_hash.txt
```
- `[options]`: Allows you to pass specific checksum options to `zip2john`; this shouldn’t often be necessary
- `[zip file]`: The path to the Zip file you wish to get the hash of
- `>`: This redirects the output from this command to another file
- `[output file]`: This is the file that will store the output

### Cracking

```sh
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
```

## Cracking RAR Archives

### `rar2john`

```sh
rar2john [rar file] > [output file]

/opt/john/rar2john rarfile.rar > rar_hash.txt
```
- `rar2john`: Invokes the `rar2john` tool
- `[rar file]`: The path to the RAR file you wish to get the hash of
- `>`: This redirects the output of this command to another file
- `[output file]`: This is the file that will store the output from the command

### Cracking

```
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
```

### Unrar

```sh
unrar x secure.rar
unrar l secure.rar
```

## Cracking SSH Keys with John

### Key Authentication

We can use our private key to log on a machine if it's configured to allow key authentication, and has our public key in it.

In most scenarios, we wanna encrypt our private key with a passphrase for higher level of security. 

However, with `john`, we can also crack the passphrase-protected SSH private key.

### `ssh2john`

Find it on
```sh
/usr/share/john/ssh2john.py

/opt/john/ssh2john.py
```

Usage:
```sh
ssh2john [id_rsa private key file] > [output file]

/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt
```
- `ssh2john`: Invokes the `ssh2john` tool
- `[id_rsa private key file]`: The path to the id_rsa file you wish to get the hash of
- `>`: This is the output director. We’re using it to redirect the output from this command to another file.
- `[output file]`: This is the file that will store the output from

### Cracking

```sh
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```

## Further Reading

John's official documentation:
https://www.openwall.com/john/

