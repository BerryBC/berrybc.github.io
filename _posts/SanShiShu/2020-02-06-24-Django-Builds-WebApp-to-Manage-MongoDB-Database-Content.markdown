---
layout: post
title:  廿肆-Django 建设 WebApp 管理 MongoDB 数据库内容
date:   2020.02.06 13:43:20 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---




# 1. 缘起

前期爬了很多`数据`，我们需要有一个`交互界面`去判断爬取的数据`归属情绪`是哪种。

因为程序是部署在`云服务器`上的，所以当然就是建立一个 `WebApp` 去交互，而我前期又帮公司做过一个 `Django` 项目的，所以我觉得这个可以直接拿来用。

# 2. Django

当然我们必须得有文档：

[Django包](https://docs.djangoproject.com/zh-hans/3.0/intro/tutorial01/)

有了文档我们就无所不能了！

这个我也是直接拿之前做好的，就没什么好说了。

# 3. MongoDB 链接事宜

前期在公司是用 `MySql` 的，所以底层链接全部要做过，这个时候发现两个比较重要的问题。

### 3.1 MongoDB 根据 ID 查找

我也不懂为什么这么难，其实 `MongoDB` 跟 `JS` 才是绝配，可既然无聊如我也只能用 `Python` 做。

但 `Python` 根据 `MongoDB` 记录的 `_id` 查找是无法很方便的直接查找的，需要 `import` 一个 包并进行转换


```python
from bson.objectid import ObjectId
articles.find_one({'_id':ObjectId('50f0d76347f4ec148890ef1e')})
```

[python mongodb根据_id查询数据](https://blog.csdn.net/happybai11/article/details/81382041)

唉呀，好麻烦啊。

### 3.2 Python 导入 MongoDB 时间问题

我觉得 `Python` 真的是很麻烦，说它是`脚本语言`，可是一点都不友好，连个`日期`都要转来转去：

[Python 插入 MongoDB 时间](https://blog.csdn.net/fiery_heart/article/details/90229610)

```python
import pymongo
from dateutil import parser
dateStr = "2019-05-14 01:11:11"
myDatetime = parser.parse(dateStr)
client = pymongo.MongoClient(host="127.0.0.1", port=27017)
db = client["test"]
db.ceshi.insert({"date": myDatetime})
client.close()
```

# 4. Nginx 反向代理

这个我深有体会，之前的云服务器开了4个 `NodeJS`、一个 `JSP`、一个 `RServe` 服务，都是用 `Nginx` 做`反向代理`的。

但问题是，我已经忘了怎么配置了。

所以只能找小文章：

[Nginx安装及配置反向代理](https://www.cnblogs.com/ZhuChangwu/p/11150437.html)

其实最麻烦的我觉得是配各种`二级路径`的时候，`Nginx` 功能是很强大，但是同时也很麻烦。

# 5. Supervisor

这个我今天早上已经专门写了个文章吐槽了：

[廿叁-Supervisor 配置 Django 被 Sqlite3 在 LD_LIBRARY_PATH 上坑了](https://blog.csdn.net/BerryBC/article/details/104193403)

我能怎么样？我也很绝望哦。

# 6. 技术混搭的弊端

这次我深刻的体会到，之前面试所说的：
>一个项目组使用不同的技术是一个噩梦，会造成很多灾难性的后果。


的确，以前总是觉得能用就行，但当你不同的技术来做一件事情时，**工作量暴增、可维护性下降、重复写相同功能**。

例如数据库一直在用 `MySQL` 的，如果是转个 `SQLServer` 还算好，改一下接口和 `SQL` 片段就可以。忽然转 `MongoDB` 或者其他`非关系型数据库`的，连`逻辑`也要改动。

还有前期很多库都是使用 `NodeJS` 写的，一转 `Python` 连重用都不行了，还得重新写。

# 7.版本迭代的可怕之处

其实以前不懂为什么说，换了一个人、版本更新宁愿 **重写** 也不要在原版本上 **修改**。

世界在进步，时代在变革。

就像原先的版本你的库还是用函数的形式给其他人应用的，忽然间改成类的形式，完全就乱套了。

也有封装不好的原因，但带来的一个深切的教训就是。

除非是试验项目，不然尽量思考清楚再开始做。