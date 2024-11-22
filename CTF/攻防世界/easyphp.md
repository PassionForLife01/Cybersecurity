# easyphp
## 源码：
```php
<?php  
highlight_file(__FILE__);  
$key1 = 0;  
$key2 = 0;  
  
$a = $_GET['a'];  
$b = $_GET['b'];  
  
if(isset($a) && intval($a) > 6000000 && strlen($a) <= 3){  
    if(isset($b) && '8b184b' === substr(md5($b),-6,6)){        $key1 = 1;  
        }else{  
            die("Emmm...再想想");  
        }  
    }else{  
    die("Emmm...");  
}  
  
$c=(array)json_decode(@$_GET['c']);  
if(is_array($c) && !is_numeric(@$c["m"]) && $c["m"] > 2022){  
    if(is_array(@$c["n"]) && count($c["n"]) == 2 && is_array($c["n"][0])){        $d = array_search("DGGJ", $c["n"]);        $d === false?die("no..."):NULL;  
        foreach($c["n"] as $key=>$val){            $val==="DGGJ"?die("no......"):NULL;  
        }        $key2 = 1;  
    }else{  
        die("no hack");  
    }  
}else{  
    die("no");  
}  
  
if($key1 && $key2){  
    include "Hgfks.php";  
    echo "You're right"."\n";  
    echo $flag;  
}
```
## 知识点补充：
### `intval()`
将字符串转为整数，暗含弱类型比较本质。
### JSON
JSON 是一种格式，short for (JavaScript Object Notation)。
表示对象：
```json
{"name" : "张三","age" : 18}
```
表示数组：
```php
["apple", "banana", "oragne"]
```
### PHP 关联数组
类似于 C++ 中的 MAP。由键值对组成。
使用
```php
$c=(array)json_decode(@$_GET['c']); 
```
可将 `json_decode` 弄出的对象，硬生生地整成关联数组。
### `array_search()`
在低版本中会进行弱类型比较。