---
layout: post
title:  Yaoyao 学习 Python 学习计划
date:   2019.12.15 22:58:00 +0800
categories: 更无聊的人生
author: 崔秉龙
location: Guangzhou, China
---


学习的顺序我觉得是至关重要的，但我也不会 `Python` 啊..怎么办？

于是我觉得是从需求出发会比较好，所以，以下，开始。

# 1. 包管理

`(半天学习时间)`

什么是包呢？任何`语言`都是需要一些`接口`功能包balabala去实现某些功能的（例如 `Windows` 常说的 `DLL`），就是你调用这些`包`，就直接能调用某些神秘的功能（例如删除文件、查看时间、balabala）。

而 `Python` 的包呢，这个比较复杂..还是算了。

反正如果你在例程中，看到：

```python
import Tkinter
top = Tkinter.Tk()
# 进入消息循环
top.mainloop()
```

里面的 `import` ，就是说要导入后面那个包了，例如上面的例子就是要导入 `Tkinter` 这个包。

而你想导入，但是如果这个`包`不存在在你电脑上怎么办呢？

你必须先安装，而安装呢？需要用 `pip` 命令：

```bash
pip install Tkinter
```

但是你在办公室，你是没办法直接用 `pip` 的，你需要先打开这个，才能使用刚刚那个命令：

![先打开这个再输入命令](/photo/InPost/14763760-51916f3c8777effb.png)


如果想认真研究什么是 `pip` ，可以看看这个网址：

[Pip安装与使用](https://www.runoob.com/w3cnote/python-pip-install-usage.html)


# 2. 然后呢？怎么写代码？

`(一天学习时间)`

其实最好是先配置好一个`编写IDE`([IDE-集成开发环境](https://baike.baidu.com/item/%E9%9B%86%E6%88%90%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/298524?fromtitle=IDE&fromid=8232086&fr=aladdin))，当然你使用上面那个截图里面的 Spyder 也可以的，但我比较喜欢 VSCode，这个看个人喜好。

# 3.喜欢用 Windows 的人都会喜欢界面！

`(两天学习时间)`

这个是毋庸置疑的！所以，我觉得首先需要学习的是这个：

[Python的GUI编程（TK）](https://www.cnblogs.com/dongxiaodong/p/9971974.html)

[Python GUI编程(Tkinter)](https://www.cnblogs.com/dongxiaodong/p/9971974.html)

[Python GUI编程 打开文件](http://blog.sina.com.cn/s/blog_6d21d3900102xfaq.html)

进阶文档：

[Introduction to GUI programming with](https://python-textbok.readthedocs.io/en/1.0/Introduction_to_GUI_Programming.html)

最简单想要做的一个`界面`就是，先有两个 `按钮` ，有一个 `Label` 。

一个按钮的功能是选择文件，然后那个 Label 就显示选择文件的路径，然后最后那个按钮是完成对选择文件的操作。

# 4.那最简单要做什么功能呢？

`(一周学习时间)`

我想要把一份所有店铺的月度 `SOP` 报表进行`成本中心(Cost Center)`与`店铺号`相匹配。

首先先对 `SOP` 报表进行格式整理，先用 `Pandas` 进行读取，然后整理为一个比较完整的 `数据帧` 格式（表头为各 P&L item），然后对汇总行进行删除再输出到与 `SOP` 报表相同文件夹中。

Pandas 的使用方法可以参考：[Pandas 中文文档](https://www.pypandas.cn/docs/)。

进阶的就是，首先要有一份`店铺号`与`成本中心`的匹配表，然后再对匹配到的行进行汇总（因为有些店铺，特别是某些虚拟店铺是有很多成本中心的）。

这个可以使用`Merge`：[pandas.merge用法详解](https://blog.csdn.net/Asher117/article/details/84725199)。

然后求和可以参考：[Pandas数据去重和对重复数据分类、求和，得到未重复和重复（求和后）的数据...](https://blog.csdn.net/weixin_30326745/article/details/96127312)。