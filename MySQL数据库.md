---
title: MySQL数据库
description: 
published: true
date: 2024-10-21T05:43:51.769Z
tags: 
editor: markdown
dateCreated: 2024-10-20T14:37:38.831Z
---

# MySQL数据库文乱码问题
先介绍一个命令行
`show variables like 'character_set%';`
命令行作用查看数据库编码格式
![数据库字符集.png](/数据库字符集.png)
分析：
character_set_client：客户端请求数据的字符集
character_set_connection：客户机与服务器连接的字符集
character_set_database：默认数据库的字符集；如果没有默认数据库，就会使用 character_set_server指定的字符集（建议不要随意更改）
character_set_filesystem：把 character_set_client转换character_set_filesystem (默认为binary, 不做任何转换)
character_set_results：返回给客户端的字符集
character_set_server：数据库服务器的默认字符集
character_set_system：系统字符集，默认utf8。（用于数据库的表、列和存储在目录表中函数的名字）
数据库产生中文乱码问题的原因
中文数据乱码的根源在于编码方式的不统一或者在传输过程中由于不同系统的编码方式不一样导致数据传输错误出现乱码。
解决办法：
1首先输入上述命令行查看数据库的编码的字符集
2.修改字符集，将字符集进行统一
可以找到my.ini文件添加一下代码
`[mysql]
default-character-set=latin1

[mysqld]
character-set-server=latin1

`
特殊情况：要是没有这个文件夹的话可以自己创一个记事本，输入上述代码修改名称
重启MySQL服务，便可以进行正常运行
，重启后需要重新输入命令行检查是否修改成功，gbk字符编码集与Latin1字符编码集合不匹配，所以便需要将gbk的字符编码集合改成utf-8的字符编码集合