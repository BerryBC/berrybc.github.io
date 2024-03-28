---
layout: post
title:  伍拾贰- Tableau Extension “Load-Tableau-Summary-Data“ 使用
date:   2020-09-17 22:03:33 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# Load Tableau Summary Data
## 介绍
使用 Tableau Extension 时，如何把 Tableau Summary Data 转化为 二维数组需要复杂的转换。
本包能方便的把多个有相同维度的 Worksheet 合并为相应的二维数组。

## 使用
在 Extension 中：
HTML，先载入 Tabelau Extension 插件，再载入本插件 。
```html
...
	<!-- 先载入 Tabelau Extension 插件，再载入本插件 -->
    <script src="./thirdpt/tableau.extensions.1.4.0.min.js"></script>
	<script src="./secondpt/loadsummarydata.js?t=202009151521"></script>
...

```

----
根据 Tableau worksheet 的设置载入配置，如果 worksheet 配置如下:
![配置](/photo/InPost/52-1.png)
JS 代码如下：

```js
var objData = {};
objData.tLoadData = new tLoadSD();
var jsonFormated = [
    { wsName: "Cat_Sales", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["Sub-Category","Category"] },
    { wsName: "Sales_Gth", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["YEAR(Ship Date)"] }
  ];

$(function() {
    tableau.extensions.initializeAsync().then(function() {
        objData.tLoadData.loadCfg([], jsonFormated);.
        objData.tLoadData.LoadCol(function() {
            objData.tLoadData.ConvToTable(function() {
                console.log(objData.tLoadData.source);
            });
        });
    };
};

```

![在这里插入图片描述](/photo/InPost/52-2.png)
## 使用方法介绍
#### loadCfg(jsonColTitle, arrWS)
- JSON `jsonColTitle` 列标题及对应列数
- Array < JSON > `arrWS` Worksheet 配置

进行配置才能进行后续操作，其中 `arrColTitle` 可先不配置，若不配置则可以后续通过函数自动获取各 worksheet 的表头
- 暂时先不配置列头信息
```js
var jsonFormated = [
    { wsName: "Cat_Sales", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["Sub-Category","Category"] },
    { wsName: "Sales_Gth", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["YEAR(Ship Date)"] }
  ];
objData.tLoadData.loadCfg([], jsonFormated);
```

- 配置列头信息
```js
var arrForCol={};
objForCol["TOTAL SALES"] = 1;
objForCol["VS. LY%  "] = 2;
objForCol["VS. BUD%"] = 3;
var jsonFormated = [
    { wsName: "Cat_Sales", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["Sub-Category","Category"] },
    { wsName: "Sales_Gth", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["YEAR(Ship Date)"] }
  ];
objData.tLoadData.loadCfg(arrForCol, jsonFormated);
```


#### LoadCol(funCB)
- Function `funCB` Callback 函数

获取列标题
```js
    objData.tLoadData.LoadCol(function() {
    	console.log(objData.tLoadData);
    });
```
![列表](/photo/InPost/52-3.png)


#### ConvToTable(funCB)
- Function `funCB` Callback 函数

获取内容值
```js
    objData.tLoadData.ConvToTable(function() {
    	console.log(objData.tLoadData);
    });
```
![Content](/photo/InPost/52-4.png)

#### sortData(intCol,funCB)
- number `intCol` 列头位置
- Function `funCB` 回调函数

根据特定列的数据进行全局排序。

## Github 地址

[https://github.com/BerryBC/Load-Tableau-Summary-Data](https://github.com/BerryBC/Load-Tableau-Summary-Data)