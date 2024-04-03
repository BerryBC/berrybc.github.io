---
layout: post
title:  伍拾捌- Tableau Extension 设置 参数 (六)
date:   2021-11-23 16:56:31 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

## 一、Tableau 都退出中国了，我在干嘛？

前阵子集团公司叫我们帮忙尝试一下 `Tableau Extension` 的某个想法是否能实现，然后就做了几天赶出了一个 Demo。

然后今天忽然就说 `Tableau` 退出中国了，那我们还干嘛？

## 二、关键原理

其实通过 `Extension` 来修改参数的原理基本就是
1. 获取 `参数` 对象
2. 修改 `参数` 对象的值


实现代码也十分简单

```js
// paraDemo 是獲取工作表的參數集合
tableau.extensions.dashboardContent.dashboard.getParametersAsync().then(function(paraDemo) {
    if (paraDemo) {
        // 遍歷所有 Tableau 的參數
        paraDemo.forEach(function(elePara) {
            // 如果 Tableau 參數等於現有情況，則存入
            if (elePara.name == 'Parameter Name') {
                elePara.changeValueAsync('Setting Value');
            }
        });
    };
});
```

因为在 `Tableau` 中，参数是`全局生效`的，也就是说只要改了`参数`，每一个`工作表`都会同时根据这个`参数`来更新。

具体参考可以看这里

[https://tableau.github.io/extensions-api/docs/interfaces/dashboard.html#getparametersasync](https://tableau.github.io/extensions-api/docs/interfaces/dashboard.html#getparametersasync)

## 三、生活不易

其实我真不知道中国人为什么用 Tableau，那么贵，一个 License 就够请一个弟弟做网页版的 Dashboard。

而且如果公司级别来用还要用 Server，还有很多个 View 的 License ，算了，继续努力工作。