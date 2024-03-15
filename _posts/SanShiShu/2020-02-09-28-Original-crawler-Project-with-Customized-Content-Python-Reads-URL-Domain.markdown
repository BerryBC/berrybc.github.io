---
layout: post
title:  廿捌-原爬虫项目加入客制化内容，Python 读取 URL 域名
date:   2020.02.09 23:54:55 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 把客制化内容加到原爬虫中

冲凉前刚完成客制化爬虫：

[廿柒- 客制化爬虫以及爬虫调参](https://blog.csdn.net/BerryBC/article/details/104238602)

冲完凉转瞬一想就觉得还是把他加到原爬虫不用维护两个相似的项目了，不是更好？

# 2. 在原项目上更新客制化的内容

[廿陆- Python 爬虫 异步改为同步加多进程 以及 某著名社交网站爬虫设想](https://blog.csdn.net/BerryBC/article/details/104217824)

加入增加内容。

### 2.1 首先需要做的就是读取Python域名

分解域名才是重点，这里我参考文章：

[Python从URL中提取域名](https://segmentfault.com/q/1010000009953492)

然后直接在处理链接时放入以下代码中：

```python

    def AddToDB(self, strHref,strInCurPageURL):
        # print(strHref)
        if not strHref is None:
            urlCurPageURL=urlparse(strInCurPageURL)
            strCurLoc=urlCurPageURL.scheme+'://'+urlCurPageURL.netloc
            strRealInsert=strHref
            if len(strHref)>4:
                if strHref[:4] != 'http':
                    if strHref[1] == '/':
                        strRealInsert=urlCurPageURL.scheme+':'+strHref
                    elif strHref[0] == '/':
                        strRealInsert=strCurLoc+strHref
                else:
                    strRealInsert=strHref
            bolHttps = ('http://' in strRealInsert or 'https://' in strRealInsert)
            strCleanURL=self.CleanURL(strRealInsert)
            if bolHttps:
                if not self.objMongoDB.CheckOneExisit('pagedb-Crawled', {'url': strCleanURL}):
                    dictNewPage = self.AnEmptyPageEle()
                    intDepth = len(strCleanURL.split('/'))-3
                    dictNewPage['url'] = strCleanURL
                    dictNewPage['d'] = intDepth
                    self.objMongoDB.InsertOne('pagedb-Crawled', dictNewPage)
                    # print(strCleanURL)


    def CleanURL(self, strURL):
        strRealURL = strURL
        intBQ = strURL.find('?')
        if intBQ > 0:
            strRealURL = strURL[:intBQ]
        intBQ = strRealURL.find('#')
        if intBQ > 0:
            strRealURL = strRealURL[:intBQ]
        return strRealURL

```
### 2.2 增加客制化通用项

```
在 MongoDB 中
db.tbCustomization.insertOne({'eURL':'','tag':'p','rURL':''})  // 放空，就是通杀
```

### 2.3 代码

放在全球最大同性交友平台上

[SpyTheLink](https://github.com/BerryBC/SpyTheLink)