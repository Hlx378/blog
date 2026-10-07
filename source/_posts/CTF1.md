---
title: 9月
date: 2026-09-29 10:00:00
updated: 2026-09-30 10:00:00
categories:
  - 练习
tags:
  - web
  - CTF练习
description: 9月底在CTF练习平台上做的题目的理解
---

web
2026-9-29
1. 查看网站上的robots.txt就是直接在原来网址上加/robots.txt进行查看
2. 找信息的时候在网络中找到相应头看看有没有手写入的相应
3. 顺着相应的提示继续往下查看
4. 看到源码后开始源码审计（找出源码的漏洞）

2029-10-2
1. 没有交互面一上来需要使用dirsearch工具来扫服务器（服务器上还藏着其他的文件）
```
 dirsearch -u http://node5.anna.nssctf.cn:你的端口/ \
    -e php,txt,zip,rar,html \
    --exclude-sizes 67
```
2. 之后按得到的线索去直接/去查看这个文件，知道这个文件到底指的是什么

3. 找到源码分析得到
```
<?php
error_reporting(0);
if(isset($_GET['cxk'])){
    $cxk=$_GET['cxk'];
    if(file_get_contents($cxk)=="ctrl"){
        echo $flag;
    }else{
        echo "洗洗睡吧";
    }
}
?>
```
最后使用`/orzorz.php?cxk=data:text/plain,ctrl`来查询得到flag


