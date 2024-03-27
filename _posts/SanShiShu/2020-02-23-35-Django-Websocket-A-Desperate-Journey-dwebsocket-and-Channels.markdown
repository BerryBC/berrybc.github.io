---
layout: post
title:  叁拾伍- Django Websocket 绝望之旅（dwebsocket 以及 channels）
date:   2020-02-23 22:10:34 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。Websocket

在思考着如何在 `Django` 呈现 `sklearn` 学习的`进度`给用户看是，提了这样一个问题：

[Django 怎么返回进度？](https://bbs.csdn.net/topics/395917059)

然后本来也觉得`轮询`更新进度是最好的（因为同一个服务器只允许一个建模进度）。

但想起`微信小程序`也是允许 `websocket` 的，那其实`长连接`还是得用的，那就这样吧，来学！

# 2。绝望的 dwebsocket

我是看到有 [文章](https://www.cnblogs.com/polly-ling/p/10173388.html) 说 `dwebsocket` 比 `channels` 方便，所以我奔着方便去做，首先试用了 `dwebsocket`，看着不同的文章 ：

[Django通过dwebsocket实现websocket](https://blog.csdn.net/weixin_39726347/article/details/88045752)

[django中使用dwebsocket 后端实时进行和前端通讯](https://www.jianshu.com/p/d7a960dd13e4)

以及官网：

[duanhongyi/dwebsocket](https://github.com/duanhongyi/dwebsocket)

我觉得快崩溃了...

巨多神奇的东西，我不知道到底是我错了还是配置错了，甚至最崩溃的就是在 `django` 里面的 `settings.py` 里面 `import dwebsocket` 是不可以在文件的头的！！！！！

只能配置在配置文件的下面：

![下方](/photo/InPost/35-1.png)

就因为他可能会把 `SECRET_KEY` 给冲没了！

我已经绝望透顶了！

就连简单的：

```python

@csrf_exempt
@accept_websocket
@LuserCtrl.decoratedPageCheckAdm
def wsCreatSklearnModel(request):

    intN=0
    print('connect')
    print(request)
    request.websocket.send('开始'.encode('utf-8'))
    time.sleep(1)
    request.websocket.send(str(intN).encode('utf-8'))
    intN+=1
    request.websocket.send(str(intN).encode('utf-8'))
    intN+=1
    request.websocket.send(str(intN).encode('utf-8'))
    intN+=1
    request.websocket.send(str(intN).encode('utf-8'))
    intN+=1
    request.websocket.send('结束'.encode('utf-8'))
    print(request.websocket)
```

我已经不想玩这个了，崩溃了。

# 3。channels

明天上班了，我觉得我现在还是放弃了...

以后有机会再战江湖吧。

明晚可以尝试。