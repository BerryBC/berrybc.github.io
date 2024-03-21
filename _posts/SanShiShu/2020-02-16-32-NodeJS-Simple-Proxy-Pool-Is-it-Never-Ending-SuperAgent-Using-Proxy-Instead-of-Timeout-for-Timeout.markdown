---
layout: post
title:  叁拾贰- NodeJS简单代理池（有完没完？） 之 SuperAgent 使用代理不是 Timeout 的 Timeout
date:   2020-02-16 23:11:28 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。绝望

当我以为`上篇`已经完了的时候，跟女朋友在全城封闭期间偷偷冒险外出吃个麦当劳回来后发现，还是一样 `TCP` 数慢增：

![此处应该是绝望的表情](/photo/InPost/32-1.png)

前文链接：

[叁拾壹- NodeJS简单代理池（合） 之 MongoDB 链接数爆炸了](https://blog.csdn.net/BerryBC/article/details/104339193)

# 2。排查

直接上`服务器`看到底是什么连的那么多个 `TCP连接`，不看不知道，一看吓一跳：

```bash
[Berry@tencentcloud ~]$ netstat -tnlpoa|grep 9999|wc -l
399
[Berry@tencentcloud ~]$ netstat -tnlpoa|grep 27017|wc -l
137
```

我咧个去..为什么9999`端口`会比27017`端口`多那么多 `TCP 连接数`？

细致看一下：

```bash
tcp        0      0 172.17.0.7:33426        163.204.244.111:9999    ESTABLISHED 2480/node /home/Ber  off (0.00/0/0)
tcp        0      1 172.17.0.7:48676        216.140.201.110:9999    SYN_SENT    2480/node /home/Ber  on (1.49/4/0)
tcp        0      1 172.17.0.7:47066        58.253.156.17:9999      SYN_SENT    2480/node /home/Ber  on (13.57/5/0)
tcp        0     78 172.17.0.7:44104        112.111.217.217:9999    ESTABLISHED 2480/node /home/Ber  on (65.50/15/0)
tcp        0      0 172.17.0.7:33446        182.35.85.137:9999      ESTABLISHED 2480/node /home/Ber  off (0.00/0/0)

```
好吧，我承认我不懂什么意思，于是搜索引擎了一下：

[linux下netstat --timers / -o详解及keepalive相关](http://www.mamicode.com/info-detail-1665739.html)

我崩溃了，有些`连接`连了整整5、6分钟都还在连。

但是我代码明明写了 `Timeout` 是那十几秒的呀！

# 3。代码

```js
        let funGoRandSpy = (strRandProxy, funCB) => {
            // 上网看到很多高匿的代理都是9999端口的，就尝试一下随机测试网上所有9999端口呗
            // 排查端口指令 sudo netstat -tnlpoa|grep 9999|wc -l
            let strProxy = 'http://' + strRandProxy + ':9999';
            // console.log('  testing :' + strProxy);
            // 其中 intTimeout 为 3000 ms
            request.get('https://www.baidu.com').timeout({ response: that.intTimeout, deadline: that.intTimeout * 3 }).use(superagentCheerio).proxy(strProxy).set(that.objHeader).then((res) => {
                let objProxyForSave = { u: strRandProxy, p: '9999' };
                objCTLSave.saveOneProxy(objProxyForSave, () => {});
                console.log('  ' + strProxy + '  测试居然通过！');
                funCB(null, true);
            }).catch((err) => {
                funCB(null, true);
            });
        };
```

为什么已经反馈了`超时`，你还一直在等！？我的心在等待，永远在等待？

我上网找了很久，也找了 [SuperAgent 官网](http://visionmedia.github.io/superagent/#timeouts)，没说这是为啥啊，那这是为啥！？


# 4。暂时解决

暂时的`解决方案`是，既然它`连接`最大是10分钟的，我决定把这个`永续循环`放到10分钟之后再执行，那该可以了吧？

明天睡醒之后再看结果。

我估计可以解决。

# 5。有没有人知道为什么啊！！

有没有`大神`请告诉我怎么破？

我感觉，需要从 `服务器 `上面下手（我的是 `Centos 7`）。 [SuperAgent 官网](http://visionmedia.github.io/superagent/#timeouts)上也没有类似的解答，我绝望了。

好了，明天上班，晚安。