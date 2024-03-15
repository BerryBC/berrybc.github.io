---
layout: post
title:  廿叁-Supervisor 配置 Django 被 Sqlite3 在 LD_LIBRARY_PATH 上坑了
date:   2020.02.06 12:06:55 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1. Django简单运行可以

前期说想写个 Web App 做管理以及发布 API。

[廿贰-NodeJS简单代理池（承） 以及 Python 捉取网页内容](https://blog.csdn.net/BerryBC/article/details/104171524)

写好后在 Github 直接 Clone 过去云服务器运行，发现服务器上有好多依赖包都没安装好，依次安装完：

```bash
sudo pip3 install Django django_extensions python-dateutil
```

后，直接

```bash
python3 manage.py runserver --noreload 0.0.0.0:1039
```

是可以运行，挺顺畅的。

# 2. 托管程序

想着想着直接运行那不行啊，客户端一退出就不行了，必须得托管。

那托管应用哪家强！？当然推选 supervisor ！

（其实主要原因是我不知道其他哪些好）

# 3.噩梦开始

然后忽然脸色一黑，为什么上不了？我直接运行是可以的，为什么上不了？

我把防火墙 Firewalld 关掉，还是上不了，Nginx 的反向代理配置我是正常的呀。

而且更重要的是，无论我在 Supervior 里面配置：

```ini
loglevel=debug
```

什么的就是不出 Log ，我也找不到原因啊。

# 4.曙光

在百般无奈的情况下，我无意中尝试输入了

```bash
sudo python3 manage.py runserver --noreload 0.0.0.0:1039
```

诶！显示 sqlite3 版本低！

为甚没有 sudo 可以，有 sudo 识别不了更新版本的 sqlite3 呢！？

# 5.更新sqlite3

因为 Django 是需要 sqlite3.8 以上的版本，千辛万苦找到一个很好的文章

[更新CentOS的SQLite版本](https://www.cnblogs.com/hupingzhi/p/11300049.html)

我明明已经更新好了呀，我看到版本号都是高于3.8的了呀！为什么还是不行？

# 6.LD_LIBRARY_PATH

思来想去，报错是报 Python Import 那个 Sqlite3 的时候 Import 的包的版本问题。

在

```bash
[Berry@tencentcloud ~]$ sudo python3
>>> from sqlite3 import dbapi2 as Database
>>> Database.sqlite_version_info
(3, 7, 7)

[Berry@tencentcloud ~]$ python3
>>> from sqlite3 import dbapi2 as Database
>>> Database.sqlite_version_info
(3, 27, 2)
```

# 7.为什么会这样了！？

在几个小时候，我终于找到了一个文章：

[LD_LIBRARY_PATH](https://www.cnblogs.com/ZhuChangwu/p/11150437.html)

里面说了：
> 本人在/etc/environment中设置了环境变量：
>
>JAVA_HOME=/opt/java
>ORACLE_HOME=/home/user2/
>LD_LIBRARY_PATH=/usr/lib:/usr/local/lib
      正常情况下执行没有问题，但使用sudo XXXXXXX的时候，其他变量都好使，除了LD_LIBRARY_PATH；
>经探索，找到原因：
>
>据说因为安全原因，Linux系统做了限制。
>LD_LIBRARY_PATH not loading from .profile nor /etc/environment


愤怒！

还看到了下面这一句：

>我们可以在 ~/.bashrc 或者 ~/.bash_profile 中加入 export 语句，前者在每次登陆和每次打开 shell 都读取一次，后者只在登陆时读取一次。我的习惯是加到 ~/.bashrc 中，在该文件的未尾，可采用如下语句来使设置生效：
>export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
>修改完后，记得关掉当前终端并重新打开一个新的终端，从而使上面的配置生效。


就是说，你修改了 ~/.bashrc 后，只能是打开当前 Shell 的当前用户才能配置那个共享库。

而 Supervisor 是在没有登陆前使用的，更何况 sudo 是不引入 LD_LIBRARY_PATH 的。

我不是通过正常途径安装 sqlite3 的，而是通过编译方式安装的，我该怎么办呢？

# 8.天无绝人之路

看过很多资料，有的说可以重新编译，但我觉得这很麻烦嘛，于是我找到了一个更好的办法。

[linux 环境变量设置方法总结（PATH / LD_LIBRARY_PATH）](https://blog.csdn.net/qq_35608277/article/details/84982318)

这下就完事了。

直接运行，爽快。

# 9.遇事需冷静

所有的事情，碰到了，我们没有办法逃避，但是我们可以勇敢面对。

例如死亡。