---
layout: post
title:  伍拾肆- R语言之 ‘如果一列为空则取另一列’
date:   2021-06-01 00:50:58 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1、话不多说让我们上代码

```r
data$CITY.x <- ifelse(is.na(data$CITY.y),data$CITY.x,data$CITY.y)
```

其中

`data` : 为数据帧

`$` : 后面为列名

`ifelse` : 函数为三目函数，

```
ifelse ( 判断条件 , 如果为条件真的值 , 如果为条件为假的值 )
```

`is.na` : 判断列是否为空

# 2、R语言可整列赋值

这个没啥好说的，就直接是这一列来对应的数据为其他列相应的行就是了
