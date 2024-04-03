---
layout: post
title:  伍拾玖- Tableau 的可能替代产品/形式
date:   2021-11-26 00:58:59 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


## 一、背景


在 [Tableau 退出中国市场](https://www.shangyexinzhi.com/article/4381535.html) 的背景下，我们需要开启寻求`替代方案`的痛苦过程。

但在寻求的过程中，我们必须先理清楚，我们在寻求的是什么。
- Tableau 对外宣传的 **核心功能** 是：
  * 数据`可视化`
  * `大量`数据分析
  * 响应快速、操作`简单`、操作`便捷`
  * 简单的 `ETL 工具`使底层数据容易整合
- 而 **作用** 分为
  * 做为 `Dashboard` 给管理层、业务部门直观数据体现
  * 能通过拖拉拽快速进行数据`整合`，能以数据说出直观的故事

我们可以沿着这些需求来看看如果我们没有 `Tableau` 后的未来。

-----

## 二、选择

有两种选择：

#### 1) 展示跟分析分离

分析直接通过编写 SQL / MapReduce + Excel / Number 等进行处理并进行`见解输出`。

展示则直接通过 `API` 获取数据并进行`网页`进行`动态展示`。

**好处**：

灵活、定制化高、无论多复杂的分析都可以做并按需展示

且展示速率极快，基本可`毫秒级加载`，需求只要数据底层分析能做，就能展示

**坏处**：

对分析人员技能要求较高，对不熟悉 SQL 等的分析人员需要再培训


#### 2) 更换为其他现有 BI 产品

可绕过底层数据处理，直接使用 IT 处理好的数据源进行拖拉拽，可见即可得。

**好处**：

操作简单、无需另外`学习知识`，专注数据处理及界面设计

**坏处**：

无法组合数据、无法对数据进行复杂处理、底层数据开发进度无法预计（因需 `IT 部门`完成）

界面样式更改因产品而异，并非所有均能定制、修改


## 三、其他现有 BI 产品

[百度 Sugar](https://cloud.baidu.com/product/sugar.html?track=BerryLoveYou) - [展示示例](https://sugar.aipage.com/dashboard/5f81db321ff3e080e9f09168c923854f)

[FineBI](https://www.fanruan.com/sem/finebi1?utm_source=BerryLoveYou) - [展示示例](http://demo.finebi.com/)

[永洪](https://www.yonghongtech.com/) - [展示示例](http://public.yonghongtech.com/bi/?au_act=login&adminv=demo&passv=Yonghong123)

[BDP](https://me.bdp.cn/home.html) - [展示示例](https://me.bdp.cn/case-operate.html)

还有一些我就不列了，国内的基本差不多离不开这些类型。

[国外的 Power BI](https://powerbi.microsoft.com/zh-cn/)

## 四、小总结

看了很多国内的产品，然后对比 `Tableau`，明显发现有几个点：
1. 国内的 BI 产品很多会十分`依赖 IT 部门`  （ 数据源需要预先配置好 ）
2. 仅仅拖拉拽，对 `字段` 处理灵活度不够（如不能用 `Case when` / `开窗函数` 等，需要实现类似功能还需要 IT 帮忙在数据源底层处理 )
3. Dashboard 的自定义`样式`不如 Tableau 自有，例如基本没有 Tableau 中的 `“浮起”` 功能。

简单点说，其实国内现在大部分的 BI 软件就相类似 SSRS 。

-----

## 正无限、后话

为什么我们需要 BI 工具？最重要的目的是使数据分析更为便捷。

针对简单的数据展示 / 分析，例如只有 `进销存、人货场` 的分析拆解，市面上任何一个 BI 工具完全胜任。但如果遇到一个较为复杂的问题，如：

```
在 A 游戏曾经充值的人群里，
曾经在 1月份 到过北京的 男人 ，
在过去的 2年内 有没有上过 天猫购买护肤品，或者 上过 京东 购买书籍？
有的话占比在 3月份 北京所有人数 的占比。
```

要完成这个分析的过程，用任何一个 BI 软件都是一个噩梦，但对于 SQL 可能就需编写 10分钟就完成了。
