---
layout: post
title:  陆拾壹- 数据分析师 / 智能商务 面试题目
date:   2022-04-24 17:37:57 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


下面的题目，如果自己不会的题目可以留空，尽量完成自己会做的题目。

附件资源包中包含题目所用到的数据，对应 Q-2-3 代表题目 2.3) 内的数据，如此类推。

其中资源包如下：

[文件下载](https://download.csdn.net/download/BerryBC/85214670)

## 1. Excel 能力

#### 1.1)	 使用 Excel 画出如下图，数据如附件包中所示。

（如有余力，可在下图之上增加提升可读性的更改）

![内容](/photo/InPost/61-1.png)

#### 1.2)	 Excel函数，仅用函数根据给出数据统计出以下信息，并保留公式。

（如有余力，可统计更多信息，例：高峰人数时段，去重人次等）

![我是可怜的秉秉](/photo/InPost/61-2.png)

####  1.3)	 Excel函数，计算今年对比去年的增长率，并保留公式。

![为什么我那么可怜](/photo/InPost/61-3.png)

####  1.4)	 把数据内容另存为 CSV，并不能有任何数据损失。

-- -------------


##  2.	数据敏感度

####  2.1)	 参考下图数据，整体每平售价应该达到多少才合理，并请给出分析思路。

![内容](/photo/InPost/61-4.png)

####  2.2)	 数据为记录丢失后，从店铺柜台重新同步的内容。查看数据并寻找数据有可能有异常的部分。


####  2.3)	 请以2013年实际店铺情况，结合2014年整体的目标，摊分成2014年每月、每店铺、每品类的销售、毛利、销量等指标，并留下摊分方法。
（需考虑数据合理性）


-- -------------

##  3.	SQL 能力

####  3.1)	 请调整下述 SQL 格式以达到便于他人阅读的程度：

```SQL
SELECT Project, SUM(CC_VAT) AS CC_VAT FROM ( SELECT tb_sales_mps.Store_Code, tb_sales_mps.Project, tb_sales_mps.FIN_Month,CASE WHEN B_S.Market IN ('Jiangx', 'Nanjing') THEN 'Dong' WHEN tb_sales_mps.Project = 'Milk Tea' THEN 'Drink' WHEN B_S.Market IN ('Mini App', 'H5') THEN 'Online' ELSE NULL END AS Platform_Type, CASE WHEN tb_sales_mps.Store_Code = 'Taobao' THEN tb_sales_mps.Cash_Coupon ELSE tb_sales_mps.Cash_Coupon * 1.17 END AS CC_VAT FROM tb_sales_mps LEFT JOIN db_storeinfo.tb_curstorelist B_S ON tb_sales_mps.Store_Code = B_S.Store_code WHERE tb_sales_mps.Fin_Month >= 202202 ) ForLove GROUP BY Store_Code, FIN_Month, Project, Platform_Type
```

####  3.2)	 通过 SQL 按30为间隔单位，统计出以下结果：

 ![简单](/photo/InPost/61-5.png)


####  3.3)	 根据数据统计出各家店的销售额，销量，订单量，并保留 SQL 代码。

####  3.4)	 统计出符合以下条件的销售额，销量，订单量，并保留 SQL 代码。
>在 易购 平台中，曾经购 “游戏点卡” 的人群里，
有在 1月份 到过北京的客户中，
有没有在 3月份 去过 昆明 ，并使用淘菜菜买过口罩？
有的话占比3月份 有在 昆明 中使用 淘菜菜 的总人数比例是多少？


####  3.5)	 现有表 ‘ tb_rmsreport159 ‘ （字段如下图所示），根据此表内容，创建新表 ‘ tb_rmsreport159_2 ‘ （字段如下图所示），并把原表数据插入到新表中，新增字段留空。

 ![内容](/photo/InPost/61-6.png)

tb_rmsreport159

 ![唉](/photo/InPost/61-7.png)

tb_rmsreport159_2



####  3.6)	 在 Hive 中，经营茶饮店的行业背景下，如果现在需每天输出每店铺的各 单品 的销售、相关指标情况，应该在主题表中以什么作为分区键。

-- -------------

##  4.	Python / R 能力

####  4.1)	 使用 R 读取、合并数据 Q-4-1-A 以及 Q-4-1-B ，并给计算出 ‘翻台率’ ，保留 R 代码。

####  4.2)	 使用 Python 读取数据并转化为下述格式输出成为 CSV，保留 Python 代码。

![内容](/photo/InPost/61-8.png)

####  4.3)	 使用 Python 使用任意机器学习包，使用任意两种方法对数据中的 Y 值进行回归，并保留代码。

####  4.4)	 在函数 fCal 中输入代码，使最终输出结果为：
   ![内容](/photo/InPost/61-9.png)

####  4.5)	 使用 Python 根据代码中 字典 变量 dictA 以及 dictB 生成如下图的 txt:

![唉，好累](/photo/InPost/61-10.png)
-- -------------

##  5.	公司现况见解能力

此为开放回答，可说出`分析方法`、尝试描述`可能影响`等。

如能用 `图表、表格、PPT` 形式辅助展示回答更佳。

这里涉及公司内容，需要根据公司内现有`业务`来添加题目。
