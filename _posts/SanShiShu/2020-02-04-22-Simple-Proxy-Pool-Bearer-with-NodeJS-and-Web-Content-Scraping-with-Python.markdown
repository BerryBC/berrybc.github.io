---
layout: post
title:  廿贰-NodeJS简单代理池（承） 以及 Python 捉取网页内容
date:   2020.02.04 17:12:47 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1.关于 NodeJS 简单代理池

前面基本建设可以参考以前的文章：

[拾肆-NodeJS简单代理池（起）](https://www.jianshu.com/p/f0177911a9fb)

[拾捌-NodeJS简单代理池（转）](https://www.jianshu.com/p/084d5a6d104c)

### 1.1 本次更新综述

本次只是在看到很多代理服务器上面，高匿代理都是以9999端口开放的，思考是否可以自己爬出代理？

### 1.2 新增函数

其实思路很简单，就是随机一个 IP ，然后如果该 IP 能顺利通过代理验证就直接放进代理池上面，完整代码如下：

```js
reqRandPro(objCTLSave, funCB) {
    const that = this;
    // 处理函数
    let funGoRandSpy = (strRandProxy, funCB) => {
        // 上网看到很多高匿的代理都是9999端口的，就尝试一下随机测试网上所有9999端口呗
        let strProxy = 'http://' + strRandProxy + ':9999';
        request.get('https://www.baidu.com').timeout({ response: that.intTimeout, deadline: that.intTimeout * 3 }).use(superagentCheerio).proxy(strProxy).set(that.objHeader).then((res) => {
            let objProxyForSave = { u: strRandProxy, p: '9999' };
            // 通过之后就进行保存
            objCTLSave.saveOneProxy(objProxyForSave, () => {});
            console.log('  ' + strProxy + '  测试居然通过！');
            funCB(null, true);
        }).catch((err) => {
            funCB(null, true);
        });
    };
    let arrRandProxy = [];
    for (let intJ = 0; intJ < 100; intJ++) {
    	// 生成100个随机 IP
        let strTestingIP = (Math.floor(Math.random() * 255)).toString() + "." + (Math.floor(Math.random() * 255)).toString() + "." + (Math.floor(Math.random() * 255)).toString() + "." + (Math.floor(Math.random() * 255)).toString();
        arrRandProxy.push(strTestingIP);
    }
    // 异步测试
    async.eachLimit(arrRandProxy, 5, funGoRandSpy, (err) => {
        funCB(err, true);
    });
}
```

### 1.3 再之后

其实我也想不出什么其他办法可以了，准备吃饭了。

# 2.关于 Python 捉取网页内容

在前期的内容上，思来想去，如果根据捉取的链接再重新捉取内容好像很麻烦，思来想去，还是觉得直接在捉取的时候直接捉内容比较好吧。


### 2.1 依靠是什么？

[Beautiful Soup 文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)

所谓有事没事看文档，是做学术的人应该有的态度。


### 2.2 我做了什么？

我前期已经做倒请求了每一个页面：

[廿壹-爬 URL 、Python 异步 、Supervisor 安装配置等事宜](https://www.jianshu.com/p/07e651969bd5)

我只是在爬链接时顺便把所有 \<p\> 的内容加起来而已，函数如下：

```python
def AddPContent(self, arrTagP):
    # print('   成功爬了一个网站')
    strPContent = ''
    for eleP in arrTagP:
        strPContent += eleP.get_text()+' '
    dictNewContent = self.AnEmptyContentEle()
    dictNewContent['ct']=strPContent
    if len(strPContent)>20:
        # print('   成功爬了一个网站')
        self.objMongoDB.InsertOne('sampledb', dictNewContent)
    # else :
    #     print('   字数不够不保存')
```

这样我就能把内容都放到数据库中了！很开心！


### 2.3 后续要干嘛？

其实我觉得我还有三个东西要做，不知道假期结束前能不能完成：

- [ ] 做一个 Django控制以及判断情绪是否正确
- [ ] 根据关键字生成决策树，看对一篇文章情绪是正面还是负面
- [ ] 捉取数据时看能否捉取 JS 内容