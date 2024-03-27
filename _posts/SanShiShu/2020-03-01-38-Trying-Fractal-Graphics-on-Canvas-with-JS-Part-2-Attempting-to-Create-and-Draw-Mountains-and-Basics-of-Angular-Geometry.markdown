---
layout: post
title:  叁拾捌- JS 在 Canvas 上尝试分形图形 （二） 尝试造山画山 以及 角度几何基础
date:   2020-03-01 18:44:38 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1。以前的失败可以令你继续失败

前期我做了一个很丑的`图形`，所以我今天决定还是按照那个`纪录片`的，试着做一个`画山`的！

前期链接：

[叁拾柒- JS 在 Canvas 上尝试分形图形 （一） 画了一个普通箱子图](https://blog.csdn.net/BerryBC/article/details/104595472)

# 2。如何画山

上网找到一个说明：

[分形应用：造山](https://www.cnblogs.com/fjsjxy1111/p/5933216.html)

但是没有详细的代码，我该如何做呢！？

于是我开始了尝试绝望之旅

-------------

按照科普视频的说法就是：
>递归分割、中点置换

好！在做这个之前我先要复习一下`几何角度`的基础。

# 3。JS 几何角度基础

说白了，其实我真有点忘了 `PI` 到底是几度了，只能先给自己记录一下：

```js
        let v1 = {
            x: 0,
            y: 0
        };
        // 在 v1 的基础上增加1个单位为 1 的夹角 60度 的点。
        // 其中 Math.PI 为 180 度。
        let v2 = {
            x: v1.x + Math.cos(Math.PI / 3),
            y: v1.y - Math.sin(Math.PI / 3)
        };
        // 输出 -60 度。
        console.log(Math.atan((v2.y - v1.y) / (v2.x - v1.x)) / Math.PI * 180);
```

# 4。开始造山

思路`伪代码`如下：

```js
let 起始三角形的三个点1;
let 起始三角形的三个点2;
let 起始三角形的三个点3;
function 递归下一层(三角形点1，三角形点2，三角形点3){
	if (如果是最后一轮递归){
		画出该三角形
	};
	let 中点1=上层点1、2的中点（并进行随机的角度偏移）;
	let 中点2=上层点2、3的中点（并进行随机的角度偏移）;
	let 中点3=上层点3、1的中点（并进行随机的角度偏移）;

	递归下一层(三角形点1，中点1，中点3);
	递归下一层(三角形点2，中点2，中点1);
	递归下一层(三角形点3，中点3，中点2);
	递归下一层(中点1，中点2，中点3);
};
递归下一层(起始三角形点1，起始三角形点2，起始三角形点3);
```

结果如下：

![刚开始](/photo/InPost/38-1.png)


为什么你`断裂`了！？为什么中间那么多黑色的！那些是什么鬼！？

# 5。山体断裂的修补

之前发那个文章有一句：
>注：各三角形的公共边的中点应保证只有一次纵移，避免形成的山体破碎。

然后我就懵了，啊？


太麻烦了吧，因为我不同的三角形是`分开递归` `分开计算`的，怎样选择同样的`中点`呢！？

唉，但还是得做的。

# 5。修复山体断裂后

我选择的是选择一个`全局数组`变量来记录不同点的位置，并对不同`点`进行`编码`，得出结果如下：

![山体都链接在一起了！](/photo/InPost/38-2.png)


最后我还加了一个`阴影效果`，如果偏移得越厉害的点画出来的`颜色越深`。

# 6。最后一版的代码

```html
<!--
 * @Descripttion: 尝试画山，修复山体破碎
 * @Author: BerryBC
 * @Date: 2020-03-01 17:32:33
 * @LastEditors: BerryBC
 * @LastEditTime: 2020-03-01 18:27:38
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
        let dblMX = 500;
        let dblMY = 400;
        let dblMLen = 600;
        let canF = document.getElementById("canFractal");
        let ctxDraw = canF.getContext("2d");
        // 层数
        let intMaxDeep = 8;
        // 鲜艳度 0 - 1 之间，越靠近1越鲜艳
        let intColorWgh = 0.1;
        let dblMaxOffsetAngle = Math.PI / 8;

        let intCountP = 2;
        let dictLSet = [];
        let arrPSet = [];

        function funGetColor(dblDeep) {
            let intR = Math.abs(255 - parseInt((Math.random() * (1 / intMaxDeep * (1 - intColorWgh) + intColorWgh)) * 255) - 100 * dblDeep);
            let intG = Math.abs(255 - parseInt((Math.random() * (1 / intMaxDeep * (1 - intColorWgh) + intColorWgh)) * 255) - 100 * dblDeep);
            let intB = Math.abs(255 - parseInt((Math.random() * (1 / intMaxDeep * (1 - intColorWgh) + intColorWgh)) * 255) - 100 * dblDeep);
            let strReturn = "rgb(" + intR.toString() + "," + intG.toString() + "," + intB.toString() + ")";
            return strReturn;
        };
        let pO0 = {
            x: dblMX - dblMLen / 2,
            y: dblMY + dblMLen / 2,
            d: 0
        };
        let dblOAng = Math.PI / 3;
        let pO1 = {
            x: pO0.x + dblMLen * Math.cos(dblOAng),
            y: pO0.y - dblMLen * Math.sin(dblOAng),
            d: 0
        };
        dblOAng = -Math.PI / 3;
        let pO2 = {
            x: pO1.x + dblMLen * Math.cos(dblOAng),
            y: pO1.y - dblMLen * Math.sin(dblOAng),
            d: 0
        };

        function funDrawTri(arrInP) {
            dblColorDeep = (arrInP[0].d + arrInP[1].d + arrInP[2].d) / 3;
            ctxDraw.fillStyle = funGetColor(dblColorDeep);
            ctxDraw.beginPath();
            ctxDraw.moveTo(arrInP[0].x, arrInP[0].y);
            ctxDraw.lineTo(arrInP[1].x, arrInP[1].y);
            ctxDraw.lineTo(arrInP[2].x, arrInP[2].y);
            ctxDraw.closePath();
            ctxDraw.fill();
        };

        function funIter(intIterN, arrIterInP, intIterLen, arrIterPNumSet) {
            let intIterNM = intIterN - 1;
            let intIterNLen = intIterLen / 2;
            if (intIterNM == 1) {
                funDrawTri(arrIterInP);
            } else {
                let arrMP = [];
                let intIterNPNumSet = [];
                for (let intI = 0; intI < 3; intI++) {

                    let intJ = (intI + 1) % 3;
                    let intForDS;
                    let intForDB;
                    if (arrIterPNumSet[intI] > arrIterPNumSet[intJ]) {
                        intForDS = arrIterPNumSet[intJ];
                        intForDB = arrIterPNumSet[intI];
                    } else {
                        intForDS = arrIterPNumSet[intI];
                        intForDB = arrIterPNumSet[intJ];
                    };
                    if (!dictLSet[intForDS + '-' + intForDB]) {

                        let dblAng = -Math.atan((arrIterInP[intJ].y - arrIterInP[intI].y) / (arrIterInP[intJ].x - arrIterInP[intI].x));

                        if (arrIterInP[intJ].x < arrIterInP[intI].x) {
                            dblAng += Math.PI;
                        };
                        dblAng += (Math.random() - 0.5) * dblMaxOffsetAngle;
                        intCountP++;
                        intIterNLen = intIterNLen * (0.8 + 0.4 * Math.random())
                        let objNowP = {
                            x: arrIterInP[intI].x + intIterNLen * Math.cos(dblAng),
                            y: arrIterInP[intI].y - intIterNLen * Math.sin(dblAng),
                            d: dblAng
                        };
                        arrMP.push(objNowP);
                        arrPSet.push(objNowP);
                        dictLSet[intForDS + '-' + intForDB] = intCountP;
                        intIterNPNumSet.push(intCountP);
                    } else {
                        arrMP.push(arrPSet[dictLSet[intForDS + '-' + intForDB]]);
                        intIterNPNumSet.push(dictLSet[intForDS + '-' + intForDB]);
                    };
                };
                for (let intI = 0; intI < 3; intI++) {
                    let intJ = (intI + 2) % 3;
                    let arrNewTri = [arrIterInP[intI], arrMP[intI], arrMP[intJ]];
                    let arrNewPNumSet = [arrIterPNumSet[intI], intIterNPNumSet[intI], intIterNPNumSet[intJ]]
                    funIter(intIterNM, arrNewTri, intIterNLen, arrNewPNumSet);
                };
                funIter(intIterNM, arrMP, intIterNLen, intIterNPNumSet);
            };
        };
        arrPSet = [pO0, pO1, pO2];
        let arrPNumSet = [0, 1, 2];
        let arrOP = [pO0, pO1, pO2];
        funIter(intMaxDeep, arrOP, dblMLen, arrPNumSet);
    </script>


</body>

</html>
```


# 7。尝试画动画

因为纪录片上面还有一个是说`星球大战`的`熔岩喷射`效果也是`分形`来画的，我觉得我可以尝试一下，但好像失败了。

唉，大概效果如下：

![大概效果如下](/photo/InPost/38-3.gif)

之后再说呗~