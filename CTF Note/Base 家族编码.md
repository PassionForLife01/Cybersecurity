# Base 家族编码

以 `hello,` 为例。
## base16

相当于将 ascii 的值转为 16 进制。

```
hello
```

```
104 101 108 108 111 44 
```

```
01101000 01100101 01101100 01101100 01101111 00101100
```

```
0110 1000 0110 0101 0110 1100 0110 1100 0110 1111 0010 1100
```

表：
```
0-9 A-F
```

```
68656C6C6F2C
```

## base32

字符集：
```
A-Z2-7=
```

5 个字符 --> 8 个 base 32 字符

32 代表码表有 32 条对应规则。

所以得到 ascii 值后，转为二进制，5 个 bit 5 个 bit 地分组。不足 5 个 bit 的自动补 0 。

```
01101000 01100101 01101100 01101100 01101111 00101100
```

```
01101 00001 10010 10110 11000 11011 00011 01111 00101 100
```

```
01101 00001 10010 10110 11000 11011 00011 01111 00101 10000 
```

```
NBSWY3DPFQ======
```

## base64

字符集：
```
A-Za-z0-9+/=
```

64 代表字符集有 64 格。
$2^{6} = 64$ 

则 6 个 bit 地分开，则为：
3 个字符 --> 4 个 base64 字符。

```
01101000 01100101 01101100 01101100 01101111 00101100
```

```
011010 000110 010101 101100 011011 000110 111100 101100
```

```
aGVsbG8s
```

## 总结
### 编码
其它的 base 编码也是如此。

其实 baseN 就是一种数的进制，N 就是这种进制的基。

将 ascii 码的值的二进制弄出来，合并，变成一个大整数。

然后不断对 N 作除法，余数的范围是 $[0, N-1]$ ，那么字符集的大小就应该为 N。

然后根据余数的大小，在字符集里面找对应的字符，直到商为 0 。

将字符逆过来。

但是还有一些细节：
如果 N 是 2 的幂，就可以从二进制的视角看 base 编码。而且对于 ascii 的二进制值整合的字符串，要按每 $M=log_2^N$ 个 bit 分组，不足 $M$ 个 bit 的话，要补 0。并且要知道多少个 ASCII 字符 对应多少个 baseN 字符。
设 $x$ 个 ASCII 字符对应 $y$ 个 baseN 字符。即 $x \times 8 = y \times M = z$ 。
$z=lcm(8, M)$
那么就是 $\frac{lcm(8,M)}{8}$ 个 ASCII 字符对应 $\frac{lcm(8, M)}{M}$ 。 

所以 
base32: 5 个 ASCII 字符对应 8 个 base32 字符。
base64: 3 个 ASCII 字符对应 4 个 base32 字符。

如果 N 不是 2 的幂，那么就直接当作大整数，一直作除法，根据余数确定字符，然后倒序即可。
### 解码

经过一堆编码的要求，解码应当是比较容易的。

去掉等号，根据 baseN 的字符集，找到 baseN 字符对应的数字，化为二进制。将二进制合并，然后 8 个 8 个取 ASCII 值即可。不足 8 位的舍去，因为是后补的 0 。