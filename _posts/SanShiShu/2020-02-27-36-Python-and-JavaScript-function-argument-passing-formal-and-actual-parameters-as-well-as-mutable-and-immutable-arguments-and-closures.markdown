---
layout: post
title:  叁拾陆- Python 跟 JS 的 函数传入参数 '形参实参' 以及 参数变与不变 ，另外还有闭包
date:   2020-02-27 00:57:48 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1。夜深了，请入睡


最近把某程序的多线程改为单线程，祈求服务器 `CPU` 不要又要被挤爆了。

然后忽然发现一件事情，也是 `Python` 跟 `JS` 的差异。

----

# 2。传入参数

我们先先来看一段`代码`的两种不同实现：

## 2-1) Python

```python
# python
a=1
b={'t':2}
def change(c,d):
    t=d['t']
    d['t']=c
    c=t


print(a,b)
Test(a,b)
print(a,b)

```

## 2-2) JS

```js
let a=1;
let b={z:2};
function change(c,d){
    t=d.z;
    d.z=c;
    c=t;
};

change(a,b);
console.log(a);
console.log(b);
```

## 2-3) 输出结果

```bash
两个的结果是一样的
输出结果都是：

a=1
b={z:1}

为什么 a 不变而 b 的内容变了呢！？
```

其实不止 `Python` 跟 `JS`，连 `Java` 都是一样的。

为什么呢！？

因为他们传入的`参数`默认都是 `形参` 而不是 `实参`。

[通过值和通过引用传递参数 (Visual Basic)](https://docs.microsoft.com/zh-cn/dotnet/visual-basic/programming-guide/language-features/procedures/passing-arguments-by-value-and-by-reference)

是的，为什么看 `VB` 呢？因为我觉得 `VB` 挺好的。

反正他们都是传递一个 [形参](https://baike.baidu.com/item/%E5%BD%A2%E5%BC%8F%E5%8F%82%E6%95%B0/1995068?fr=aladdin) 过去（即`新建`一个`参数`再`复制`自身的内容过去）。

## 2-4) 输出结果但为什么对象可以改变内容呢？

因为基本`脚本语言`的`对象`均是一`指针`，`参数`本身只是指向某一`内存对象`的地址：

![虽然有点丑](/photo/InPost/36-1.png)

此处说明，你声明了一个函数 `change`，创建了变量 `c` 和 `d` 之后，`函数`执行完，`c` 跟 `d` 变量均已`销毁`，但你更改了变量 `d` 指向的 `z`，所以同时查看变量 `b` 所指向的 `z` 内容时也是改变了的。

----

# 4。为什么我那么喜欢 JS 呢？

因为他`闭包`好玩啊！

我们来看看我们修改一下上面的代码时会发生什么事？

## 4-1) Python

```python
a=1
b={'z':2}
def change():
    t=b['z']
    b['z']=a
    a=t


print(a,b)
change()
print(a,b)

```

Python输出：

```bash
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in change
UnboundLocalError: local variable 'a' referenced before assignment
```

人家会告诉你，你根本没定这个`变量`啊！我不知道有这回事！你发给邮件过来跟我确认！

## 4-2) JS

```js
let a=1;
let b={z:2};
function change(){
    t=b.z;
    b.z=a;
    a=t;
};
change();
console.log('a:'+ a);
console.log('b.z:'+ b.z);
```

JS 输出：

```bash
a:2
b.z:1
```

## 4-3) 所谓的闭包嘛

其实很简单。

我很困了，先睡了。


----

# 5。尾声

其实如果如果细心的人会发现，没有了**3**。

算了，反正没细心的朋友。