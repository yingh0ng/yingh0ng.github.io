---
title: 记一个PageHelper的使用问题
date: 2018-07-17
tags: 数据库
---



###### 在使用PageHelper4.1.6做分页的时候，有两点需注意。

1. 当”select count(0)”的total小于size的时候，PageHelper不做分页（无论page传的是多少，
   最后组装的sql后面limit部分的offset都是0）
   
   <!-- more -->
   
2. 当传的offset>(total-1)时，最后PageHelper都会把offset改为total-size-1。即当传的page
   大于最后一页时，永远的都是返回最后一页的数据