# Regular Expression
https://regex101.com/

### `?` 可有可无，如有则一次
`?` 前的字符可有可无。
```
ab?c

matched:
ac
abc
```
### `*` 0个或多个
`*` 前的字符零个或多个。
### `+` 1个以上
`+` 前的字符一个以上。
### `{}`  连续出现多少次
`{}` 内限定前面的字符连续出现多少次。
```
ab{2}c
matched:
abbc

ab{2, 6}c
matched:
abbc
abbbc
abbbbbbc

ab{2, }c
matched:
abbc
abbbbbbbc
```
### `()` 打包
将字符打包。 
```
(ab)+
matched:
abc
ababc
abababc
```
### `|` 或运算 
使用或运算。
```
a (cat | dog)
matched:
a cat
a dog
```
### 字符类
```
[abc]+
[a-z]
[A-Z]
[0-9]
```
### 元字符
```
\d 数字字符，0-9
\w 单词字符，所有英文字母+数字+上下划线
\s 空白字符，包括制表符和换行符

\D 非...
\W 非...
\S 非...

. 代表任意字符，不包含换行符
```
### 行首行尾
```
^a 只匹配行首的 a
$a 只匹配行末的 a
```
### 贪婪匹配和懒惰匹配
```
<.+>  

<.+?> 加了个问号
```
## 总结
![[Pasted image 20241102095938.png]]