---
layout: post
title:  叁拾壹- NodeJS简单代理池（合） 之 MongoDB 链接数爆炸了
date:   2020-02-16 12:01:23 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。前文再续

惊现 `MongoDB` 链接数`过多`，怎么办？

在前期写的简单代理中，放在`服务器`上跑了一天，`MongoDB` 就不断重启，一开始以为是 `MongoDB` 的事情，但后来看见不对啊， `TCP 链接数`还是猛涨，因为服务器上用 `MongoDB` 的不止 `NodeJS` 的项目，还有 `Python` 的，所以排查很麻烦。

先优化了 `Python` 的，后来发现问题还在，关了 `NodeJS` 的项目，世界马上清净了。

那就是 `NodeJS` 链接的问题了。

[廿贰-NodeJS简单代理池（承） 以及 Python 捉取网页内容](https://blog.csdn.net/BerryBC/article/details/104171524)

# 2。原

我完全根据官网 `MongoDB` 的 `NodeJS 驱动`来写的：

```js
        MongoClient.connect(that.strMGUrl, function(err, client) {
            if (!err) {
                const db = client.db(that.dbSet.dbName);
                let curFind = db.collection(that.dbSet.col).find({})
                curFind.toArray(function(err, item) {
                    client.logout();
                    client.close();
                    curFind.close();
                    if (!err) {
                        if (!!item) {
                            let arrProxyBack = [];
                            for (const objProxyInMDB of item) {
                                arrProxyBack.push(common.funObj2Str(objProxyInMDB));
                            }
                            funCB(null, arrProxyBack);
                        } else {
                            funCB(null, false);
                        };
                    } else {
                        funCB('error:in "loadEveryProxyMongodb" after find, err : ' + err);
                    };
                });
            } else {
                client.logout();
                client.close();
                funCB('error:in "loadEveryProxyMongodb" after connect, err : ' + err);
            };
        });
```

标准的按照官网链接文章来写。

结果：

![我都不知道说什么好了](/photo/InPost/31-1.png)

对，就是这个结果，明明我已经`断开`了呀！

-----

我觉得可能链接方式不对，于是不断上网找`链接`方式，直到找到：

[https://www.v2ex.com/amp/t/591315](https://www.v2ex.com/amp/t/591315)

有人说：
>正确姿势是连着不关。官方 DEMO 只是展示了一次操作完整的过程，并不是说每次都要连接和关闭。

我瞬间就崩溃了。

# 3。壹改

首先我就按连着的来呗。

```js
// 在构造类时
class cMongodbIO {

    /**
     *Creates an instance of cMongodbIO.
     * @param {Object} objDBSet
     * @memberof cMongodbIO
     */
    constructor(objDBSet) {
        const that = this;
        that.dbSet = objDBSet;
        that.strMGUrl = 'mongodb://' + that.dbSet.user + ':' + that.dbSet.pw + '@localhost:' + that.dbSet.host + '/' + that.dbSet.dbName;
        that.clientMongo = null;
        MongoClient.connect(that.strMGUrl, { poolSize: 50, keepAlive: false, reconnectTries: 100, reconnectInterval: 30, useNewUrlParser: true }, function(err, client) {
            if (!err) {
                that.clientMongo = client;
            } else {
                console.log('error:in connect to MongoDB, err : ' + err);
            };
        });
    };
    checkOnline(funCB) {
        const that = this;
        if (!that.clientMongo) {
            funCB(null, false);
        } else {
            funCB(null, true);
        }
    };
    ....
};

//主函数中

function funInit() {
    let strConfig = fs.readFileSync(path.join(__dirname, '/cfg.json'), { encoding: "utf-8" });
    let objConfig = JSON.parse(strConfig);
    let arrSaveSet = objConfig.saveSet;
    let objWebCfg = objConfig.webCfg;
    let intCfgMaxFailTime = objConfig.MaxFailTime;
    objCTLIO = new cControllerIO(arrSaveSet, intCfgMaxFailTime);
    ....
    funStarMain();

    function funStarMain() {
        objCTLIO.checkOnline(function(err, bolResult) {
            if (bolResult) {
                console.log(' 完成初始化 ');
                funGoPro();
                funVerifyProxy();
                funGoRandPro();
            } else {
                intRetry++;
                console.log(' 第 ' + intRetry + ' 次尝试启动');
                setTimeout(funStarMain, 3000);
            };
        });
    };
};
```

诶！结果好很多诶！

![立竿见影的效果](/photo/InPost/31-2.png)

在`情人节`当天看到这个结果我还是很感觉欣慰的。

但是，还是看到会有所`抬升`，为什么会这样呢！？

我思来想去，是不是`闭包`影响？导致那些 `cursor` 一直`占着不释放`？

# 3。贰改

```js
// 主程序

function funVerifyProxy() {
    objCTLIO.loadEveryProxy((err, result) => {
        console.log((new Date().toString()) + ' 列出完所有代理。');
        objCTSpy.verifyTheProxy(result, objCTLIO, (err, result) => {
            console.log((new Date().toString()) + ' 验证完所有了。');
            objCTLIO.saveTick((err, result) => {
                console.log((new Date().toString()) + ' 保存完。');
                setTimeout(() => {
                    timeToVerifyProxy();  //  <---原本这里是直接调用原函数的
                }, (1000 * 60 * 60 * (objTimeConfig.verify[0] + Math.random() * objTimeConfig.verify[1])));
            });
        });
    });
};

function timeToVerifyProxy() {
    funVerifyProxy();
};
```

原本我是直接在 `setTimeout `里面调用回自身函数，我猜测会不会是因为`闭包`的原因它一直不释放 `cursor` 的`内存`呢？

修改后看到：

![为什么还是爬升？](/photo/InPost/31-3.png)

Oh！已经好了一点！

（虽然我还是不太懂这个是不是真的没有把前期的`闭包`相关变量包进去，但是明显好了一点）

但，为什么还是有爬升呢？


# 4。肆改

再又查了好久文档之后，看到：

![timeout](/photo/InPost/31-4.png)

![noCursorTimeout](/photo/InPost/31-5.png)

因为一个英文的问题，我一致以为那个 `noCursorTimeout` 是 “没有 `cursor` 的时候 / 超时断开”，结果后来才发现，那个原来的意思是 “没有 / `cursor` 连接超时断开”

断句的问题很恐怖！


改为：

```js
        let curFind = db.collection(that.dbSet.col).find({}, { 'noCursorTimeout': false, 'timeout': true })
        curFind.toArray(function(err, item) {
            // client.logout();
            // client.close();
            curFind.close();
            if (!err) {
                if (!!item) {
                    let arrProxyBack = [];
                    for (const objProxyInMDB of item) {
                        arrProxyBack.push(common.funObj2Str(objProxyInMDB));
                    }
                    funCB(null, arrProxyBack);
                } else {
                    funCB(null, false);
                };
            } else {
                funCB('error:in "loadEveryProxyMongodb" after find, err : ' + err);
            };
        });
```

呃，我还是先放个十几个小时看看结果吧。


# 5。后事

多亏`腾讯云`有各种的监控我懒得自己建`监控`东西。

希望这次能解决问题吧，我觉得一类文章写个“起转承合”已经不想再写了，我想放弃了。

虽然其实事情还没`解决`，唉，我很懒啊....