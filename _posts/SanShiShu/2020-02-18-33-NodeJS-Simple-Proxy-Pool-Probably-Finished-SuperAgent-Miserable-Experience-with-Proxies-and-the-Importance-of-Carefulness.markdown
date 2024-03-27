---
layout: post
title:  叁拾叁- NodeJS简单代理池（估计完了吧）SuperAgent 使用 Proxy 的惨痛经历 及做事要细心
date:   2020-02-18 00:03:11 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 零。 绝望

我不懂多年前`年幼`的我是怎么想的：

[拾肆-NodeJS简单代理池（起）](https://blog.csdn.net/BerryBC/article/details/86700357)

那时候我有没有测试过？

有一个致命的错误。

导致这样：

[叁拾贰- NodeJS简单代理池（有完没完？） 之 SuperAgent 使用代理不是 Timeout 的 Timeout](https://blog.csdn.net/BerryBC/article/details/104350570)

其实没想法可以直接看最底下。

那是结论。

# 壹。继续排查 TCP 连接数过多

![到底是为什么！？](/photo/InPost/33-1.png)

我十分迷惑，于是查 `netstat`，以下是过程：

```bash
[Berry@tencentcloud ~]$ sudo netstat -tnlpoa

tcp        0      0 172.17.0.7:50136        182.54.207.74:80        ESTABLISHED 10418/node /home/Be  off (0.00/0/0)
tcp        0      1 172.17.0.7:51250        49.76.80.223:80         SYN_SENT    10418/node /home/Be  on (12.42/6/0)
tcp        0      0 172.17.0.7:53422        73.239.197.175:80       ESTABLISHED 10418/node /home/Be  off (0.00/0/0)
tcp        0      0 172.17.0.7:50170        122.192.175.180:9999    ESTABLISHED 10418/node /home/Be  off (0.00/0/0)
tcp        0      1 172.17.0.7:57588        185.100.15.48:80        SYN_SENT    10418/node /home/Be  on (11.39/5/0)
tcp        0      1 172.17.0.7:36296        223.242.224.59:80       SYN_SENT    10418/node /home/Be  on (61.76/6/0)
tcp        1      0 127.0.0.1:39800         127.0.0.1:52107         CLOSE_WAIT  1490/python3         off (0.00/0/0)
tcp        0      0 172.17.0.7:43576        85.196.183.162:80       ESTABLISHED 10418/node /home/Be  off (0.00/0/0)
tcp        0      0 172.17.0.7:47874        180.254.227.43:80       ESTABLISHED 10418/node /home/Be  off (0.00/0/0)



[Berry@tencentcloud ~]$ sudo netstat -tnlpoa|grep 80|wc -l
311

# ----- mongo:

> db.tbProxy.find({p:null})
{ "_id" : ObjectId("5e46b85a7e384044fb93c7a2"), "u" : "183.166.103.182", "p" : null, "ft" : ISODate("2020-02-14T15:10:18.526Z"), "fail" : 0 }
{ "_id" : ObjectId("5e46b85a7e384044fb93c7a3"), "u" : "82.137.244.59", "p" : null, "ft" : ISODate("2020-02-14T15:10:18.526Z"), "fail" : 0 }
{ "_id" : ObjectId("5e46b85a7e384044fb93c7a4"), "u" : "110.78.154.71", "p" : null, "ft" : ISODate("2020-02-14T15:10:18.527Z"), "fail" : 0 }

> db.tbProxy.find({p:null}).count()
3426

```

我看过端口为 `27017 (MongoDB)` 以及 `9999 (某高匿代理常用端口)` 。

但发现，原来引起多的主要来自于`80`端口（占了一大半）。

What！？

# 贰。翻查代码

我保存`代理`的时候主要是用以下的`代码`：

```js
            request.get(strWebURL).set(that.objHeader).timeout({ response: that.intTimeout, deadline: that.intTimeout * 2 }).use(superagentCheerio).then((res) => {
				....
                let arrProxyList = strTableContent.match(/\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}[\n\s]*\d{1,4}/g);
                for (const strOneProxy of arrProxyList) {
					....
                }
                // console.log('完成 ' + strWebURL + ' 的捉取，捉取到 ' + arrProxyList.length)
				....
            }).catch((err) => {
                //上不了就上不了了，不报错了。
				....
            });
        };

```

问题来了，我看到有很多`代理记录`根本是没有端口的 (`MongoDB` 中 `p` 为 `null` 项)。

然后我逐个网站看过，发现有些网站的`免费代理`是这样的：

![有些](/photo/InPost/33-2.png)

![可怕](/photo/InPost/33-3.png)

而代码：

```js
t.match(/\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}[\n\s]*\d{1,4}/g)
```

是匹配不到第二种的，匹配第二种需要在中括号之中加上 `:`，如下：

```js
t.match(/\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}[\n\s:]*\d{1,4}/g)
```

就是干活不仔细的原因，唉。

# 叁。SuperAgent 使用 Proxy

当时我是脑抽了，写了下面这段代码：

```js
const superagentCheerio = require('superagent-cheerio');
const request = require('superagent');

...
    verifyTheProxy(arrProxy, ctlIO, funCB) {
        let that = this;
        let funCheck = function(item, funCB) {
            let strProxy = 'http://' + item;
            request.get('https://www.baidu.com').timeout({ response: that.intTimeout, deadline: that.intTimeout * 3 }).use(superagentCheerio).proxy(strProxy).set(that.objHeader).then((res) => {
				....
				// 更新代理，确认为可以
            }).catch((err) => {
				....
            });
        };
        async.eachLimit(arrProxy, 6, funCheck, (err) => {
            ....
        });
    };

```

那是有问题么？

本来我不觉得有任何问题，直到搜 `SuperAgent` 的官网。

压根儿没有 `proxy` 这个！

然后搜 `SuperAgent` 和 `Proxy`，搜到了这个：

[superagent-proxy](https://www.npmjs.com/package/superagent-proxy)

----


我有问题么？

我先直接用 `NodeJS` 试一下

```js
G:\Programme\Working\NodeJS\Working\Proxy_Pool>node
> const superagentCheerio = require('superagent-cheerio');
undefined

> (node:872) ExperimentalWarning: The http2 module is an experimental API.
const request = require('superagent');
undefined

> let intTimeout=10000;
undefined

> let strProxy='http://122.xx.xx.xx:xxxx';
undefined

> request.get('https://www.baidu.com').timeout({ response: intTimeout, deadline: intTimeout * 3 }).use(superagentCheerio).proxy(strProxy).then((res) => {console.log(res)})
TypeError: request.get(...).timeout(...).use(...).proxy is not a function
```

为什么？

为什么 `not a function` ！？

那就是其实我之前在 `NodeJS` 上的验证代理能否连接一直都是错的验证！？

那就是之前无论怎么连接都其实直接去到 `catch` 里面！？

用 `VSCode` 断点试一下。

还真是....

----

我..那个..

我马上加了这个：

`require('superagent-proxy')(request);`

```js
const superagentCheerio = require('superagent-cheerio');
const request = require('superagent');
// 加了这个，就这行！！！！！！
require('superagent-proxy')(request);
...
    verifyTheProxy(arrProxy, ctlIO, funCB) {
        let that = this;
        let funCheck = function(item, funCB) {
            let strProxy = 'http://' + item;
            request.get('https://www.baidu.com').timeout({ response: that.intTimeout, deadline: that.intTimeout * 3 }).use(superagentCheerio).proxy(strProxy).set(that.objHeader).then((res) => {
				....
				// 更新代理，确认为可以
            }).catch((err) => {
				....
            });
        };
        async.eachLimit(arrProxy, 6, funCheck, (err) => {
            ....
        });
    };

```

# 肆。人生需要努力

但你会发现无论你多努力，天资不足还是不足的，算了吧。