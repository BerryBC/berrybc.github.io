---
layout: post
title:  肆拾捌- 当我们在用 Tableau Dashboard Extension 时，我们到底看到什么？ 绝望之旅 (三)
date:   2020-07-26 14:40:24 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1. 我们必须设置 Debug

在调试 `Tableau 扩展`时，使用的是 `Chrome` 浏览器，详情可参考官网设置：

[Debug Extensions in Tableau Desktop](https://tableau.github.io/extensions-api/docs/trex_debugging.html)

其实就只需要一部，你需要在你的 `Tableau` 快捷方式里面增加一个配置：

![添加](/photo/InPost/48-1.png)

添加这段话后，每次打开 `Tableau` ，它就会创建一个 `web app`，然后你在浏览器里面输入：http://localhost:8696/ ，就能到达调试页面了。

但这里需要注意一个坑，`Tableau` 的 `Debuger` 呢，是不支持 `Chrome` 浏览器 80 及以后的，各位需要注意`浏览器`不要更新到最新。

如果你`浏览器`版本太新，建议先卸载，然后通过以下网址下载79版本的：

[chromedownloads](https://www.chromedownloads.net/chrome64win-stable/949.html)

# 2. tableau.extensions

好了！让我们来看看 `Tableau 扩展`能给我们带来什么东西，首先大家可以开着`官网 API 文档`：

[API Reference](https://tableau.github.io/extensions-api/docs/index.html)

-----

### 2.1 点选

在我们的 `Debuger` 里面的 `Console` 里面输入：

tableau.extensions

Wow!!

![Error](/photo/InPost/48-2.png)

出错了！为什么呢？

因为在 `Tableau Server `上面我们可以看到，其实 `Tableau` 是用一个 `iFrame` 来装我们的`扩展`的，所以我们需要在左上角` top` 那个位置点选一下：

![点选](/photo/InPost/48-3.png)

------------------

### 2.2 扩展内容

当我们打开时，我们依次展开，对我们最关心的 `Dashboard` 内容进行查看:

![内容](/photo/InPost/48-4.png)

我们能惊奇的发现，这里是对应我们的:

![我们](/photo/InPost/48-5.png)

我们的内容都在这里了！！

------------------

### 2.3 worksheet 数据

我们要读取 `worksheet` 里面的内容怎么办？

我们看回我们刚打开的内容:

![这里](/photo/InPost/48-6.png)


打开这里可以看出我们在仪表盘里面的两个`工作表`的`内容`，还有它的名字，我们想读取它的`数据`时，我们可以通过一个稍微复杂的语句：


```js
tableau.extensions.dashboardContent.dashboard.worksheets.find(function(w) {
		if (w.name === "Income") { return true; };
	})
.getSummaryDataAsync().then(function(sumdata) {
	const worksheetData = sumdata;
	console.log(worksheetData);
});
```

这段代码的意思是需要读取 工作表 的名字为 "Income" 的数据内容。

( 因为 `Tableau Server` 的用户可能会有 `IE` 用户，所以不要写如 let ()=> 等 `ES6` 的语法 )

出来是什么呢？

![内容](/photo/InPost/48-7.png)

对应 "Income" 那个工作表的数据为：

![信息源](/photo/InPost/48-8.png)

关注我们读取信息里面的 `Columns` ，我们会发现，他们的 `Columns` 就是里面的所有行列以及数据标识均堆叠成一起。再让我们看` Data` 会清晰很多：

![内容](/photo/InPost/48-9.png)

可以看到，`Tableau 工作表` 里面每一个`格子`的`数据`，在 `Tableau 扩展` 中都是根据行列来单独作为一个个体存在的。
好玩不！？


------------------

### 2.4 filter

同样在` Debuger` 的 `Console `里面运行:

```js
wsLoveBerry = tableau.extensions.dashboardContent.dashboard.worksheets.find(function(w) { if (w.name === "Income") { return true; }; });
wsLoveBerry .getFiltersAsync().then(function (filtersForWorksheet) {console.log(filtersForWorksheet);});
```

我们可以看到:

![filter](/photo/InPost/48-10.png)

我们看到 `Filter` 里面有两个对象，这个就是我们在工作表上定义的数据了！

是不是很神奇？

通过读取他们现在 `apply` 的数据，我们可以得知现在的 `过滤器` 选择。

当然，可以获取！肯定可以修改的！


原本我们的` Filter` 是:

![这个](/photo/InPost/48-11.png)

但是当我们运行:

```js
wsLoveBerry.applyFilterAsync("Staff",["爱美丽"],"replace", { isExcludeMode: false })
```

忽然变成：

![改了](/photo/InPost/48-12.png)

这不就改了嘛！




------------------

### 2.5 Parameter

这个是什么东西？

![Para](/photo/InPost/48-13.png)

只要我们运行参数

```js
        tableau.extensions.dashboardContent.dashboard.getParametersAsync().then(function(paraPnL) {
            if (paraPnL) {
                paraPnL.forEach(function(elePara) {
                    console.log(elePara);
                });
            };
        });
```

就行了。

![完了](/photo/InPost/48-14.png)

# 3. 纯粹介绍

本文其实纯粹介绍，我们并没有任何太详细的说明原因是，我们是需要多点看 文档。

之后会继续说个实例的了！