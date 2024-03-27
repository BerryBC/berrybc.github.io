---
layout: post
title:  肆拾伍- 正则表达式 (?=pattern) 以及 (?!pattern)
date:   2020-07-07 00:16:26 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

# 1. 一位旧同事的奋斗历程

一位旧同事在很努力的学习 `Python`，然后横在他面前有一座`大山`，名为`正则表达式`。

总感觉很多人对`正则表达式`有异常严重的`执念`，但其实我当初是完全没有看过的。

-----

**我同事的原话是：**


```python
print(re.findall(r"\b\w+(?=ing)","cooking ，swe ，doing"))

# 输出是
['cook', 'do']
```

而

```python
print(re.findall(r"\b\w+(?!ing)","cooking  swe  doing"))

# 输出是
['cooking', 'swe', 'doing']
```

现在不懂是  `(?!ing)` 理论上应该输出的只有 `swe` 的   现在却输出['cooking', 'swe', 'doing']

----

# 2. 关于 (?xpattern) 的解释

![百度图片](/photo/InPost/45-1.png)

[百度百科的解释](https://baike.baidu.com/item/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F/1700215?fr=aladdin)

# 3. 先有一点解释

为了方便一点做介绍，我们先给出以下网上`摘抄`：

[Python中re的match、search、findall、finditer区别](https://blog.csdn.net/djskl/article/details/44357389)

> 1、match

re.match(pattern, string[, flags])

从**首字母**开始开始匹配，string如果包含pattern子串，则匹配成功，返回Match对象，失败则返回None，若要完全匹配，pattern要以$结尾。

2、search

re.search(pattern, string[, flags])

若string中包含pattern子串，则返回Match对象，否则返回None，注意，如果string中存在多个pattern子串，只返回第一个。

3、findall

re.findall(pattern, string[, flags])

返回string中所有与pattern相匹配的全部字串，返回形式为数组。


还有：

[正则表达式中(?:pattern)、(?=pattern)、(?!pattern)、(?<=pattern)和(?<!pattern)](https://www.cnblogs.com/dogecheng/p/11466687.html)
>(?=pattern)#

正向肯定预查（look ahead positive assert），匹配pattern前面的位置。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。

简单说，以 xxx(?=pattern)为例，就是捕获以pattern结尾的内容xxx

例如，"Windows(?=95|98|NT|2000)"能匹配"Windows2000"中的"Windows"，但不能匹配"Windows3.1"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

----

(?!pattern)#

正向否定预查(negative assert)，在任何不匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。

简单说，以 xxx(?!pattern)为例，就是捕获不以pattern结尾的内容xxx

例如"Windows(?!95|98|NT|2000)"能匹配"Windows3.1"中的"Windows"，但不能匹配"Windows2000"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

# 4. (?=pattern)

我们话不多说直接上`代码`：


```python
import re
```


```python
regex=re.compile(r"\b\w+(?=ing)")

string="cooking  swe  doing"
print("一.1. "+str(regex.findall(string)))

string="cooking"
print("一.2. "+str(regex.match(string)))

string="coo"
print("一.3. "+str(regex.match(string)))

string="cook"
print("一.4. "+str(regex.match(string)))
# 一直到这里还是没有出现过 ing ，所以不会匹配出来
string="cookin"
print("一.5. "+str(regex.match(string)))
# 第一次出现 ing！！出来结果了！
string="cooking"
print("一.6. "+str(regex.match(string)))
# 后面的 be 是没有匹配出来的
string="cookingbe"
print("一.7. "+str(regex.match(string)))
# 因为最后面还有一个 ing ，所以前面的 cookingbe 是能被 \w+ 所包括，所以直接列出来了。
string="cookingbeing"
print("一.9. "+str(regex.match(string)))
```

    一.1. ['cook', 'do']
    一.2. <re.Match object; span=(0, 4), match='cook'>
    一.3. None
    一.4. None
    一.5. None
    一.6. <re.Match object; span=(0, 4), match='cook'>
    一.7. <re.Match object; span=(0, 4), match='cook'>
    一.9. <re.Match object; span=(0, 9), match='cookingbe'>


```python

regex=re.compile(r"\b(?=ing)\w+")
# 为什么会整个 ingcoo 匹配出来呢？ 可以想象当满足 (?=ing) 后，该正则表达式成立，重新指向 'i' 字符,然后整个 ingcoo 都被 \w 匹配
string="ingcoo"
print("三.1. "+str(regex.match(string)))

string="co,ingok"
print("三.2. "+str(regex.match(string)))
# 注意，这里变成了 search 函数之后，其实是可以出来的，因为 match 函数是从字符串头开始匹配
string="co,ingok"
print("三.3. "+str(regex.search(string)))
# findall 是可以找出来的，跟 search 一样，都是不需要从字符串头开始的
string="co,ingok"
print("三.4. "+str(regex.findall(string)))

string="cooingkin"
print("三.5. "+str(regex.match(string)))
# 为什么这里不行呢？因为，我们在正则表达式中，
string="cooingkin"
print("三.6. "+str(regex.search(string)))

regex=re.compile(r"(?=ing)\w+")
# 为什么第6点匹配不出来呢？对比两个表达式，我们发现，我们的正则表达式把前面的 \b 去掉了，即我们不需要该单词一开始包含 ing ，即可匹配
string="cooingkin"
print("三.8. "+str(regex.search(string)))

```

    三.1. <re.Match object; span=(0, 6), match='ingcoo'>
    三.2. None
    三.3. <re.Match object; span=(3, 8), match='ingok'>
    三.4. ['ingok']
    三.5. None
    三.6. None
    三.8. <re.Match object; span=(3, 9), match='ingkin'>



```python
regex=re.compile(r"\b\w+(?<=ing)")
# 为什么只有 ing 能匹配出来！？我们发现跟上面的不同点是因为， = 号前面加了 < ，
# 根据我们前面的链接说明可知该匹配符是反向的，就是说，当我们能匹配 ing 时，
# 如果没有 < 的情况下，我们是整个 (?=ing) 是落在 i 这个字符上的 （详情可以参考第三点），
# 而存在 < 时，匹配到 (?<=ing) 时是落在 g 这个字符的。
# 所以在以下这个例子， \w+(?<=ing) 中的 \w+ 是从 \b (符号一开始) 到 g 这个字符，所以 \w+ 为 ing
string="ingcoo"
print("四.1. "+str(regex.match(string)))

string="co,ingok"
print("四.2. "+str(regex.match(string)))


string="co,ingok"
print("四.3. "+str(regex.findall(string)))
# 可以看到 BB 是不包含在 \w+ 里面来的
string="co,ingok,dingBB,OKing"
print("四.4. "+str(regex.findall(string)))

string="cooingkin"
print("四.6. "+str(regex.match(string)))

regex=re.compile(r"\b(?<=ing)\w+")
# 为什么匹配不出任何东西？因为 \b 的出现，ing 不是出现在单词的一开始，所以无法匹配
string="cooingkin"
print("四.8. "+str(regex.search(string)))


regex=re.compile(r"(?<=ing)\w+")
# 当去掉 \b 时是可以匹配出来的
string="cooingkin"
print("四.9. "+str(regex.search(string)))

```

    四.1. <re.Match object; span=(0, 3), match='ing'>
    四.2. None
    四.3. ['ing']
    四.4. ['ing', 'ding', 'OKing']
    四.6. <re.Match object; span=(0, 6), match='cooing'>
    四.8. None
    四.9. <re.Match object; span=(6, 9), match='kin'>




```python
regex=re.compile(r"\b(?<=ing)\w+")
# 为什么这个无法匹配任何东西呢？一直看下去
string="ingcoo"
print("五.1. "+str(regex.match(string)))

string="co,ingok"
print("五.2. "+str(regex.match(string)))

string="co,ingok"
print("五.3. "+str(regex.search(string)))


string="co,ingok"
print("五.4. "+str(regex.findall(string)))

string="co,ingok,dingOK,OKing"
print("五.5. "+str(regex.findall(string)))

string="cooingkin"
print("五.7. "+str(regex.match(string)))

regex=re.compile(r"\bing(?<=ing)\w+")
# 没错，因为当匹配到 ing 时，当前指向是落在了 g 上面，
# 而之前的在 \b 与 (?<=ing) 之间是没有任何东西的，
# 就是说，在字符串开头就必须匹配出 ing 的 g，
# 而 'ing' 是没有任何在正则表达式中可以匹配的东西的，
# 所以我们无论如何，如果不加 'ing' 或者 \w+ 的话都是无法匹配出东西的
string="co,ingok,dingOK,OKing"
print("五.8. "+str(regex.findall(string)))

regex=re.compile(r"\b\w+(?<=ing)\w+")
# 那思考题来了，为什么最后一个 OKing 无法在上述表达式中匹配出来？
string="co,ingok,dingOK,OKing"
print("五.9. "+str(regex.findall(string)))
```

    五.1. None
    五.2. None
    五.3. None
    五.4. []
    五.5. []
    五.7. None
    五.8. ['ingok']
    五.9. ['ingok', 'dingOK']


```python
regex=re.compile(r"(?<=ing)\w+")
# 是不是觉得匪夷所思呢？
string="ingcoo"
print("六.1. "+str(regex.match(string)))

string="co,ingok"
print("六.2. "+str(regex.match(string)))


string="co,ingok"
print("六.3. "+str(regex.findall(string)))

string="co,ingok,dingOK,OKing,ingAi"
print("六.4. "+str(regex.findall(string)))

string="dingOK"
print("六.6. "+str(regex.match(string)))

string="OKing"
print("六.7. "+str(regex.match(string)))

string="ingAi"
print("六.7. "+str(regex.match(string)))

```

    六.1. None
    六.2. None
    六.3. ['ok']
    六.4. ['ok', 'OK', 'Ai']
    六.6. None
    六.7. None
    六.7. None




```python
regex=re.compile(r"(?<=ing)\w+")
# 其实这就是，重申一次，当你用 match 函数的时候，
# 搜索是从文本的开头开始的！！
string="ingcoo"
print("七.1. "+str(regex.search(string)))

string="co,ingok"
print("七.2. "+str(regex.search(string)))


string="co,ingok"
print("七.3. "+str(regex.findall(string)))

string="co,ingok,dingOK,OKing,ingAi"
print("七.4. "+str(regex.findall(string)))

string="dingOK"
print("七.6. "+str(regex.search(string)))

string="OKing"
print("七.7. "+str(regex.search(string)))

string="ingAi"
print("七.7. "+str(regex.search(string)))

```

    七.1. <re.Match object; span=(3, 6), match='coo'>
    七.2. <re.Match object; span=(6, 8), match='ok'>
    七.3. ['ok']
    七.4. ['ok', 'OK', 'Ai']
    七.6. <re.Match object; span=(4, 6), match='OK'>
    七.7. None
    七.7. <re.Match object; span=(3, 5), match='Ai'>


# 5. (?!pattern)

该睡觉了，不写了，晚安。

其实跟 (?=pattern) 一样嘛！

只不过是，从等于，变成了不等于而已！