---
layout: post
title:  Fifty-Two- Tableau Extension “Load-Tableau-Summary-Data“
date:   2020-09-17 22:03:33 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# Load Tableau Summary Data

## Introduction

When using tableau extension, how to convert tableau summary data to 2D array requires complex conversion.

This package can easily merge multiple worksheets with the same dimension into corresponding two-dimensional arrays.



## Use

In the extension:

HTML, first load the tabelau extension plug-in, and then load this plug-in.

```html
...
	<!-- First load the tabelau extension plug-in, and then load this plug-in -->
    <script src="./thirdpt/tableau.extensions.1.4.0.min.js"></script>
	<script src="./secondpt/loadsummarydata.js?t=202009151521"></script>
...

```

----

Load the configuration according to the settings of tableau worksheet. If the configuration of the worksheet is as follows:

![配置](/photo/InPost/52-1.png)

JS code is as follows:

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

## How to use it

#### loadCfg(jsonColTitle, arrWS)

- JSON `jsonColTitle` column title and corresponding column number

- Array < JSON > ` arraws ` worksheet



Follow up operations can only be carried out after configuration. The 'arrcoltitle' can not be configured first. If not, the header of each worksheet can be obtained automatically through the function

- Do not configure column header information for the time being

```js
var jsonFormated = [
    { wsName: "Cat_Sales", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["Sub-Category","Category"] },
    { wsName: "Sales_Gth", valueName: "Measure Values", rowTitle: ["City","State/Province"] , colTitle: ["YEAR(Ship Date)"] }
  ];
objData.tLoadData.loadCfg([], jsonFormated);
```

- Configure column header information

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

- Function `funCB` Callback function

Get column header

```js
    objData.tLoadData.LoadCol(function() {
    	console.log(objData.tLoadData);
    });
```

![列表](/photo/InPost/52-3.png)


#### ConvToTable(funCB)

- Function `funCB` Callback function

Get content value

```js
    objData.tLoadData.ConvToTable(function() {
    	console.log(objData.tLoadData);
    });
```

![Content](/photo/InPost/52-4.png)

#### sortData(intCol,funCB)

- number `intCol` Column position
- Function `funCB` callback function

Global sort based on the data for a specific column.
