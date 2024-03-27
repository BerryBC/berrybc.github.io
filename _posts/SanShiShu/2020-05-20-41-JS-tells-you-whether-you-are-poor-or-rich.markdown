---
layout: post
title:  肆拾贰- JS 告诉你，到底你是贫穷还是富贵
date:   2020-05-20 00:00:25 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 0. 引用

本内容继续引用 `EChart` / `JQuery` 以及 `Bootstrap`。

# 1. 测试你是贫穷还是富贵

请回答以下问题，请问你觉得最近的`物价`是`上升`了还是`下降`了呢~？

- [ ] 物价**上涨**了！
- [ ] 物价**下降**了！

-----

- 答案是`上涨`的

恭喜，你是属于`贫穷`阶层的

- 答案是`下降`的

`老板`，能留下你的联系方式么？

# 2. Tell me why！

首先涉及一个超级简单的`供给曲线`，如果懒得翻书的话可以直接看以下链接：

[https://wenku.baidu.com/view/bb23f2868762caaedd33d4a9.html](https://wenku.baidu.com/view/bb23f2868762caaedd33d4a9.html)

更加懒得打开链接的可以直接看一下图（图片来源于网络）：

![秘密](/photo/InPost/41-1.png)

不理解上图什么意思？没关系，其实跟下面说的`没有`直接`关系`，因为我们即将模拟的只是一个`简单`且`短期`的模型，将会与`供求平衡`关系不大。

# 3. 我们首先得创造一个城市，以便观察它

一个城市的核心是什么？核心是`人`！

- __人(市民)__

我们创造了一堆`人`，他们居住在一个`城市`，所以称他们为 **`市民`** ，而这些市民全部的作息、兴趣千遍一律，`不同点`只有他们的`工资`，还有他们的`存款`，他们的`行为模式`也是千篇一律的，主要有以下情况：

  * 市民们不会因为`短期`的`工资下降`而`降低`自己的`消费水平`；
  * 为了维持他们自己的`存款`在某一固定水平，他们会`主动下降` `消费水平`。

距个栗子：

  * 某市民 A 月薪 1万 ，存款 100万 ，每个月花光 1万 收入。
  * 可是某一天告诉他，工资这个月只拿到 5千，他还是照样花 1万 。
  * 但他某天发现他存款只剩下 50万 了，他开始慌了，只按 5千 一个月来花。

-----

然后既然有人`赚钱`，那还是得`花钱`啊！所以我们创造了 **`开销`**。

- __开销__

我们这个城市有且只有 `3种` 开销，分别是：
  - 高消费 ( 追求人生更高的层次 )
  - 中消费 ( 普通的消遣娱乐 )
  - 低消费 ( 基础生存吃喝 )

  对应人类需求的 吃喝 (低消费) 、玩 ( 中消费 ) 、乐 ( 高消费 )。

  至于是谁玩谁，那就不知道了。

  各种级别的`开销`，均会因应前来消费的`人数`而变动`价格`，`越多`的人来消费，该级别的`消费水平`将会`越高`，`越少`人来消费，就会`降价`。

  举个栗子：
    - 高消费商店的消费市民本来只有10个，结果现在忽然变成了20个，相应高消费商店的对应消费单价将会上升；
    - 原本前往中消费商店的市民只有200个，结果现在只剩下100个了，相应的人中消费商店就会降价以祈求更多的市民前往消费。

# 4. 这个城市怎么运行呢？

首先，我们给每个市民先模拟一个正常日子下的`存款`，依据`存款`设立一定百分比的`收入 `( 加上某些随机因素 )。

然后，就让他们自己在城市中随心生存呗~

# 5. 顺利的人生并没有趣

太顺利的人生就像一滩`死水`，于是我们决定来点刺激的。

我们设定两个按钮，在按`第一个按钮`的时候，城市里面每个人能拿到的`收入`只有平常的`60%`！

做人总得给与别人希望对吧，于是我们`第二个按钮`，就是`恢复`平日的`收入水平`。

![按钮](/photo/InPost/41-2.png)

# 6. 让城市运作起来吧！

- 正常的日子


  在正常的日子里面，可以看出，基本生活是`波澜不惊`的，各消费水平的物价均处以一种`非常稳定`的水平。

  但你有没有看到，每个图表的右方，均有一个 **出事期间** 的标签，预示着不好的未来即将到来。

![图片](/photo/InPost/41-3.png)


- 变天了


  **坏未来** 终于来到了！！

  在`坏未来`之下，我们明显看出：
  - 转变为`低消费`的市民`大幅上升`；
  - `低消费的物价`因为大量市民转为低消费而`小幅上涨` ( 因为原本低消费市民人数较多，所以升幅不大 ) ；
  - `高消费`的市民人数`大幅减少`；
  - `高消费`的物价`大幅下降`；
  - 整个城市`总消费金额`明显减少。


![坏未来](/photo/InPost/41-4.png)

- 雨过天晴

  慢慢`恢复`到未出事前的水平，生活重回过往！
  ![正常](/photo/InPost/41-5.png)

# 7. 后记

上述模拟其实是一种`异常乐观`的模拟，主要有以下原因：

- 市民们的`收入`实际上并不能在雨过天晴后真正`恢复`正常水平；
- 消费人数以及消费价格并`未实际服从供求关系变化`；
- 整个`城市总收入下降`并未反馈在`市民收入`上；
- 供给物并`无设置`别的`替代模式`；
- `市民`逃离`城市`；
- 等等等等


基于上述条件，如果我们全部都设到模型中去，我们将会得到一个在出事后不久就`支离破碎`的`城市`，人们甚至连`低消费`都`消费不起`，都跑去`借钱`了。


# 8. 后记之后

话说可能没人关注，如果一个`陨石`砸下来`会死` `一千人`，整个城市的人疏散导致`经济下滑`，几乎每个人都会选择疏散这个城市的人。

但我想说的是，每个人都很容易关注`直接砸死`的一千人，但因为经济下滑而因`破产`、靠`借贷生活`无法`还钱`、生产链断裂而`无法获取`生存物资的人，可能有`千千万万人`，这些人也很有可能会在往后的`数个月`甚至`数年内`死掉。

我们是否因为`拯救`直接死掉的`一千人`，而`杀掉`我们没有直接动手的`千千万万人`呢？

或许你们可以看看以下这条新闻：

[WFP新闻](https://insight.wfp.org/covid-19-will-almost-double-people-in-acute-hunger-by-end-of-2020-59df0c4a8072)
![](/photo/InPost/41-6.png)

# 9. 代码

`JS`

```js
/*
 * @Descripttion: 城市模拟器
 * @Author: BerryBC
 * @Date: 2020-05-12 21:44:16
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-05-19 00:13:56
 */
var chartTotalSpend = echarts.init(document.getElementById('divCanTotalSpend'));
var chartTypeOfConsumers = echarts.init(document.getElementById('divCanTypeOfConsumers'));
var chartNumberOfConsumers = echarts.init(document.getElementById('divCanNumberOfConsumers'));


// 模拟运行天数
var intDay = 0;

// 市民数量
var intNumOfCitizen = 5000;
// 市民工资与存款结构
var objCitizenElement = { dblOriginalDeposit: 100000, dblDeposit: 100000, dblIncome: 10000 };
// 市民数组
var arrCitizien = [];
// 有某种状况下的工资发放系数
var dblPaymentCoefficient = 1;

// 总消费规模(金钱)
var dblTotalSpend = 0;
// 消费模式种类数
var intNumberOfConsumptionCategories = 3;
// 消费门槛
var arrOriginalConsumptionThreshold = [1000, 2000, 4000];
var arrConsumptionThreshold = [1000, 2000, 4000];
// 消费人数
var arrOriginalNumberOfConsumers = [0, 0, 0];
var arrNumberOfConsumers = [0, 0, 0];

// 初始化市民消费情况
for (var intI = 0; intI < intNumOfCitizen; intI++) {
    // 随机化市民工资情况
    var dblTmpDeposit = Math.random() * 100000;
    var dblTmpIncome = dblTmpDeposit * 0.045 + Math.random() * (dblTmpDeposit * 0.05);
    arrCitizien.push({ dblOriginalDeposit: dblTmpDeposit, dblDeposit: dblTmpDeposit, dblIncome: dblTmpIncome });
    // 该市民属于哪种消费模式
    if (dblTmpIncome >= arrConsumptionThreshold[2]) {
        arrOriginalNumberOfConsumers[2]++;
        arrNumberOfConsumers[2]++;
    } else if (dblTmpIncome >= arrConsumptionThreshold[1]) {
        arrOriginalNumberOfConsumers[1]++;
        arrNumberOfConsumers[1]++;
    } else {
        arrOriginalNumberOfConsumers[0]++;
        arrNumberOfConsumers[0]++;
    };
    dblTotalSpend += dblTmpIncome;
};



// 画图准备
var intAreaBegin = 100000;
var intAreaEnd = 100001;
var arrDay = [];
var arrTotalSpend = [];
var arrTypeOfConsumers = [
    [],
    [],
    []
];
var arrChartNumberOfConsumers = [
    [],
    [],
    []
];

$(function() {
    // 开始模拟
    $('#btnGo')[0].onclick = function() {
        setInterval(RunBaby, 100);
    };
    // 开始出事了
    $('#btnBegan')[0].onclick = function() {
        dblPaymentCoefficient = 0.5;
        intAreaBegin = intDay;
    };
    // 日子回复正常了
    $('#btnBack')[0].onclick = function() {
        dblPaymentCoefficient = 1;
        intAreaEnd = intDay;
    };
});

// 人生无常的模拟一下
function RunBaby() {
    arrNumberOfConsumers = [0, 0, 0];
    dblTotalSpend = 0;
    // 遍历每个市民模拟状况
    for (var intI = 0; intI < intNumOfCitizen; intI++) {
        // 模拟市民花费金钱情况
        var dblSpend = (arrCitizien[intI].dblDeposit / arrCitizien[intI].dblOriginalDeposit * 0.5 + 0.5) * arrCitizien[intI].dblIncome + (Math.random() - 0.5) * arrCitizien[intI].dblIncome * 0.002;
        // 模拟市民存款情况
        arrCitizien[intI].dblDeposit = arrCitizien[intI].dblDeposit - dblSpend + arrCitizien[intI].dblIncome * dblPaymentCoefficient;
        if (dblSpend >= arrConsumptionThreshold[2]) {
            arrNumberOfConsumers[2]++;
        } else if (dblSpend >= arrConsumptionThreshold[1]) {
            arrNumberOfConsumers[1]++;
        } else {
            arrNumberOfConsumers[0]++;
        };
        // 社会消费总额
        dblTotalSpend += dblSpend;
    };
    for (var intI = 0; intI < intNumberOfConsumptionCategories; intI++) {
        // 各种消费情况
        arrConsumptionThreshold[intI] = arrOriginalConsumptionThreshold[intI] + arrOriginalConsumptionThreshold[intI] * 0.2 * (arrNumberOfConsumers[intI] / arrOriginalNumberOfConsumers[intI] - 1)
    };


    // Times fly~
    intDay++;
    // 绘制 Chart 图
    DrawChart();
};

function DrawChart() {
    // 画图需要
    arrDay.push(intDay);
    arrTotalSpend.push(dblTotalSpend);
    arrTypeOfConsumers[0].push(arrConsumptionThreshold[0]);
    arrTypeOfConsumers[1].push(arrConsumptionThreshold[1]);
    arrTypeOfConsumers[2].push(arrConsumptionThreshold[2]);
    arrChartNumberOfConsumers[0].push(arrNumberOfConsumers[0]);
    arrChartNumberOfConsumers[1].push(arrNumberOfConsumers[1]);
    arrChartNumberOfConsumers[2].push(arrNumberOfConsumers[2]);

    // 绘制 市民们总消费金额 的曲线
    optTotalSpend = {
        title: {
            text: '市民们总消费金额'
        },
        xAxis: {
            type: 'category',
            data: arrDay,
            boundaryGap: false,
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '消费总数',
            type: 'line',
            data: arrTotalSpend,
            smooth: true,
            // lineStyle: {
            //     color: "green"
            // },
            // itemStyle: {
            //     color: "green"
            // },
            markArea: {
                color: "red",
                data: [
                    [{
                        name: '出事期间',
                        xAxis: intAreaBegin
                    }, {
                        xAxis: intAreaEnd
                    }]
                ]
            }
        }]
    };
    chartTotalSpend.setOption(optTotalSpend);

    // 绘制 不同消费种类物价 的曲线
    optTypeOfConsumers = {
        title: {
            text: '不同消费种类物价'
        },
        xAxis: {
            type: 'category',
            data: arrDay
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '低消费物价',
            type: 'line',
            data: arrTypeOfConsumers[0],
            smooth: true,
            // areaStyle: {},
            // stack: '总量',
            markArea: {
                data: [
                    [{
                        name: '出事期间',
                        xAxis: intAreaBegin
                    }, {
                        xAxis: intAreaEnd
                    }]
                ]
            }
        }, {
            name: '中消费物价',
            type: 'line',
            data: arrTypeOfConsumers[1],
            smooth: true,
            // stack: '总量',
            // areaStyle: {}
        }, {
            name: '高消费物价',
            type: 'line',
            data: arrTypeOfConsumers[2],
            smooth: true,
            // areaStyle: {},
            // stack: '总量'
        }]
    };
    chartTypeOfConsumers.setOption(optTypeOfConsumers);


    // 绘制 不同消费种类物价 的曲线
    optNumberOfConsumers = {
        title: {
            text: '不同消费种类市民数'
        },
        xAxis: {
            type: 'category',
            data: arrDay
        },
        yAxis: {
            type: 'value'
        },
        series: [{
            name: '低消费人数',
            type: 'line',
            data: arrChartNumberOfConsumers[0],
            smooth: true,
            areaStyle: {},
            stack: '总量',
            markArea: {
                data: [
                    [{
                        name: '出事期间',
                        xAxis: intAreaBegin
                    }, {
                        xAxis: intAreaEnd
                    }]
                ]
            }
        }, {
            name: '中消费人数',
            type: 'line',
            data: arrChartNumberOfConsumers[1],
            smooth: true,
            stack: '总量',
            areaStyle: {}
        }, {
            name: '高消费人数',
            type: 'line',
            data: arrChartNumberOfConsumers[2],
            smooth: true,
            areaStyle: {},
            stack: '总量'
        }]
    };
    chartNumberOfConsumers.setOption(optNumberOfConsumers);
};
```

----

`HTML`
```html
<!--
 * @Descripttion: 遗传算法展示页
 * @version: 0.1.0
 * @Author: BerryBC
 * @Date: 2020-05-09 10:09:50
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-05-18 23:45:25
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
        消费水平变化
    </title>

</head>

<body>
    <div class="container">

        <section>
            <h2>世事无常</h2>
            <div class="col-md-12 mb-3">
                <button class="btn btn-primary btn-lg btn-block" id='btnGo'>正常的日子</button>
                <button class="btn btn-danger btn-lg btn-block" id='btnBegan'>发生了某些不好的事情！</button>
                <button class="btn btn-success btn-lg btn-block" id='btnBack'>日子又正常了</button>
            </div>
        </section>
        <section>
            <h2>图表</h2>
            <div class="row">
                <div id="divCanTotalSpend" style="width: 100%;height:300px;"></div>
            </div>

            <div class="row">
                <div id="divCanTypeOfConsumers" style="width: 50%;height:300px;"></div>
                <div id="divCanNumberOfConsumers" style="width: 50%;height:300px;"></div>
            </div>
        </section>
    </div>
</body>


<script src="./static/js/echarts.min.js"></script>
<script src="./static/js/jquery.min.js" type="text/javascript"></script>
<script src="./static/js/jquery-ui.min.js" type="text/javascript"></script>
<script src="./static/js/bootstrap.min.js" type="text/javascript"></script>

<script src="./static/js/Consumption.js?v=20200518" type="text/javascript"></script>

</html>
```