---
layout: post
title:  肆拾- 通过 JS （而不是 Python） 告诉你为什么要派发消费券
date:   2020-05-13 00:15:36 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 0. 引用

本内容引用 `EChart` / `JQuery` 以及 `Bootstrap`。

# 1. 首先这个是一个简单的商场模拟器

我们只是简单的模拟最简单的`商场`运行，不考虑除以下之外的任何情况：

1) 店铺只交`固定租金`；
2) 店铺只要当天`销售额`低于`租金`，则马上`退租`；
3) 商场`无法`引入`新租户`；
4) `消费券`折购部分全部由`商场`承担。

# 2. 模拟行为

如以下三个按钮：

![只是一个行为](/photo/InPost/40-1.png)

- 其中第一个按钮为平日`正常运营`
- 中间按钮为 `Outbreak` 开始发生，客户消费`意欲`是平常的 60%
- 下面的按钮为商场开始派发`消费券`

# 3. 结论

![简单粗暴](/photo/InPost/40-2.png)


- 在第 1 天开始`正常运营`
- 在第 35 天开始发生 `outbreak`
- 在第 95 天开始使用 `coupon`

以上图可得出，只需要使用 `消费券`，即使商场`支出`增大，但其实只要保住`店铺`的存在，还是有`盈利`的，但如果任由发展，就会变成下图情况：

![爆炸了](/photo/InPost/40-3.png)

可以看出，不出半年`利润`已经小于0了，`亏损`了！

# 4. 代码

`JS:`

```js
/*
 * @Descripttion: 商场模拟器
 * @Author: BerryBC
 * @Date: 2020-05-12 21:44:16
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-05-12 23:57:34
 */
var chartMallCost = echarts.init(document.getElementById('divCanMallCost'));
var chartTotalStoreTurnover = echarts.init(document.getElementById('divCanTotalStoreTurnover'));
var chartStoreNum = echarts.init(document.getElementById('divCanStoreNum'));
var chartMallProfit = echarts.init(document.getElementById('divCanMallProfit'));

// 模拟运行天数
var intDay = 0;

// 店铺数，初始均为存活状态
var arrStore = [];
// 初始有50家店铺
var intMaxStoreNum = 50;
// 剩余生存店铺一开始等于初始值
var intAliveStoreNum = intMaxStoreNum;
// 所有店铺总收入
var dblStoreTotalTurnover = 0;
// 每日店铺租金
var dblStoreRental = 1800;

// 正常状态下每天客流量
var intClientNum = 1800;

// 正常情况下 客单价
var dblExpend = 130;
// 每客户客单价浮动金钱
var dblFluctuate = 80;
// Outbreak 影响下的消费欲望系数，初始为1
var dblOutbreakImpact = 1;
// 消费券折购力度，初始为1（不打折）
var dblCouponRate = 1;

// 正常情况下每天商场基础成本
var dblMallFixCost = 2600;
// 商场每天成本
var dblMallDailyCost = dblMallFixCost;
// 商场每天收入
var dblDailyMallIncome = 0;
// 商场每天利润，等于商场每天收入减去商场每天成本
var dblDailyMallProfit = 0;

// 画图准备
var arrStoreNum = [];
var arrTotalStoreTurnover = [];
var arrMallCost = [];
var arrMallProfit = [];

$(function() {
    // 开始模拟
    $('#btnGo')[0].onclick = function() {
        setInterval(RunMall, 300);
    };
    // Outbreak 开始
    $('#btnBegan')[0].onclick = function() { dblOutbreakImpact = 0.6; };
    // 消费券开始派发
    $('#btnCoupon')[0].onclick = function() { dblCouponRate = 0.68; };
});

// 开始模拟商场
function RunMall() {
    // 每天清空营业店铺昨天的营业额及商场的基础成本，新的一天从0开始
    arrStore = [];
    dblMallDailyCost = dblMallFixCost;
    dblDailyMallIncome = 0;
    dblStoreTotalTurnover = 0;

    // 初始每家店铺收入
    for (var intI = 0; intI < intAliveStoreNum; intI++) {
        arrStore.push(0);
    };

    // 如果商场店铺数量越少，人流按比例减少
    for (var intI = 0; intI < Math.floor(intClientNum * intAliveStoreNum / intMaxStoreNum); intI++) {
        // 随机选择一家店铺
        var intSelectAStore = Math.floor(Math.random() * intAliveStoreNum);
        // 客户每单消费金额
        var dblClientExpend = (dblExpend + (Math.random() - 0.5) * dblFluctuate) * dblOutbreakImpact / dblCouponRate;
        arrStore[intSelectAStore] += dblClientExpend;
        // 若派发消费券，消费券折购金额全部由商场承担
        dblMallDailyCost += dblClientExpend * (1 - dblCouponRate);
    };

    // 每日对每家店铺进行租金、收入以及商场开销、收入进行核算
    for (var intI = 0; intI < intAliveStoreNum; intI++) {
        // 店铺租金进商场收入
        dblDailyMallIncome += dblStoreRental;
        dblStoreTotalTurnover += arrStore[intI];
        // 如果店铺当天收入小于租金则马上关店
        if (arrStore[intI] < dblStoreRental) {
            intAliveStoreNum--;
        };
    };
    // 计算商场当天利润
    dblDailyMallProfit = dblDailyMallIncome - dblMallDailyCost;
    intDay++;
    // 绘制 Chart 图
    DrawChart();
};

function DrawChart() {
    arrStoreNum.push([intDay, intAliveStoreNum]);
    arrTotalStoreTurnover.push([intDay, Math.floor(dblStoreTotalTurnover / 1000)]);
    arrMallCost.push([intDay, Math.floor(dblMallDailyCost / 1000)]);
    arrMallProfit.push([intDay, Math.floor(dblDailyMallProfit / 1000)]);

    // 绘制 现存店铺数 的曲线
    optStoreNum = {
        title: {
            text: '现存店铺总数'
        },
        xAxis: {
            type: 'value'
        },
        yAxis: {
            type: 'value',
            max: 60
        },
        series: [{
            name: '店铺数',
            type: 'line',
            data: arrStoreNum,
            smooth: true,
            lineStyle: {
                color: "#000000"
            },
            itemStyle: {
                color: "#000000"
            }
        }]
    };
    chartStoreNum.setOption(optStoreNum);


    // 绘制 商场总利润 的曲线
    optMallProfit = {
        title: {
            text: '商场总利润'
        },
        xAxis: {
            type: 'value'
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '商场利润',
            type: 'line',
            data: arrMallProfit,
            smooth: true,
            lineStyle: {
                color: "#00FF00"
            },
            itemStyle: {
                color: "#00FF00"
            }
        }]
    };
    chartMallProfit.setOption(optMallProfit);



    // 绘制 商场总支出 的曲线
    optMallCost = {
        title: {
            text: '商场总支出'
        },
        xAxis: {
            type: 'value'
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '商场总支出',
            type: 'line',
            data: arrMallCost,
            smooth: true,
            lineStyle: {
                color: "#FF0000"
            },
            itemStyle: {
                color: "#FF0000"
            }
        }]
    };
    chartMallCost.setOption(optMallCost);


    // 绘制 商店总利润 的曲线
    optTotalStoreTurnover = {
        title: {
            text: '商店总利润'
        },
        xAxis: {
            type: 'value'
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '商店总利润',
            type: 'line',
            data: arrTotalStoreTurnover,
            smooth: true,
            lineStyle: {
                color: "#00FFFF"
            },
            itemStyle: {
                color: "#00FFFF"
            }
        }]
    };
    chartTotalStoreTurnover.setOption(optTotalStoreTurnover);
};
```



`HTML:`

```html
<!--
 * @Descripttion: 遗传算法展示页
 * @version: 0.1.0
 * @Author: BerryBC
 * @Date: 2020-05-09 10:09:50
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-05-12 22:54:41
 -->
<!DOCTYPE html>

<html lang="zh-yue-Hans">

<head>
    <!-- Bootstrap Core CSS -->

    <link href="./static/css/animate.css" rel="stylesheet" type="text/css" />
    <link href="./static/css/jquery-ui.min.css" rel="stylesheet" type="text/css" />
    <link href="./static/css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link href="./static/css/app-creative.min.css" rel="stylesheet" type="text/css" />
    <link href="./static/css/style.css" rel="stylesheet" type="text/css" />

    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="renderer" content="webkit" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>
        商场模拟器
    </title>

</head>

<body>
    <div class="container">

        <section>
            <h2>开始运行</h2>
            <div class="col-md-12 mb-3">
                <button class="btn btn-primary btn-lg btn-block" id='btnGo'>Normal day</button>
                <button class="btn btn-danger btn-lg btn-block" id='btnBegan'>The outbreak began</button>
                <button class="btn btn-success btn-lg btn-block" id='btnCoupon'>Use Coupon</button>
            </div>
        </section>
        <section>
            <h2>展示</h2>
            <div class="row">
                <div id="divCanMallProfit" style="width: 50%;height:300px;"></div>
                <div id="divCanMallCost" style="width: 50%;height:300px;"></div>
            </div>
            <div class="row">
                <div id="divCanTotalStoreTurnover" style="width: 50%;height:300px;"></div>
                <div id="divCanStoreNum" style="width: 50%;height:300px;"></div>
            </div>
        </section>
    </div>
</body>


<script src="./static/js/echarts.min.js"></script>
<script src="./static/js/jquery.min.js" type="text/javascript"></script>
<script src="./static/js/jquery-ui.min.js" type="text/javascript"></script>
<script src="./static/js/bootstrap.min.js" type="text/javascript"></script>

<script src="./static/js/coupon.js?v=20200512" type="text/javascript"></script>

</html>

```