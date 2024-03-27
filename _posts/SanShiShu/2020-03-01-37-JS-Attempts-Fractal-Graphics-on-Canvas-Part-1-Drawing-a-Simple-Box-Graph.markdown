---
layout: post
title:  叁拾柒- JS 在 Canvas 上尝试分形图形 （一） 画了一个普通箱子图
date:   2020-03-01 17:26:12 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。看到惊为天人的图形

我表哥在给我`科普` `分形`的时候，发了一个视频给我看：

[【PBS纪录片】寻找隐藏的维度【中英双字】](https://www.bilibili.com/video/av13766486/?spm_id_from=333.788.videocard.4)

其中说到一个关于`分型图形`的：

![就这个图](/photo/InPost/37-1.gif)

我忽然惊为天人！！

我觉得那人其实就是用了 `LSD` 才想出这种东西。

我懒得学`数学`，于是就用直接看看怎么画的。


----

结果发现，没有现成的`教程`。于是自己上网看。

# 2。H5 的 Canvas

我看过，比较熟的语言中，比较方便的就是 `JS` 了，直接`画图`的话，那用 `Html` 是方便的呀，而且 `Html5` 有 `Canvas` ，就它了！

看了教程觉得很容易啊！

[W3school HTML 5 Canvas](https://www.w3school.com.cn/html5/html_5_canvas.asp)

### 2-1) 看文档的重要性

其实我到现在还是找不到 `H5` 的`文档`，如果各位知道哪里可以看到的话麻烦留意一下告诉我，反正我被那个 `fillRect `耍了一个小时。

我一直以为：

```js
cxt.fillRect(0,0,150,75);
其中 0,0 是左上角的坐标
其中 150,74 是右下角的坐标


但！原来 150,75 其实是 width 以及 height！！
```


# 3。递归

因为我不是很熟其实`分形`是什么，只是看到`科普文章`里面只是说
> "当你不断递归的时候，你就得到分型图形了！！"


于是我简单的写了个思路

```js
let 第一个箱子=画布中间位置;
function 画下一层箱子(现在箱子的参数){
	let 现在箱子上面加个箱子=计算位置;
	let 现在箱子左面加个箱子=计算位置;
	let 现在箱子右面加个箱子=计算位置;
	let 现在箱子下面加个箱子=计算位置;

	画现在箱子上面的箱子();
	画现在箱子左面的箱子();
	画现在箱子右面的箱子();
	画现在箱子下面的箱子();

	画下一层箱子(上面的箱子);
	画下一层箱子(左面的箱子);
	画下一层箱子(右面的箱子);
	画下一层箱子(下面的箱子);
};
画下一层箱子(第一个箱子);
```

因为纯粹想看看会发生什么事，所以完全没有写`重用`、`优化代码`，得到结果如下：

![就一个普通的图形](/photo/InPost/37-2.png)


# 4。完整代码

```html
<!--
 * @Descripttion: 尝试画分形图形
 * @Author: BerryBC
 * @Date: 2020-03-01 16:48:25
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-03-01 17:22:43
 -->
<html>

<head>
    <meta charset="utf-8">
        <title>测试分形</title>
        <style>
            body {
                text-align: center;
                background-color: black;
            }

            .canFractal {
                background-color: rgb(243, 236, 253);
            }
        </style>
</head>

<body>
    <canvas id="canFractal" width="1300" height="900">绝望</canvas>
    <script>
        // 第一版
        let dblMX = 650;
        let dblMY = 450;

        let canF = document.getElementById("canFractal");
        let ctxDraw = canF.getContext("2d");
        let intMaxL = 80;
        let intMaxN = 8;
        let objRect = {
            top: dblMY - intMaxL / 2,
            bottom: dblMY + intMaxL / 2,
            left: dblMX - intMaxL / 2,
            right: dblMX + intMaxL / 2
        }

        // 0 是 上
        // 1 是 左
        // 2 是 下
        // 3 是 右
        function funDraw(intN, intL, objUpRect) {

            function forTest() {

                let intR = 0;
                let intG = 0;
                let intB = 0;
                intR = 10 - parseInt((Math.random()) * 9.9);
                intG = 10 - parseInt((Math.random()) * 9.9);
                intB = 10 - parseInt((Math.random()) * 9.9);
                ctxDraw.fillStyle = "#" + ((intR * 10 + intR) * 10000 + (intG * 10 + intG) * 100 + (intB * 10 + intB)).toString();
                ctxDraw.fillRect(100, 100, 200, 300);
            };


            intL = intL * intN / intMaxN;

            let intR = 0;
            let intG = 0;
            let intB = 0;
            let dblXRand = (objUpRect.right - objUpRect.left - intL) * Math.random();
            let dblYRand = (objUpRect.bottom - objUpRect.top - intL) * Math.random();

            let objRect0 = {
                top: objUpRect.top - intL,
                bottom: objUpRect.top,
                left: objUpRect.left + dblXRand,
                right: objUpRect.left + dblXRand + intL
            };
            let objRect2 = {
                top: objUpRect.bottom,
                bottom: objUpRect.bottom + intL,
                left: objUpRect.left + dblXRand,
                right: objUpRect.left + dblXRand + intL
            };
            let objRect1 = {
                top: objUpRect.top + dblYRand,
                bottom: objUpRect.top + dblYRand + intL,
                left: objUpRect.left - intL,
                right: objUpRect.left
            };
            let objRect3 = {
                top: objUpRect.top + dblYRand,
                bottom: objUpRect.top + dblYRand + intL,
                left: objUpRect.right,
                right: objUpRect.right + intL
            };

            intR = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intG = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intB = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            ctxDraw.fillStyle = "#" + ((intR * 10 + intR) * 10000 + (intG * 10 + intG) * 100 + (intB * 10 + intB)).toString();
            ctxDraw.fillRect(objRect0.left, objRect0.top, objRect0.right - objRect0.left, objRect0.bottom - objRect0.top);
            intR = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intG = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intB = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            ctxDraw.fillStyle = "#" + ((intR * 10 + intR) * 10000 + (intG * 10 + intG) * 100 + (intB * 10 + intB)).toString();
            ctxDraw.fillRect(objRect1.left, objRect1.top, objRect1.right - objRect1.left, objRect1.bottom - objRect1.top);
            intR = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intG = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intB = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            ctxDraw.fillStyle = "#" + ((intR * 10 + intR) * 10000 + (intG * 10 + intG) * 100 + (intB * 10 + intB)).toString();
            ctxDraw.fillRect(objRect2.left, objRect2.top, objRect2.right - objRect2.left, objRect2.bottom - objRect2.top);
            intR = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intG = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            intB = 10 - parseInt((Math.random() * intL / intMaxL) * 9.9);
            ctxDraw.fillStyle = "#" + ((intR * 10 + intR) * 10000 + (intG * 10 + intG) * 100 + (intB * 10 + intB)).toString();
            ctxDraw.fillRect(objRect3.left, objRect3.top, objRect3.right - objRect3.left, objRect3.bottom - objRect3.top);

            intN--;
            if (intN > 0) {
                funDraw(intN, intL, objRect0);
                funDraw(intN, intL, objRect1);
                funDraw(intN, intL, objRect2);
                funDraw(intN, intL, objRect3);
            };
        };
        ctxDraw.fillRect(objRect.left, objRect.top, objRect.right - objRect.left, objRect.bottom - objRect.top);
        funDraw(intMaxN, intMaxL, objRect);
    </script>

</body>

</html>

```