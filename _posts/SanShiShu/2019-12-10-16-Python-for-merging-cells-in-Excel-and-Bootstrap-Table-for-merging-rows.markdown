---
layout: post
title:  拾陆- Python 对 Excel 合并单元格及 Bootstrap Tbale 跨行合并
date:   2019.12.10 22:30:53 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 什么都没有，不用怕

首先呢，我觉得没什么事，纯粹是记录一下而已。

### 1. Bootsrtap 的 Table 进行行间合并

在[Bootstrap中文网](https://www.bootcss.com/)找到了一个方法，但那个是跨列合并的，跨行合并其实可以用以下方法：

```html
<table id="tbUser" class="table table-bordered text-center table-striped dt-responsive nowrap">
.......
<tr>
  <td>2</td>
  <td rowspan="2" class="align-middle">Yao</td>
  <td>Normal User</td>
</tr>
<tr>
  <td>3</td>
  <td>Admin</td>
</tr>
```

注意在下面那行的同一列是应该跳开不放任何东西的，而且`Class`分别在 `table` 以及 `td` 内应该放 `text-center` 以及 `align-middle` 的。

### 2.Python 合并 Excel 单元格

参考[Python读写Excel表格，就是这么简单粗暴又好用](https://blog.csdn.net/csdnnews/article/details/80878945)文章，使用包 `xlwt`，完事。

```vb
sheet1.write_merge(4,5,3,3,'打篮球')
# 其中函数中第一第二为行数，第三第四列为列数。
```

### 3.Mysql配置内存

[Mysql5.5配置文件my.cnf配置(my-huge.cnf)详解(四)](http://www.05bk.com/285.html)
