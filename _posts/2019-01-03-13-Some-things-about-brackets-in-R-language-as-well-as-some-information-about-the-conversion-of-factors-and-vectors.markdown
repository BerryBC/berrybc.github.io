---
layout: post
title:  拾叁-关于R语言中括号的一些事，以及因子、向量转换的一些情
date:   2019.01.03 00:56:44 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



在`R语言`中，遇到什么事情，请别犹豫，立刻输入`?somethingunknow`（当然somethingunknow的意思是some thing unknow，例如你不懂函数`ctree`，你要输入`?ctree`，我是不是很无聊），你马上会有解答的。

所以这篇东西只是用来打发下无聊漫长的夜晚，还有找不到工作的寂静无聊。

可能最好先看第5点再重头看看呗。

# 一、我为什么无聊到这个程度？

首先我在写脚本的时候，想把数据帧的第一列变为`行标题`（`rownames`），但这时候却报错了：

```bash
> data.input.f<-data.frame(data.input[2:ncol(data.input)],row.names=data.input[1])
Error in if (length(row.names) != 1L || row.names < 1L || row.names >  :
  需要TRUE/FALSE值的地方不可以用缺少值
此外: Warning messages:
1: In Ops.factor(left, right) : ‘<’ not meaningful for factors
2: In Ops.factor(left, right) : ‘>’ not meaningful for factors

```


说真的，其实我不是很懂这到底是什么意思，但我单独抽出

`head(data.input[2:ncol(data.input)])`

以及

`head(data.input[1])`

都是我想哟的结果，于是我用了神器`?`，发现里面对于`row.names`的描述为：
>row.names
NULL or a single integer or character string specifying a column to be used as row names, or a character or integer **`vector`** giving the row names for the data frame.


关键点我已经表粗了，我终于懂了！你是要一个`向量(vector)`作为输入的，那..............我先用`class`函数看看先：

```bash
> class(data.input[1])
[1] "data.frame"
```



诶呀我X，你为什么是一个数据帧了呢！？不科学啊！

这时候我想起了之前有个例子曾经用`[[1]]`作为提取其中一列的数据，我尝试下先：

```bash
> class(data.input[[1]])
[1] "factor"
```


哎呀妈呀....为什么不是向量的！？不管了，先试下先：

```bash
> data.input.f<-data.frame(data.input[2:ncol(data.input)],row.names=data.input[[1]])
> head(data.input.f)
             ComparedLastYearVoiceGrowth ComparedPHolidayVoiceGrowth ComparedLastYearTrafficGrowth ComparedPHolidayTrafficGrowth
从化花市                         -0.0216                      5.0685                         0.534                         1.771
番禺花市                         -0.3020                     -0.0401                         0.283                         0.931
越秀西湖花市                     -0.2872                      0.1258                         0.884                         0.719
增城花市                         -0.0487                      0.2641                         0.655                         0.665
海珠花市                         -0.4660                     -0.0106                         0.724                         0.406
黄埔花市                         -0.4547                      0.5840                         0.544                         0.275
```


哎呀，成了！？


# 二、为什么！？因子跟向量是什么鸟关系

这个关系呢，其实说来话长，我就不说了，说点实际的。当我们需要一个`向量`的时候，我们手里握的是`因子`，那怎么办！？转换啊！先来尝尝最简单的转换：

```bash
> head(data.input[[1]])
[1] 从化花市     番禺花市     越秀西湖花市 增城花市     海珠花市     黄埔花市
193 Levels: 105国道 106国道 5号停机坪 APM线 G324国道 白鹅潭 白水寨 白云公园 白云国际会议中心 白云国际机场 白云花市 白云山 白云万达广场 百万葵园 北京路步行街 城隍庙 赤沙服务区 从化花市 从化汽车站 ... 珠三角环线高速
> head(c(data.input[[1]]))
[1]  18  42 179 181  81 100
> class(c(data.input[[1]]))
[1] "integer"

```

显然不能直接用`c()`来转换因子，那还有什么办法呢？这时候，你可以`Google`，也可以`?c`。帮助文档给出了一个答案：
>**c** is sometimes used for its side effect of removing attributes except names, for example to turn an array into a vector. **as.vector** is a more intuitive way to do this, but also drops names. Note that methods other than the default are not required to do this (and they will almost certainly preserve a class attribute).


显然我们可以尝试一下`as.vector`（虽然看描述好像比`c()`更不靠谱）。

结果：

```bash
> head(as.vector(data.input[[1]]))
[1] "从化花市"     "番禺花市"     "越秀西湖花市" "增城花市"     "海珠花市"     "黄埔花市"
> class(as.vector(data.input[[1]]))
[1] "character"

```

还不错哦！！

所以..........诶，我刚说到哪里？哦，就是刚那条把数据帧第一列变成行标题的语句最好改为：

```data.frame(data.input[2:ncol(data.input)],row.names=as.vector(data.input[[1]]))```


# 三、那关中括号什么事呢？

`R语言`里面，`中括号`是...不是很想说，不如大家来看看[R统计笔记（四）：中括号与双中括号的差异](https://blog.csdn.net/yiifaa/article/details/73252980?locationNum=8&fps=1)吧。

看完其实都了解得差不多，如果实在不是很理解就自己去看看`?"[["`呗。

下面弄各实例呗，当你对一个`数据帧`去使用`中括号`的时候：

```bash

> head(data.input)
       HotSpot ComparedLastYearVoiceGrowth ComparedPHolidayVoiceGrowth ComparedLastYearTrafficGrowth ComparedPHolidayTrafficGrowth
1     从化花市                     -0.0216                      5.0685                         0.534                         1.771
2     番禺花市                     -0.3020                     -0.0401                         0.283                         0.931
3 越秀西湖花市                     -0.2872                      0.1258                         0.884                         0.719
4     增城花市                     -0.0487                      0.2641                         0.655                         0.665
5     海珠花市                     -0.4660                     -0.0106                         0.724                         0.406
6     黄埔花市                     -0.4547                      0.5840                         0.544                         0.275
> class(data.input)
[1] "data.frame"
> head(data.input[2])
  ComparedLastYearVoiceGrowth
1                     -0.0216
2                     -0.3020
3                     -0.2872
4                     -0.0487
5                     -0.4660
6                     -0.4547
> class(data.input[2])
[1] "data.frame"
> head(data.input[[2]])
[1] -0.0216 -0.3020 -0.2872 -0.0487 -0.4660 -0.4547
> class(data.input[[2]])
[1] "numeric"
```


但如果你对一个`列表`去使用`中括号`的话呢：

```bash
> head(mylist)
$title
[1] "My First List"

$ages
[1] 25 26 18 39

[[3]]
     [,1] [,2]
[1,]    1    6
[2,]    2    7
[3,]    3    8
[4,]    4    9
[5,]    5   10

[[4]]
[1] "one"   "two"   "three"

> class(mylist)
[1] "list"
> mylist[3]
[[1]]
     [,1] [,2]
[1,]    1    6
[2,]    2    7
[3,]    3    8
[4,]    4    9
[5,]    5   10

> class(mylist[3])
[1] "list"
> mylist[[3]]
     [,1] [,2]
[1,]    1    6
[2,]    2    7
[3,]    3    8
[4,]    4    9
[5,]    5   10
> class(mylist[[3]])
[1] "matrix"

```

也就是说，当你使用`单中括号`的时候，你仍然会获得`原对象`的`数据类型`（但内容只有你选中的字段），而当你用`双中括号`的时候才会获得`他自身的数据类型`。


# 四、其实我只是无聊

明天可以说下`formula`呗，真的挺无聊的我。


# 五、关于所用函数

本次所说的内容里面主要包含两个函数，一个函数是`class`，另外一个是`head`，其实对于`linux`用户来说，`head`我觉得不用说了（那就真的不说了哦！）。而`class`呢？就是查询该对象的的类型，就像：

```
> class(1024)
[1] "numeric"
> class('Love You Baby')
[1] "character"
```

完事。