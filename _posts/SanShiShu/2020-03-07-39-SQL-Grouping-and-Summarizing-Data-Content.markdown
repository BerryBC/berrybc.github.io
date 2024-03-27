---
layout: post
title:  叁拾玖- SQL对数据内容 分段/分组 汇总
date:   2020-03-07 00:05:01 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1。这一霎

内心并非无所平静的跳动，不在乎前与后的存在与否，是一致，或不美丽。

布朗运动是一种可爱的`灵魂`释放，就像天边五个月亮连成一串，那亦不无可爱。

# 2。何苦以言之内

`言之内`与`言之外`的一切，如缘起与`物间`的关系，若我们非以`目前之数`度`以变之数`，何其被动。

固执`法以言外`。

言之内则例如：[SQL语句查询年龄分段分组查询](https://blog.csdn.net/zfb52572/article/details/79049520)

若查`众多之数`或以`众法变之数`何以`不变言`？

# 3。言之外

### 3-1）均以段分

```sql
SELECT Round([Sales]/100,0)*100 AS SalesBin, Count(Data.Sales) AS CountOfSales
FROM Data
GROUP BY Round([Sales]/100,0)*100;
```

均以段，是之以`佰分`，诉之一不同之`言`，得之以不同之数，幸之以来。

### 3-1）自之有数段分

吾之有众数如：

```bash
creat table Bin
Level	MinValue
1	40
2	36
3	32
4	27
5	22
6	17
7	13
8	9
9	6
10	4
11	2
12	1
```

则言之若：

```sql
SELECT Bin.Level, Bin.MinValue, Count(Data.[Sales]) AS [CountOfSales]
FROM Bin, Data
# 若无须，则勿添
WHERE (((Data.Sales)<[Bin].[MinValue] And (Data.Sales)>0))
# ------
GROUP BY Bin.Level, Bin.MinValue;

```

得一以 Level 标之`累加`之数。

# 4。庆之若有，则以幸仍思并想

千万行之亦数分钟内完，`恒河沙数`之事，可`分表分库`也。

准则之于天下，非以特殊之术也。