---
layout: post
title:  柒-关于Java变量类型定义及初始化的事情
date:   2018.12.06 15:19:06 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

# 1、这个世界让我觉得讨厌

其实我只是讨厌这个极端的世界，以及需要提前设定的很多痛苦的事情。

没了啦，只是我之前用的`VB.Net`以及`JS`都是对`数据类型定义`比较宽大的语言，突然来到`Java`我真的很难习惯，而且是极端不习惯。

# 2、关于String及char数据类型

在`JS`里面，我是从来没考虑过`char`类型的，但当我来到了`Java`的世界我才发现是原来`String`其实不是天生的东西，详情可以看[qauchangqingwei](https://me.csdn.net/qauchangqingwei)博客里面说的：
>char是基本数据类型，而String是个类，属于引用数据类型。String类可以调用方法，具有面向对象的特征。

因为对于`char[]`以及`String`的长期混淆，当我看到一个函数的传入参数为`(String userName, String database, char[] password)`时，我很自然的输入了`("San","Shi","Shu")`。

当然，毫无悬念地，错误了。

错误不要紧，知道`char[]`跟`String`不一样，并且[转换](https://blog.csdn.net/qq_20032995/article/details/79654075)就行了对吧？

`("San","Shi","Shu".toCharArray())`

反正对于这种真的很烦躁。

# 3、关于String数组定义

前期使用一个导出`CSV`的包，我看那个包的例程是需要调用`ArrayList<String[]>`的，那没问题，但接下来我想定义一个`String[]`的变量，接下来是我的定义方式：

```java
ArrayList<String[]> csvData = new ArrayList<String[]>();
String[] csvHeader=new String()[];
```

哎呀！错了！？

**错了！？！？！？**

没错，是错了。

正确方式可以有：

```java
String[] straHeader = new String[csvHeader.length + 2];
String[] straHeader = new String[2];
String[] csvHeader;
```

------

*光定义就让我焦头烂额了，这种情况下就别纠结是否在定义时就必须确定数组大小等情况了。*
*我就是觉得为什么这么麻烦的语言居然使用率这么高，`JS`不好么？*

-------

# 4、关于String的相等判定

我在做`分词`后的词性判定是，发现一个很神奇的事情，代码如下：

```java
for (org.ansj.domain.Term termEle : ToAnalysis.parse(str)) {
  if (termEle.getNatureStr() != "w" && termEle.getNatureStr() != "null") {
    System.out.println(termEle.getRealName() + "---|" + termEle.getNatureStr() + "|---"
      + ((Boolean) (termEle.getNatureStr().toString() == "w")));
  }
}
```

反正就是当词性是w的时候就判断为true，结果！！！结果如下：

```
欢迎---|v|---false
使用---|v|---false
ansj---|en|---false
seg---|en|---false
,---|w|---false
(---|w|---false
ansj---|en|---false
中文---|nz|---false
分词---|v|---false
)---|w|---false
......
```

各位看官能发现有问题么！？

当然在我之前说的那个[文章](https://blog.csdn.net/qauchangqingwei/article/details/80831797)是有介绍这种情况以及原因的，反正`String`的相等就不要用`==`了，用`termEle.getNatureStr().equals("w")`，就是`equals()`函数。

------

*这东西耗了我2个小时，我还以为分词的包有问题呢....*

-------


# 5、大小写问题以及数据类型的问题

在定义数据类型时，首先必须关键字注意`String`和`string`是不同的，相对`int`以及`Int`、`Char`以及`char`也是不同的东西，这是被微软害苦了的人的苦苦相告。

其余像`Integer`和`int`这种东西可以参考 [java面试题之int和Integer的区别](https://www.cnblogs.com/guodongdidi/p/6953217.html)这种文章，实在让人太疯狂了我觉得。

# 6、无奖竞猜

我是否会在本系列写到**“拾”**之前完全没有`Java`的内容呢？