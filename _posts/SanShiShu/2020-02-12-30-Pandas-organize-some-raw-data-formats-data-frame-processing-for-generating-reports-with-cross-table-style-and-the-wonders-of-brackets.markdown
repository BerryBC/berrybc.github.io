---
layout: post
title:  叁拾- Pandas 整理某些原始数据格式 - 类交叉表式生成报表的数据帧处理 以及中括号的妙处
date:   2020-02-12 13:47:52 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1. 写数据处理类 Python 代码的工具

当然就是鼎鼎大名的[朱庇特](https://jupyter.org/)了！

可以先把`代码`在这里运行，直接查看结果，再把完成的代码 `Copy` 到工程上，就省去往来`调试`的麻烦了。

Windows 下直接`下载安装`就可以了。

# 2. Pandas 读取 Excel 是怎样的呢？

我们现在有两个 `Shit`：

![Sheet1](/photo/InPost/30-1.png)

![Sheet2](/photo/InPost/30-2.png)

那么从 `Pandas` 导入这两个 `Sheet` 会怎样的呢？

![明显从 Excel 表的左上侧读取](/photo/InPost/30-3.png)

在什么都`不设置`的情况下，`Pandas` 是默认从 `Excel` 表的最`左上侧`读取的，而不是读取第一个有数据的行和列。

# 3. 数据源

![数据源](/photo/InPost/30-4.png)

- 在 `E2 单元格`是当前数据的某个`时间标识`（当前数据源是`'周'`）；
- 可以看得出第4行跟第5行都是`标题数据`；

 其中第4行是`日期标识`，

 第5行是字段`内容标识`

 - 数据B列-E列为单都数据`归属标识`
 - 数据列F列-J列为对应F4单元格日期的`数据内容`

# 4. 转换目标格式

![目标转换格式](/photo/InPost/30-5.png)

# 5. 工作前需注意事项

程序语言与日常的数数不一样，是`从 0 开始`的，而不是`从 1 开始`的（但我其实 `VB` 某些`数据类型`返回是从 1 开始数的，我也不知道是什么心态了）。

所以注意，`Pandas` 中 `数据帧`的`行和列`的位置跟 `数据源` 的行号是不一样的！

# 6. 简单处理开始

因为本文只是简单处理`数据格式`调整，未涉及数据`内容处理`，相关后事请看后文。

### 6.1 准备

```python
# 引入包
import pandas as pd
import time
import numpy as np

# 此处引入数据
# 是的，我是 Windows 用户，怎么了！？不给么！？哼！
dfWeeklySales=pd.read_excel('C:\\Users\\BerryCui\\Desktop\\TempData\\20200110\\qq.xlsx',header = None)

# 此处初始化数据
arrSales=[5,10,15]
arrTXN=[6,11,16]
arrATV=[7,12,17]
arrTime=dfWeeklySales.iloc[3][[5,10,15]].tolist()
arrTime
intYear=2019
intDayNum=3

# 此处定义一个空白的数据帧存放数据
dfShow=pd.DataFrame(columns=['Channel','CN','Store','Market','Act Sales','Act TXN','Act ATV','Day','Year'])
```

上述代码描述了如何`初始化位置参数`，当然实际上面对的表格可能并不是`固定格式`（即相应数据内容并不固定行）的，但本文只针对固定格式进行说明。

（其实判断`对应内容位置`的代码在别的文章，别随便告诉人）

另外还存设定了一个`空白`的`数据帧`以存放整理后的数据。

### 6.2 转换

开始了哦，别眨眼了哦！

```python
for intI in range(intDayNum):
    dfTmp=pd.DataFrame(dfWeeklySales[~dfWeeklySales.iloc[:,[3]].isnull().values &
                                     (dfWeeklySales.iloc[:,[3]]!='Store').values].iloc[:,[1,2,3,4,arrSales[intI],arrTXN[intI],arrATV[intI]]])
    dfTmp.columns=['Channel','CN','Store','Market','Act Sales','Act TXN','Act ATV']
    dfTmp.insert(len(dfTmp.columns),'Day',arrTime[intI])
    dfTmp.insert(len(dfTmp.columns),'Year',intYear)
    dfShow=dfShow.append(dfTmp,ignore_index=True)
```

好了，完事了。

### 6.3 关键解释

##### 6.3.1 关于两个中括号(方括号)

其实如果有学过 `R语言` 的人，估计也会经历类似的伤心事。

上述代码中：

```python
dfWeeklySales.iloc[3][[5,10,15]]
```

为什么会有 一个`中括号`里再包含一个`中括号`呢？

原因参看文章 [Pandas中loc，iloc与直接切片的区别](https://www.cnblogs.com/daozhongshu/archive/2018/04/30/8973439.html) ：
>直接索引索引的是列，方口号里面的内容一般是列索引名。也可以接受一个列名组成的list来接受多个列名。

##### 6.3.2 获取数据帧中非空数据

对于 `Pandas` `空数据处理`、`数据过滤`、`查找空值`等方法在基础教程里面有很多，一下为查找第4列`非空`的数据：

```python
dfWeeklySales[~dfWeeklySales.iloc[:,[3]].isnull().values]
```

其中 `~dfWeeklySales.iloc[:,[3]].isnull().values` 为第4列非空的行，`~` 的意思是否。

那为什么要加 `.values` 呢？

`.values` 的意思是从 数据帧转换为：`numpy.ndarray` 类型。

如果你不是放 `numpy.ndarray` 到中括号中，而是放 `数据帧（pandas.core.frame.DataFrame）`，例如：

```python
dfTT=pd.DataFrame([[True,False,True,True,True,True],
                   [True,False,True,True,True,True],
                   [True,False,True,True,True,True],
                   [True,False,True,True,True,True]])
dfWeeklySales[dfTT]
```

你将会得到：

![前10行数据](/photo/InPost/30-6.png)

其实，就是在`原数据帧`（dfWeeklySales）中，对应`新数据帧`（dfTT）内容为 `True` 的数据。

是不是很简单呢~

##### 6.3.3 原理

for 循环一共`循环`了3次（定义`数据源`只有3天的数据，提取数据表头内容的方法将在后续文章撰写）。

然后分别把3天的 `'Act Sales'`,`'Act TXN'`,`'Act ATV'` 以及`数据归属`放到一个临时的`数据帧`。

再把每天的`数据帧`合并到一起就行了。


# 7. 关于后事

三世书之所以重要，即需了解任何人事物的`前世`、`今生`、`来世`。

事物的前世包括它的`形成、来源、意义`等；

事物的今生包括它现在的`形式、组成、链接外在的链接接口`等；

事物的来世包括它可能引起的`后果、即将前往何处、会与谁进行交互`等。

了解前世需要相关`经验`，了解今生需要`知识`，了解来世需要用`意识`，我们必须靠努力的不断学习，不断充实自己，才能得到我们想要的结果，不然我们只能不断在原地`迷惘`，进而`堕入`修罗之道。

之后就把前期做好的表头查找形式、以及部分技巧继续存续下来，以便能让他们更好的保存下来，对自己的未来以及未来的你能有更大的裨益。

别误会，我不是雷锋，我只是崔秉龙，最后这一段纯粹是凑字数。

刚好4000字了，我好累啊。