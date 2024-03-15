---
layout: post
title:  廿玖-Python 多线程之与 NodeJS 不同之处
date:   2020.02.11 01:22:27 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 多线程与停等

一直写着 `JS`，所以觉得，如果你在 `Python` 用一个 `threading.Timer` 的话，就像 `JS `的 `setTimeout` 其余东西都是会继续运行，而不是会等这个 `threading.Timer` 运行完之后才继续进行。

但结果却是，他其实是放进同一个`线程`，等一个 `threading.Timer` 完成之后再完成第二个 `threading.Timer` ，你必须新建一个`线程`。

就是所说的`异步`与`同步`之迷惘。

![alt text](/photo/InPost/29-1.png)

![alt text](/photo/InPost/29-2.png)

# 2. 参考文章

[Python多任务(利用threading创建线程时传入参数--args参数)](https://blog.csdn.net/wei18791957243/article/details/83995823)

[python threading.enumerate()查看线程数量（多线程二）](https://blog.csdn.net/zhujuntangxiaojuan/article/details/99182036)

python线程数量与线程池[添加链接描述](https://blog.csdn.net/weixin_33922670/article/details/88004890)

# 3. 实验

实验的函数为：

```python
def TestThreading(t):
    time.sleep(6)
    print('Now It\'s :'+ str(t))
    # print('  Now enumerate is :'+str(len(threading.enumerate())))
    print('  Now activeCount is :'+str(len(threading.enumerate())))
    TT=threading.Timer(2, TestThreading(t+1))
    TT.start()
```

### 3.1 实验1

调用函数为：

```python
threading.Timer(2, TestThreading(0)).start()
threading.Timer(2, TestThreading(100)).start()
threading.Timer(2, TestThreading(200)).start()
threading.Timer(2, TestThreading(300)).start()
```

输出为：

```
Now It's :0
  Now activeCount is :1
Now It's :1
  Now activeCount is :1
Now It's :2
  Now activeCount is :1
Now It's :3
  Now activeCount is :1
Now It's :4
  Now activeCount is :1
Now It's :5
  Now activeCount is :1
```

明显看出，其实`线程`还是只有`一个`，只有当其中`一个线程`完全完成了自己的任务 ( `TestThreading(0)` )后才会调用下一个任务 ( `TestThreading(100)` )。

### 3.2 实验2

调用函数为：

```python
def Go(t):
    TestThreading(t)

threading.Thread(target = Go,args=(0,)).start()
threading.Thread(target = Go,args=(100,)).start()
threading.Thread(target = Go,args=(300,)).start()
```

输出为：

```
Now It's :300
Now It's :100
  Now activeCount is :4
  Now activeCount is :4
Now It's :0
  Now activeCount is :4
Now It's :301
  Now activeCount is :4
Now It's :101
  Now activeCount is :4
Now It's :1
  Now activeCount is :4
Now It's :302
  Now activeCount is :4
Now It's :102
  Now activeCount is :4
Now It's :2
  Now activeCount is :4
Now It's :303
  Now activeCount is :4
Now It's :103
Now It's :3
  Now activeCount is :4
  Now activeCount is :4
Now It's :304
  Now activeCount is :4
Now It's :104
  Now activeCount is :4
Now It's :4
  Now activeCount is :4
Now It's :305
  Now activeCount is :4
Now It's :105
  Now activeCount is :4
Now It's :5
  Now activeCount is :4
```

# 4. 我真的讨厌异步与同步

不同`语言`之间的`特性`差别真的差很远，所以我觉得有时候，我真的不想编程，我宁愿做一个自由自在的人，认真的活着。

了解语言之间的差别，才是认真的事。


# 5. 为什么我会找到这个问题？

其实我本来是不知道的，但我在做爬虫的时候发现，执行完一次之后，为什么几个小时也不重新开始爬新的页面？

因为我是好像 `NodeJS` 那样设定`时间`的，之前在 `Django` 也是直接用 `threading.Timer` 的，为什么会产生等待那么长时间才执行下一次程序 ( 我也不想细看 `Django` 的`线程`是怎么做的了 ) 呢。答案就在这里，我讨厌`异步`。
