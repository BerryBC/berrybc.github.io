---
layout: post
title:  伍拾- 用 Tableau 记录生活，Tableau Extension 开发中插入数据的绝望之旅 (四)
date:   2020-08-22 23:23:41 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。我们必须先放一下效果图：

![图片](/photo/InPost/50-1.gif)

以上是我们的效果图。

众所周知，`Tableau` 更多的是属于一个`数据展示`的强大软件，`Dashboard` 的交互也仅存在于点击`筛选器`、`选择参数`、`点击`等。

似乎对于`数据分析`来说已经完全足够。

但对于某些需要稍微复杂的`数据展示`来说，要不就`效率的猛降`，要不就是`不够灵活`。

# 2。数据源的问题

`Tableau` 的`数据源`我们一般会选择清洗后、根据某一级别汇总后的数据（`数据仓库`），不会选择用原始数据（`数据湖`）。

这给我们带来某些思考，`Tableau` 能否直接对`数据仓库`进行交互（`插入、删除`）？

答案当然是可以的，通过 `Tableau Extension` 就可以带来这种可能。

但....（此处内容请下拉到最后一点）

# 3。如何实现

以一开始的效果图为例：

### -- 3.1. 开局

首先，我们必须在 `Tableau Extension` 创建一个 `Dashboard` (这是废话) 。

### -- 3.2. 发育

然后连接到我们的`数据库` ( 这里我选择使用 `MySQL` ，免费、对 `Python` / `NodeJS` 等轻量语言以及有完善的轻量 `Web App` 框架十分友好 )。
> *注意 Tableau 链接 MySQL 需要另外下载驱动。*

### -- 3.3. 捉单

创建我们 `Extension` 的内容，其中

HTML:

```html

    <div class="container">
        <div class="table-responsive container" id="divTable">
            <select  class="custom-select d-block w-100"  id="selCity">
            </select>
            <select  class="custom-select d-block w-100"  id="selParlor">
            </select>
            <strong><label for="txtColTitle">今天消费金额:</label></strong>
            <input type="text" class="form-control "  id="txtColTitle"  />
            <button class=" form-control btn btn-primary" style="margin-bottom: 30px;" id="btnRef">记录</button>
        </div>
    </div>
```

JS:


```js

$(function() {
    tableau.extensions.initializeAsync({ 'configure': funConfigure }).then(function() {
        $("#selCity").change(function(e) {
        	// 这里用 Ajax 从服务器提供的 API 中捞出当前选择城市中的 Parlor
            $("#selParlor").empty();
            $("#selParlor").append("<option value='1'> xxxx </option>");
            ....
            ....
        });
        $("#btnRef").click(function(e) {
        	// 这里通过 Ajax 通过服务器的 API 上传到我们当前输入的数据
        	....

			// 下面的语句是本期精选，刷新我们引用的数据源
            tableau.extensions.dashboardContent.dashboard.worksheets.find(w => w.name === "LovelyGirl").getDataSourcesAsync().then(datasources => {
                dataSource = datasources.find(datasource => datasource.name === "BigRich");
                dataSource.refreshAsync();
            });
        });
    });
});
```


### -- 3.4. 团战

关于`后端`的设置我决定不写了。

# 4。事后

通过简单几句简单的代码，前端的内容就写好了，其实：
- 就是拉取`服务器`现在多级归属信息数据
- 根据多级信息`更新`当前可选项
- 对当前输入`数据合理性`检查
- `上传`数据至`服务器`


后端的就比较复杂：
 - 提供查询 `API`
 - 提供写入` API`
 - 对输入输出的 `API` 传输数据进行`合理性检查`


好了。

本期内容到此结束


# 5。总结

本期我们针对 `Extension` 的内容只有一样，`刷新数据源`！

![插入图片](/photo/InPost/50-2.png)

[Interface DataSource](https://tableau.github.io/extensions-api/docs/interfaces/datasource.html)

以前我们进行 `Tableau Dashboard` 开发时，我们均不会在意`数据源`的刷新，但如果我们主动更新了`数据源`后，其实及时的反馈在`前端`是很有意义的。

--------



而本章真正有意义的就是那一句

```js
			tableau.extensions.dashboardContent.dashboard.worksheets.find(w => w.name === "LovelyGirl").getDataSourcesAsync().then(datasources => {
                dataSource = datasources.find(datasource => datasource.name === "BigRich");
                dataSource.refreshAsync();
            });
```

要剖析这一句的精髓，其实我们必须要理解：

**Dashboard 里面一切处理均是处理 Worksheet**

搜先我们必须先定位到一个`工作薄`，继而才能定位这个`工作薄`引用的`数据源`，再对`数据源`进行处理。

完。


# 6。真正的总结



其实作为使用 Tableau 的人来说，完全不需要管后台数据库是如何处理的，我们专心需要面对的问题是，如何使数据得到合理的展示，让我们可以从视觉上发现数据中的故事。

而数据量如果太大我们如何去优化呢？

其实我建议，不需要从数据分析师这个角度去优化，而应该是从建立数据仓库的开发者去思考、优化。

------


之前看过一个新闻，不确定是否真切，说在国内一个可以用的前端开发，年薪是10w，而美国也是10w，不过美国的是美金，我们是人民币。

Tableau 的官网标语有一句我觉得很有意义：“使 Dashboard 开发时间从以前的两周缩短为一天。”

这个标语让我觉得在释放两个信息：

1、单个 Dashboard 的开发如果仍然超过两周，那不应该使用 Tableau ；

2、国外前端两周的工资可以顶我们这边请6、7个前端一起干两周，Tableau 一个 License 一年的工资够我请一个前端就每天完成我无理的需求，那我何苦还要 Tableau？