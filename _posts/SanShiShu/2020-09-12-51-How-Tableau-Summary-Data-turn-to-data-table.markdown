---
layout: post
title:  伍拾壹- Tableau Extension 获取的工作表数据如何转换为行列表的绝望之旅 (五)
date:   2020-09-12 15:09:38 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 0. 获取食材的方法



在前期的文章中有介绍如何在 `Tableau` 中`调试( debug )`，还有获取`数据的基础结构`介绍，本期的内容多数为在前期的基础上进行介绍：

[肆拾捌- 当我们在用 Tableau Dashboard Extension 时，我们到底看到什么？ 绝望之旅 (三)](https://blog.csdn.net/BerryBC/article/details/107590412)

# 1. 从食材到料理

首先我们从 `Tableau Extension` 获取`工作薄`的 `API` 中得到的是：

![食材](/photo/InPost/51-1.png)

而我们想在`内存`中以`数组`形式存放：

![我们想得到的数据形式](/photo/InPost/51-2.png)

我们该怎样做呢？

# 2.稍微描述

对 Tableau Extension 获取信息的 API 介绍可以参考：

[肆拾捌- 当我们在用 Tableau Dashboard Extension 时，我们到底看到什么？ 绝望之旅 (三)](https://blog.csdn.net/BerryBC/article/details/107590412)

然后我们会得到一个类似`键值表`（但它本质上其实又是一个数组），我们怎么去定义转化呢？

# 3.一个函数走天下


先放代码：

```js
// 定义一个全局变量装当前设置
var objData = {};
// 所有数据的存放位置
objData.source = [];
// 定义我们需要读取 工作薄 的名字
objData.wsName = "Paradise";
// 定义在 Tableau 上行标题所属维度的变量名
objData.dimension = "Parlor";
// 尽在 Tableau 上读取数据时有用
objData.dictForRow = {};
// 定义在 Tableau 上列标题所属维度的变量名
objData.colName = "Content";
// 预先输入列标题内容
objData.arrCol = ["店铺","欧式","泰式","中式-1小时","中式-2小时","中式-过夜"];
// 预设列标题个数
objData.colCount = objData.arrCol.length;
// 定义值所在的字段名
objData.valueName = "总和(Income)";

// 链接时，需先对 Extensions 进行初始化
tableau.extensions.initializeAsync().then(function() {

	// 读取 工作薄 的内容
	tableau.extensions.dashboardContent.dashboard.worksheets.find(function(w) { if (w.name === objData.wsName) { return true; }; }).getSummaryDataAsync().then(function(sumdata) {
		// sumdata 为从 API 得到的数据，习惯会重新改变量名，其实可以不用
        var worksheetData = sumdata;
        // 值所在的 Tableau 原始 Data 中的各维度标识位置
        var intDim = 0;
        var intCol = 0;
        var intVal = 0;
        // 循环读取每个列字段名
        for (var intI = 0; intI < worksheetData.columns.length; intI++) {
            // 因不同语言的 Tableau 字段会不同，所以需要使用函数把不同语言的预留字段统一，并进行小写处理
            var strNowColName = funFixReservedWords(worksheetData.columns[intI].fieldName.toLowerCase());
            var strDimName = funFixReservedWords(objData.dimension.toLowerCase());
            var strShowColName = funFixReservedWords(objData.colName.toLowerCase());
            var strValueName = funFixReservedWords(objData.valueName.toLowerCase());

            // 此处进行行标题位置查询
            if (strNowColName == strDimName) {
                intDim = intI;
            };

            // 此处进行列标题维度汇总
            if (strNowColName == strShowColName) {
                intCol = intI;
            };

            // 此处进行值的位置查询
            if (strNowColName == strValueName) {
                intVal = intI;
            };
        };

        // 对每个值进行查询处理
        for (var intI = 0; intI < worksheetData.data.length; intI++) {
            // 读取每一颗数据
            var eleRow = worksheetData.data[intI];
            // 读取行标题，行标题必须使用 FromattedValue ，不然用户自定义的 “别名” 无法生效
            var strStoreCode = eleRow[intDim].formattedValue;

            // 列标题位置查找
            var strCol = eleRow[intCol].formattedValue;

            // 读取当前数据行的数据
            var dblVal = eleRow[intVal].value;

            // 如果前期未存在该行标题
            if (typeof(objData.dictForRow[strStoreCode]) == "undefined") {
                var intNowStore = objData.source.length;
                // 创建空白行数据
                var arrNewStoreInfo = new Array(objData.colCount);
                // 行标题先存入
                arrNewStoreInfo[0] = strStoreCode;
                // 把行标题跟存放在数组中的数据存入字典
                objData.dictForRow[strStoreCode] = intNowStore;
                // 存入数据
                objData.source.push(arrNewStoreInfo);
            };
            // Tableau 中 空数据 为 null 字符，改为 undefined
            if (dblVal == "%null%") {
                dblVal = undefined;
            };
            // 存入数据值
            objData.source[objData.dictForRow[strStoreCode]][objData.arrCol.indexOf(strCol)] = dblVal;
        };
        console.log(objData.source);

	});
});

// 因为不同语言的 Tableau 会自动对 度量名称/度量值 重新命名，而客户不能更改
// 故在此对 Tableau 保留字段进行统一化
function funFixReservedWords(strTitle) {
    var strReturn = strTitle;
    var arrMN = ["measure names", "度量名称", "度量名稱"];
    var arrMV = ["measure values", "度量值", "度量值"];
    if (arrMN.indexOf(strTitle) >= 0) {
        strReturn = "measure names";
    };
    if (arrMV.indexOf(strTitle) >= 0) {
        strReturn = "measure values";
    };
    return strReturn;
};

```

# 4.运行结果

![结果](/photo/InPost/51-3.png)

# 5.关于 SummaryData

在 `Tableau` 中，能获取的`数据`分为两种：
 - ***Summary Data***
 - ***Full Data***


或者简单的来说，`Summary Data` 就是你能在 `Tableau` 上让你看到的数据，而 `Full Data` 是`构成`你在 `Tableau` 上能让你看到的数据，官方介绍如下：

[Get Data from the Dashboard](https://tableau.github.io/extensions-api/docs/trex_getdata.html)


那么，为什么我们要千辛万苦的来只获取 `Summary Data`，而不直接获取 `底层数据 (Underlying Data )` 来直接在 `Extensions` 上来计算呢？

原因在于：

[Accessing Underlying Data](https://tableau.github.io/extensions-api/docs/trex_data_access.html)


我们在获取任何 `底层数据 ( Underlying Data ) `的时候，都是需要`允许`扩展一个叫做 `" full-data "` 的`权限`的。而为了`安全性`而言，我们一般是`不允许`开启这个权限的，所以就造成为什么我们千辛万苦的来把 `Summary Data` 来改造成`表格`样式的原因。


# 6.我们未来会做什么呢？


首先我们会试着把上述那一个函数把 `Summary Data` 转化为 `表格` 形式的 JS 代码，写成一个`包`，让以后开发 `Extensions` 的时间缩短，然后。

就没有然后了。