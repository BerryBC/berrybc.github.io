---
layout: post
title:  拾壹-Linux下Rserve开机自启动
date:   2019.01.01 00:09:03 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 一、奇怪的开端

为什么我要这样做？

这是一个谜团，可能因为我无聊，也可能因为我真的实在太无聊，可是就这样了。

我实在喜欢上`R语言`了，对比`Python`来说，这个挑战难度太大了！！

# 二、Linux开机自启动

其实一般来说，`Linux`开机自启动有几种方式，相信我，我都试过了，例如[Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)、[centos7 tomcat service 自启动](https://blog.csdn.net/yuzfengxu/article/details/79271174)、[Linux中如何自动启动服务](https://www.xuebuyuan.com/3238900.html)、[在Linux中利用Service命令添加系统服务及开机自启动](https://blog.csdn.net/u013554213/article/details/78792686)，甚至`Supervisor`（我知道是不适合这种没有托管的后台程序）等我都试过了，我是绝了个望。


# 三、寻找错误

在找寻中间，我发现在`/etc/init.d`下生成一个服务的方法还是可行的哦（因为之前`nginx`就是这样自启动的），甚至如果不是开机启动而是手动`service rserve start`是可以启动的！但为什么就是没办法开机自启动呢？

试了好久之后，发现估计是脚本写错了，我尝试用其他的服务脚本去改，诶！居然有点起色----->出现了新的错误！


# 四、新的错误其实也是好的嘛

知道有新的错误就可以解决新的错误嘛，新的错误就是：`Fatal error: you must specify '--save', '--no-save' or '--vanilla'`。

上网已搜，这错误是属于`R`报出来的，就是说我的脚本是可以运行的而只是`R`那里出错了，结果出来了，只要在我的脚本那里加上一个选项参数`--no-save`就行了，结果，可以了。


# 五、自启动脚本

在`/etc/init.d`目录下，生成一个`rserve脚本`，脚本内容如下：

```bash
#!/bin/bash
# chkconfig: 2345 05 98
# description: Start Rserve
### BEGIN INIT INFO
# Provides:          rserve
# Required-Start:    $network
# Required-Stop:     $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
### END INIT INFO

start() {
        /usr/local/lib64/R/bin/R CMD /home/Berry/R/x86_64-pc-linux-gnu-library/3.2/Rserve/libs//Rserve  --no-save
}

stop() {
    true
}

case "$1" in
    start)
       start
       ;;
    stop)
       stop
       ;;
    restart)
       stop
       start
       ;;
    status)
       ;;
    *)
       echo "Usage: $0 {start|stop|status|restart}"
esac
exit 0

```


# 六、我实在是太无聊了

我觉得我很无聊啊，怎么办？

有没有工作邀请我去做？

我在找工作啊！！

# 七、Rserve

其实对于`Rserve`来说，自启动是不是必须呢？因为毕竟这也不是一个稳定的平台（有时候需要重启），但既然只是一个想用来找工作的平台我觉得也没什么所谓了，对吧？