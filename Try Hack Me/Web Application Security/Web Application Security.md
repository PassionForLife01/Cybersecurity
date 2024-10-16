## A. How does Web Application work

![[Pasted image 20240814225623.png]]

## B. Web Application Security Risks
### 1. Identification and Authentication Failure
- Brute force
- Weak password
- Store password in plain text
### 2. Broken Access Control
- ***Give users more privilege.*** For example, an online customer should be able to view the prices of the items, but they should not be able to change them.
- ***View or modify someone else’s account by using its unique identifier.*** For example, an online customer should be able to view the prices of the items, but they should not be able to change them.
- ***Browse pages that require authentication (logging in) as an unauthenticated user.*** For example, we cannot let anyone view the webmail before logging in.
### 3. Injection
### 4. Cryptographic Failures
- ***Sending sensitive data in clear text, for example, using HTTP instead of HTTPS.*** HTTP is the protocol used to access the web, while HTTPS is the secure version of HTTP. Others can read everything you send over HTTP, but not HTTPS.
- ***Relying on a weak cryptographic algorithm.*** One old cryptographic algorithm is to shift each letter by one. For instance, “TRY HACK ME” becomes “USZ IBDL NF.” This cryptographic algorithm is trivial to break.
- ***Using default or weak keys for cryptographic functions.*** It won’t be challenging to break the encryption that used `1234` as the secret key.
## C. Example: IDOR (Insecure Direct Object References)
This vulnerability falls under the Broken Access Control. 
```
https://store.tryhackme.thm/products/product?id=52
```