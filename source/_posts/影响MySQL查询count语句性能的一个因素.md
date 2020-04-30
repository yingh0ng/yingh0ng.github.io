---
layout: 影响mysql
title: 影响MySQL查询count语句性能的一个因素
date: 2019-08-23
tags: 数据库
---



#### 前言

在生产上发现对A表进行select count(*)查询时非常慢，而数据量是A表几百倍的B表进行select count(*)时查询时间竟然跟A表非常接近

<!-- more -->



#### key_len是什么

很多人会问，select count(1)跟select count(*)哪个快，答案是—一样快。两者都是用到了表里的最优索引，那什么是最优索引呢。通过EXPLAIN后，输出的指标有个叫key_len的，意思是索引的字节数。通过测试，上述两种方式都用到了表里key_len最小的索引（UTF-8一个字符占3个字节）。
回到前面所说的查询慢的问题，发现就是前面的表中只有一个索引，key_len是98.而后者最小的key_len是5，所以这就造成两者的查询效率差异大。

