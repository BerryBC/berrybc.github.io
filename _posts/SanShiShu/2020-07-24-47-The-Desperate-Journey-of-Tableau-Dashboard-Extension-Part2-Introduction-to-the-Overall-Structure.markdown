---
layout: post
title:  肆拾柒- Tableau Dashboard Extension 的绝望之旅 (二) - 整体结构介绍
date:   2020-07-24 00:06:24 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 结构

其实 `Tableau 扩展`并没太复杂的结构 ( 或者说不能有太复杂的东西 )，就是一个 `主页面` ( 即你拉插件后插件位置展示的页面 ) 以及一个 `配置页`。

# 2. 主页面

当你运行 `Dashboard` 的时候，`插件`同时会打开插件所指向的`页面`，这个时候就跟直接在 `Dashboard` 上引用一个 `网页` 是一样的。

但`插件`与`网页`的不一样，就是在`主页面`中以下这个 JS 语句：

```js
$(document).ready(function() {
	// 这句话是针对 Tableau 进行初始化
    tableau.extensions.initializeAsync().then(function() {
    	var dashboard = tableau.extensions.dashboardContent.dashboard;
		...
		// 这里就可以对 Tableau 为所欲为了
    }).catch(function(err){
		// 可以判断一下当前是直接用浏览器，还是 插件 的内容出错。可以展示不同的内容引导用户进行操作。
	});
});
```

*附加介绍: 如果你运行一个网页，该网页有引用一个 `JS 文件`的话，这段 `JS 代码`会跟随网页一起运行。*

以上那段代码的意思就是，当你网页`初始化`完成之后！你就可以读取当前 `Dashboard` 上的`数据`了！

官方介绍可看：

[Create a 'Hello World" Dashboard Extension](https://tableau.github.io/extensions-api/docs/trex_create.html)

-----

你可以做的事情不止以下：

1、在插件上按一个按钮可以操控所有 `Worksheet` 的 多个`筛选器`；

2、把当前 `Worksheet` 上的数据拿出来用 `J`S 做别的`可视化`处理 ( 如使用 `eChart` 等 )；

3、根据当前`筛选器`修改`参数`，或根据`参数`修改各`筛选器`；

4、.......

*( 其实我真心觉得，在网页上做的那么简单的东西，在 `Tableau` 上为什么就那么复杂呢？然后还要卖那么贵 `License` )*


# 3. 配置页

当你做好`主页面`的`代码`之后，你会寻思着，有某些设置是 `Dashboard` 作者想针对这个 `扩展` 所特定的 ( 但又不需要修改`代码层面` ) 。

这个时候你就需要一个 `配置页面` ，而 `Tableau 扩展`针对这个情况，可以允许你在 `主页面` 上声明一个`配置页面`，官方说明如下：

[Add a Configuration Popup Dialog](https://tableau.github.io/extensions-api/docs/trex_configure.html)


在`主页面`的具体 `JS 代码`就是：

```js
$(document).ready(function () {
    tableau.extensions.initializeAsync({'configure': configure}).then(function() {
    	...
    });
});

function configure() {
	var popupUrl="./popup.html";
	var para={content:"想要传达到 配置页面 里的内容"};
	// 这里注册 配置页 的url
    tableau.extensions.ui.displayDialogAsync(popupUrl, para, { height: 500, width: 500 }).then((closePayload) => {
		...
		// closePayload 是当配置页面关闭后返回的信息
	}).catch((error) => {
		// 配置页出错的信息
	});
};

```

而`配置页`的 `JS 代码`可以如下：

```js
$(document).ready(function () {
	// 配置页 的 JS 代码也是需要初始化的哦~
	tableau.extensions.initializeDialogAsync().then(function (openPayload) {
		// openPayload 的信息就是上述传入的信息，即上述代码中的 para 变量
		...
	});
});

function closeDialog() {
	// 关闭当前配置页时需调用下面这个函数，让 主页面 知道是用户正式关闭了配置页。
	tableau.extensions.ui.closeDialog('NewInterval');
		// 'NewInterval' 为传递给主函数的变量，即上述代码中的 closePayload 变量
    });
};

```


# 4. 还有其他吗？

没有了， `Tableau 扩展`其实内容真的太有限。

以上的内容均为对 `Tableau 扩展`的介绍文档的大体翻译，小伙伴们可以先看看 `Tableau 扩展` 的 `API 文档`，我们之后会介绍一个我们做的把 `Tableau Worksheet` 内容表格化 ( 当然还可以简单计算，如计算`增长率、达成率、差值等` ) 的扩展。

[Tableau Dashboard Extensions API](https://tableau.github.io/extensions-api/docs/index.html#tableau-dashboard-extensions-api)