
## 解密

$E$, $N$ 公开
$D$ 私密

公钥 $(E, N)$
私钥 $(D, N)$

接收方向发送方发送公钥，自己保留私钥。

$(P)^E\bmod N = C$ 
$(C)^E \mod N = P$
## 加密

重点在生成密钥。

1. 选择两个质数 $p$, $q$
	$p$ = $3$, $q$ = $11$
2. 算出 $N$, $N = p \times q$ 
	$N$ = $33$
3. 算出 $\phi(N)$, $T = \phi(N) = (p - 1) \times (q - 1)$
	$T = 20$
4. 选择公钥 $E$ ，满足条件: 质数, $1 < e < T$, 不是 $T$ 的因子。
	$E = 3$, 常见的值：$3, 17, 65537$ 
5. 计算私钥 D，满足：$(D \times E) \bmod T = 1$  , 且 $D$ 为整数
	$D = 7$ 

于是得到最后的
Public Key: $(3, 33)$
Private Key: $(7, 33)$ 

Let's give it a quick review:
1. Choose two primes $p$ and $q$ 
2. $n = p \times q$ 
3. Calculate $\phi(n) = n - p - q + 1 = (p - 1)(q - 1)$
4. Choose $e$, where $1 < e < \phi(n)$ and prime to $\phi(n)$ 
5. Choose $d$, such that $e \times d = 1 \bmod \phi(n)$, or $d = e^{-1} \bmod \phi(n)$ 
6. Generate public key $(n, e)$
7. Generate private key $(n, d)$
8. Encrypt plaintext $m$ with $c = m^e \bmod n$ 
9. Decrypt ciphertext $c$ with $m = c^d \bmod n$ 

In addition:
- Generate signature: $s = m^d \bmod n$
- Check the signature $m = s^e \bmod n$ 