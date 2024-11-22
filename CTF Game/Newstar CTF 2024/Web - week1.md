# Web - week1
## headach3
```sh
curl http://eci-2ze8qyohrl89jdgu7q97.cloudeci1.ichunqiu.com/ --verbose
```
## 会赢吗
F12
flag第一部分：ZmxhZ3tXQTB3，开始你的新学期吧！:/4cqu1siti0n

第二关：
看到一个按钮，想到按钮，一般情况下应该弹个窗才对。查看 `<script>` 部分：
```html
<script>
	async function revealFlag(className) {
		try {
			const response = await fetch(`/api/flag/${className}`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				}
			});
			if (response.ok) {
				const data = await response.json();
				console.log(`恭喜你！你获得了第二部分的 flag: ${data.flag}\n……\n时光荏苒，你成长了很多，也发生了一些事情。去看看吧：/${data.nextLevel}`);
			} else {
				console.error('请求失败，请检查输入或服务器响应。');
			}
		} catch (error) {
			console.error('请求过程中出现错误:', error);
		}
	}

	// 控制台提示
	console.log("你似乎对这门叫做4cqu1siti0n的课很好奇？那就来看看控制台吧！");
</script>
```
会向服务器中的 `/api/flag/${className}` 处发送 POST 请求。当前网页是可访问的，发个 POST 试试。
在 Console 中输入
```js
revealFlag("4cqu1siti0n")
```

输出：
```
恭喜你！你获得了第二部分的 flag: IV95NF9yM2Fs
……
时光荏苒，你成长了很多，也发生了一些事情。去看看吧：/s34l
```

第三关：
```html
<script>
        document.addEventListener('DOMContentLoaded', function () {
            const form = document.getElementById('seal_him');
            const stateElement = document.getElementById('state');
            const messageElement = document.getElementById('message');

            form.addEventListener('submit', async function (event) {
                event.preventDefault();

  
                if (stateElement.textContent.trim() !== '解封') { // !!!
                    messageElement.textContent = '如何是好？';
                    return;
                }

                try {
                    const response = await fetch('/api/flag/s34l', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        body: JSON.stringify({ csrf_token: document.getElementById('csrf_token').value })
                    });

                    if (response.ok) {
                        const data = await response.json();
                        messageElement.textContent = `第三部分Flag: ${data.flag}, 你解救了五条悟！下一关: /${data.nextLevel || '无'}`;
                    } else {
                        messageElement.textContent = '请求失败，请重试。';
                    }
                } catch (error) {
                    messageElement.textContent = '请求过程中出现错误，请重试。';
                }
            });
        });
    </script>
```

只要让 `stateElement.textContent.trim() == '解封'` 就行了。`stateElement` 是 `Id` 为 `state` 的某个东西。
![[Pasted image 20241027105843.png]]
修改为解封。按下按钮，得：
```
第三部分Flag: MXlfR3I0c1B, 你解救了五条悟！下一关: /Ap3x
```
源码中有 `<noscript>` 标签，将在禁用 js 时显示。
在谷歌的设置中，选择隐私和安全，选择网站设置，找到 JavaScript，禁用当前网站的 JavaScript。
![[Pasted image 20241027111712.png]]
点击无量空处
```
{"flag":"fSkpKcyF9","nextLevel":null}
```
Base64 解密。
## 智械危机
`/robotx.txt`
`/backd0or.php`
脚本：
```python
import base64
import hashlib

# Base64 编码
def base64_encode(data):
    # 将字符串编码为字节
    byte_data = data.encode('utf-8')
    # 进行 Base64 编码
    base64_bytes = base64.b64encode(byte_data)
    # 将结果从字节转换回字符串
    return base64_bytes.decode('utf-8')

# Base64 解码
def base64_decode(base64_data):
    # 将 Base64 字符串转换为字节
    base64_bytes = base64_data.encode('utf-8')
    # 进行解码
    byte_data = base64.b64decode(base64_bytes)
    # 将字节转换回字符串
    return byte_data.decode('utf-8')

# MD5 哈希
def md5_hash(data):
    # 创建一个 md5 对象
    md5 = hashlib.md5()
    # 更新对象，输入数据必须是字节
    md5.update(data.encode('utf-8'))
    # 返回十六进制哈希值
    return md5.hexdigest()

de_cmd = input("Please input a cmd: ")
cmd = base64_encode(de_cmd)
a = cmd[::-1]
b = md5_hash(a)
c = b
key = base64_encode(c)

print("cmd = ", cmd)
print("key = ", key)
```
最终通过命令 `cat /flag` 获得 flag。