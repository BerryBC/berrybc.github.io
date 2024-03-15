---
layout: post
title:  捌-通过Git上传项目到服务器并部署Tomcat从而开启项目
date:   2018.12.15 16:51:38 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



是不是题目很霸气！？我也觉得是。

反正要实现的功能是：
1. 通过本地`Git`上传项目到`GitHub`；
2. 通过`GitHub`把项目拉到服务器端；
3. 在服务器端部署`Tomcat`；
4. 用`Nginx`的反向代理把`Tomcat`代理到网站的二级目录下。

*P.S :其实我不是很喜欢仓木麻衣。*

# 一、Git上传项目

现在时间 -- 2018-12-15 14:08

其实`Git`的使用在[菜鸟乐园](http://www.runoob.com/git/git-tutorial.html)以及[廖雪峰](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)的网站都有，个人觉得廖老师的比较详尽，但是查询某些指令的使用的话还是菜鸟乐园的好些。

本地创建`版本仓库`、`添加文件`、首次`提交`指令如下：

```git
$ git init
$ git add *
$ git commit -m "The First 完成"
```

然后你该上`Github`上新建一个库然后上传咯哟~

上传的指令其实在`Github`新建完之后是会有提示的：

```git
git remote add origin git@github.com:BerryBC/QuestionsDeduplication.git
git push -u origin master
```

上传完就好了呀~

打开`Github`就发现你的代码就躺在上面了：[例如](https://github.com/BerryBC/QuestionsDeduplication)。

# 二、在服务器端通过Git拉取代码

首先你的服务器必须先安装`Git`！如果你服务器的软件库没有`Git`是比较麻烦的，自己编译是十分压抑的一件事情。

```bash
[Berry@berry ~]$ mkdir QuestionsDeduplication
[Berry@berry ~]$ cd QuestionsDeduplication
[Berry@berry QuestionsDeduplication]$ sudo git clone git@github.com:BerryBC/QuestionsDeduplication.git
Cloning into 'QuestionsDeduplication'...
remote: Enumerating objects: 37, done.
remote: Counting objects: 100% (37/37), done.
remote: Compressing objects: 100% (27/27), done.
remote: Total 37 (delta 0), reused 37 (delta 0), pack-reused 0
Receiving objects: 100% (37/37), 30.70 MiB | 19.20 MiB/s, done.
[Berry@berry QuestionsDeduplication]$ ls
QuestionsDeduplication
[Berry@berry QuestionsDeduplication]$ cd Que*
[Berry@berry QuestionsDeduplication]$ ls
build  README.md  src  WebContent
[Berry@berry QuestionsDeduplication]$ pwd
/home/Berry/QuestionsDeduplication/QuestionsDeduplication
```

看起来都觉得很麻烦。

# 三、弄一只可爱的Tomcat

现在有点慌张，因为我服务器好像没装`Java`..很慌张！

需要找教程！人们都说，先`Google`找不到再`Baidu`，但是，身为墙内人我觉得还是先`百度`再说，找到一个教程不错的# [centos7+tomcat部署JavaWeb项目超详细步骤](https://www.cnblogs.com/xiaohu1218/p/7233162.html)。

注意在修改`/etc/profile`前先确定当前安装`Java`的目录，因为在教程里面添加环境变量时：

```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

而实际：

```bash
[Berry@berry QuestionsDeduplication]$ cd /usr/lib/jvm
[Berry@berry jvm]$ ls
java  java-1.8.0  java-1.8.0-openjdk  java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64  java-openjdk  jre  jre-1.8.0  jre-1.8.0-openjdk  jre-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64  jre-openjdk
```

注意：`JAVA_HOME`是有不同的。

--------


至此，已经完成了。

# 四、用Tomcat运行我们的项目

考虑到我们以后我们可能会同时运行两个或三个`Java Web Apps`，所以需要找找`Tomcat`多开的窍门。

然后发现很麻烦，于是放弃了。

------

我觉得，把`Tomcat`当成一个系统服务以自启动还是挺好的，因为`NodeJS`那些程序我可以用`PM2`来管理，但`Tomcat`我还真不知道怎
么管理，先[做成服务](https://blog.csdn.net/yuzfengxu/article/details/79271174)。

-----------------------

现在时间 -- 2018-12-15 16:12

我很绝望，因为发现服务器上根本运行不了...为什么呢！？为什么！！！！

**坑**

我知道为什么了，首先，`Tomcat`运行的必须是`编译后`的项目，而在前期我是整个`源文件`直接上传至服务器的，而`Java`编译后，是会产生`.class`文件的。

在`WebContent`目录下，`\WEB-INF\classes`目录是存在诸如你建立的`类`或者`Servlet`文件，所以必须把你刚`源代码`中编译好的文件（源码根目录下`build`文件夹）copy到`\WEB-INF\classes`目录下。

现在可以了。

# 五、还有Nginx反向代理

这个我就晚点说了先去吃饭好不好~？

好了啦~