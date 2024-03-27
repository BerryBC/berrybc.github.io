---
layout: post
title:  肆拾肆- 微信小程序 canvas 解锁及弹簧物理效应动画
date:   2020-06-23 00:27:25 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 效果

![简单动画](/photo/InPost/44-1.gif)

在`微信小程序`内做基于`弹簧物理效应`的`动画`。

# 2. 关于响应事件设置

`微信`现在好像只提供 `H5` 的 `Canvas` 接口，然后关于`拖动`、`点击`的`事件`接口如下：

```js
Page({
  onLoad: function () {
	// ......
  },

  // 这里是拖动响应
  onTouchMove: function (e) {
    cleanLine(false);
    gbobjPoint.isTouch = true;
    let tmPoint = e.touches[0];
    if (tmPoint.x < 0) {
      gbobjPoint.touchX = 0;
    } else if (tmPoint.x > gbobjPoint.canW) {
      gbobjPoint.touchX = gbobjPoint.canW;
    } else {
      gbobjPoint.touchX = tmPoint.x;
    };
    if (tmPoint.y < 0) {
      gbobjPoint.touchY = 0;
    } else if (tmPoint.y > gbobjPoint.canH) {
      gbobjPoint.touchY = gbobjPoint.canH;
    } else {
      gbobjPoint.touchY = tmPoint.y;
    };
    for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {
      if (!gbobjPoint.arrPoint[intI].clicked) {
        let dblFarFTouchX = tmPoint.x - gbobjPoint.arrPoint[intI].nowx;
        let dblFarFTouchY = tmPoint.y - gbobjPoint.arrPoint[intI].nowy;
        if ((dblFarFTouchX * dblFarFTouchX + dblFarFTouchY * dblFarFTouchY) < (gbobjPoint.R * gbobjPoint.R)) {
          gbobjPoint.arrPoint[intI].clicked = true;
          gbobjPoint.lineP[gbobjPoint.numOfP] = intI;
          gbobjPoint.numOfP++;
          if (gbobjPoint.numOfP == gbobjPoint.pwNum) {
            let bolRight = true;
            for (let intI = 0; intI < gbobjPoint.pwNum; intI++) {
              if (gbobjPoint.lineP[intI] != gbobjPoint.pw[intI]) {
                bolRight = false;
              };
            };
            if (bolRight) {
              gbobjPoint.fading = true;
            };
          };
        };
      };
    };
  },

  // 这里是手指离开的响应事件
  onTouchEnd: function (e) {
    gbobjPoint.isTouch = false;

    if (gbobjPoint.lineInv) {
      cleanLine(true);
    };
    gbobjPoint.lineInv = setInterval(invLine, 50);
  },
);
```

# 3. 关于力效应

1 )  `弹簧效应`主要公式为：

$$
F(x) = -mx
$$

所以其实纯粹是物体`越远`，受的力`越大`。

2 ) 当点击时，相当于也给一个力`作用`于`所有点`：

这里我给予两个`力`

-  当位于圆圈两倍半径内，给与`弹簧力`
-  当位于圆圈两倍半径外，给与小一点的`弹簧力`


```js
    if (gbobjPoint.isTouch) {
      if (dblFarT < (gbobjPoint.R * 2) * (gbobjPoint.R * 2)) {
        gbobjPoint.arrPoint[intI].vx += dblGapTX / 18;
        gbobjPoint.arrPoint[intI].vy += dblGapTY / 18;
      } else {
        if (dblGapTX > 0) {
          gbobjPoint.arrPoint[intI].vx += Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vx += -Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        };
        if (dblGapTY > 0) {
          gbobjPoint.arrPoint[intI].vy += Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vy += -Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        };
      };
    };
```

3 )  为了体现`阻尼`效果我还给了一个`阻尼力`


$$
F(v) = -mv
$$

# 4. 关于力的体现

纯粹是用一个`定时器`对所有点的状态以及位置计算出各点现在的`位置`以及力的`方向`，代码如下：

```js
function funRoundInv() {
  gbobjPoint.ctx.fillStyle = gbobjPoint.backColor + "1)";
  gbobjPoint.ctx.fillRect(0, 0, gbobjPoint.canW, gbobjPoint.canH);
  if (gbobjPoint.numOfP > 0) {
    gbobjPoint.ctx.lineWidth = gbobjPoint.R / 8;
    gbobjPoint.ctx.strokeStyle = gbobjPoint.lineColor + (gbobjPoint.lineTime / 10).toString() + ")";
    gbobjPoint.ctx.beginPath();
    gbobjPoint.ctx.moveTo(gbobjPoint.arrPoint[gbobjPoint.lineP[0]].nowx, gbobjPoint.arrPoint[gbobjPoint.lineP[0]].nowy);

    if (gbobjPoint.numOfP > 1) {
      for (let intI = 1; intI < gbobjPoint.numOfP; intI++) {
        gbobjPoint.ctx.lineTo(gbobjPoint.arrPoint[gbobjPoint.lineP[intI]].nowx, gbobjPoint.arrPoint[gbobjPoint.lineP[intI]].nowy);
      };
    };
    if (gbobjPoint.isTouch) {
      gbobjPoint.ctx.lineTo(gbobjPoint.touchX, gbobjPoint.touchY);
    };
    gbobjPoint.ctx.stroke();
  };
  gbobjPoint.ctx.lineWidth = 0;
  for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {
    gbobjPoint.ctx.fillStyle = gbobjPoint.arrPoint[intI].color;

    let dblGapX = (gbobjPoint.arrPoint[intI].nowx - gbobjPoint.arrPoint[intI].orgx);
    let dblGapY = (gbobjPoint.arrPoint[intI].nowy - gbobjPoint.arrPoint[intI].orgy);


    let dblGapTX = gbobjPoint.touchX - gbobjPoint.arrPoint[intI].nowx;
    let dblGapTY = gbobjPoint.touchY - gbobjPoint.arrPoint[intI].nowy;
    let dblFarT = (dblGapTX) * (dblGapTX) + (dblGapTY) * (dblGapTY);

    if (gbobjPoint.isTouch) {
      if (dblFarT < (gbobjPoint.R * 2) * (gbobjPoint.R * 2)) {
        gbobjPoint.arrPoint[intI].vx += dblGapTX / 18;
        gbobjPoint.arrPoint[intI].vy += dblGapTY / 18;
      } else {
        if (dblGapTX > 0) {
          gbobjPoint.arrPoint[intI].vx += Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vx += -Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        };
        if (dblGapTY > 0) {
          gbobjPoint.arrPoint[intI].vy += Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vy += -Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        };
      };
    };
    gbobjPoint.arrPoint[intI].vx += -dblGapX / 5;
    gbobjPoint.arrPoint[intI].vy += -dblGapY / 5;

    gbobjPoint.arrPoint[intI].vx += -gbobjPoint.arrPoint[intI].vx / 16;
    gbobjPoint.arrPoint[intI].vy += -gbobjPoint.arrPoint[intI].vy / 16;

    gbobjPoint.arrPoint[intI].nowx += gbobjPoint.arrPoint[intI].vx;
    gbobjPoint.arrPoint[intI].nowy += gbobjPoint.arrPoint[intI].vy;
    gbobjPoint.ctx.beginPath();
    gbobjPoint.ctx.arc(gbobjPoint.arrPoint[intI].nowx, gbobjPoint.arrPoint[intI].nowy, gbobjPoint.R, 0, 2 * Math.PI);
    gbobjPoint.ctx.closePath();
    gbobjPoint.ctx.fill();
  };
  if (gbobjPoint.fading) {
    gbobjPoint.ctx.fillStyle = gbobjPoint.backColor + (gbobjPoint.fadeTime / 10).toString() + ")";
    gbobjPoint.ctx.fillRect(0, 0, gbobjPoint.canW, gbobjPoint.canH);
    if (gbobjPoint.fadeIn) {
      gbobjPoint.fadeTime -= 0.3;
      if (gbobjPoint.fadeTime < 0) {
        gbobjPoint.fading = false;
        gbobjPoint.fadeIn = false;
      };
    } else {
      gbobjPoint.fadeTime += 0.3;
      if (gbobjPoint.fadeTime >= 10.5) {
        gbobjPoint.fading = false;
        clearInterval(gbobjPoint.roundInv);
        gbobjPoint.roundInv=null;
        gbobjPoint.wordInv=setInterval( funWordInv,30);
      };
    };
  };
};
```

# 5. 全代码
```js
const app = getApp();
let arrColor = ["#FF9D9D", "#E9FD78", "#6BFFAD", "#6BB0FF", "#E16BFF", "#FF6B7F", "#31ECDD", "#9BEC31", "#ECBA31"];
let gbobjPoint = {};
gbobjPoint.intPoint = 9;
gbobjPoint.arrPoint = [];
gbobjPoint.R = 9;
gbobjPoint.ctx = 0;
gbobjPoint.canW = 0;
gbobjPoint.canH = 0;
gbobjPoint.backColor = "rgba(255, 240, 255,";
gbobjPoint.roundInv = 0;
gbobjPoint.isTouch = false;
gbobjPoint.touchX = 0;
gbobjPoint.touchY = 0;
gbobjPoint.dpr = 0;
gbobjPoint.lineP = [];
gbobjPoint.numOfP = 0;
gbobjPoint.lineColor = "rgba(200,160,200,";
gbobjPoint.lineInv = null;
gbobjPoint.lineTime = 10;
gbobjPoint.fadeIn = true;
gbobjPoint.fadeTime = 10;
gbobjPoint.fading = true;
gbobjPoint.pw = [1, 3, 6, 4, 8, 5, 1, 1, 1];
gbobjPoint.pwNum = 6;
gbobjPoint.wordInv = null;
gbobjPoint.wordNum = 3;
gbobjPoint.wordContent = ["我","爱","丹尼尔"];
gbobjPoint.wordNow = 0;
gbobjPoint.wordMaxTime =10;
gbobjPoint.wordTime =0;
gbobjPoint.wordColor ="rgba(128, 0, 128,"

class claPoint {
  constructor() {
    this.color = "";
    this.orgx = 0;
    this.orgy = 0;
    this.nowx = 0;
    this.nowy = 0;
    this.vx = Math.random() * 5 - 2.5;
    this.vy = Math.random() * 5 - 2.5;
    this.clicked = false;
  };
};

function cleanLine(bolReal) {
  if (bolReal) {
    for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {
      gbobjPoint.arrPoint[intI].clicked = false;
      gbobjPoint.lineP[intI] = -1;
    };
    gbobjPoint.numOfP = 0;
  };
  gbobjPoint.lineTime = 10;
  clearInterval(gbobjPoint.lineInv);
  gbobjPoint.lineInv = null;
};

function invLine() {
  gbobjPoint.lineTime--;
  if (gbobjPoint.lineTime == 0) {
    cleanLine(true);
  };
};
function funWordInv(){

  gbobjPoint.ctx.fillStyle = gbobjPoint.backColor + "1)";
  gbobjPoint.ctx.fillRect(0, 0, gbobjPoint.canW, gbobjPoint.canH);
  gbobjPoint.ctx.fillStyle = gbobjPoint.wordColor+(1-gbobjPoint.wordTime/gbobjPoint.wordMaxTime).toString()+")";
  gbobjPoint.ctx.font = (gbobjPoint.wordTime/gbobjPoint.wordMaxTime*150).toString()+"px '微软雅黑'";
  gbobjPoint.ctx.textAlign = "center";
  gbobjPoint.ctx.textBaseline = "middle";

  gbobjPoint.ctx.fillText(gbobjPoint.wordContent[gbobjPoint.wordNow], gbobjPoint.canW/2, gbobjPoint.canH/2);
  gbobjPoint.wordTime+=0.1;
  if(gbobjPoint.wordTime>gbobjPoint.wordMaxTime){
    gbobjPoint.wordTime=0;
    gbobjPoint.wordNow++;
    if(gbobjPoint.wordNow>=gbobjPoint.wordNum){
      clearInterval(gbobjPoint.wordInv);
      gbobjPoint.wordInv=null;
    };
  };
};

function funRoundInv() {
  gbobjPoint.ctx.fillStyle = gbobjPoint.backColor + "1)";
  gbobjPoint.ctx.fillRect(0, 0, gbobjPoint.canW, gbobjPoint.canH);
  if (gbobjPoint.numOfP > 0) {
    gbobjPoint.ctx.lineWidth = gbobjPoint.R / 8;
    gbobjPoint.ctx.strokeStyle = gbobjPoint.lineColor + (gbobjPoint.lineTime / 10).toString() + ")";
    gbobjPoint.ctx.beginPath();
    gbobjPoint.ctx.moveTo(gbobjPoint.arrPoint[gbobjPoint.lineP[0]].nowx, gbobjPoint.arrPoint[gbobjPoint.lineP[0]].nowy);

    if (gbobjPoint.numOfP > 1) {
      for (let intI = 1; intI < gbobjPoint.numOfP; intI++) {
        gbobjPoint.ctx.lineTo(gbobjPoint.arrPoint[gbobjPoint.lineP[intI]].nowx, gbobjPoint.arrPoint[gbobjPoint.lineP[intI]].nowy);
      };
    };
    if (gbobjPoint.isTouch) {
      gbobjPoint.ctx.lineTo(gbobjPoint.touchX, gbobjPoint.touchY);
    };
    // gbobjPoint.ctx.closePath();
    // gbobjPoint.ctx.closePath();
    gbobjPoint.ctx.stroke();
  };
  gbobjPoint.ctx.lineWidth = 0;
  for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {
    gbobjPoint.ctx.fillStyle = gbobjPoint.arrPoint[intI].color;

    let dblGapX = (gbobjPoint.arrPoint[intI].nowx - gbobjPoint.arrPoint[intI].orgx);
    let dblGapY = (gbobjPoint.arrPoint[intI].nowy - gbobjPoint.arrPoint[intI].orgy);


    let dblGapTX = gbobjPoint.touchX - gbobjPoint.arrPoint[intI].nowx;
    let dblGapTY = gbobjPoint.touchY - gbobjPoint.arrPoint[intI].nowy;
    let dblFarT = (dblGapTX) * (dblGapTX) + (dblGapTY) * (dblGapTY);

    if (gbobjPoint.isTouch) {
      if (dblFarT < (gbobjPoint.R * 2) * (gbobjPoint.R * 2)) {
        gbobjPoint.arrPoint[intI].vx += dblGapTX / 18;
        gbobjPoint.arrPoint[intI].vy += dblGapTY / 18;
      } else {
        if (dblGapTX > 0) {
          // gbobjPoint.arrPoint[intI].vx += (gbobjPoint.R * 2) / 13 + (dblGapTX-gbobjPoint.R* 2) / 39;
          // gbobjPoint.arrPoint[intI].vx += Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 50;
          gbobjPoint.arrPoint[intI].vx += Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vx += -Math.sqrt(dblFarT - (dblGapTY) * (dblGapTY)) / 85;
        };
        if (dblGapTY > 0) {
          gbobjPoint.arrPoint[intI].vy += Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        } else {
          gbobjPoint.arrPoint[intI].vy += -Math.sqrt(dblFarT - (dblGapTX) * (dblGapTX)) / 85;
        };
      };
    };
    gbobjPoint.arrPoint[intI].vx += -dblGapX / 5;
    gbobjPoint.arrPoint[intI].vy += -dblGapY / 5;


    // gbobjPoint.arrPoint[intI].vx += Math.random() ;
    // gbobjPoint.arrPoint[intI].vy += Math.random();

    gbobjPoint.arrPoint[intI].vx += -gbobjPoint.arrPoint[intI].vx / 16;
    gbobjPoint.arrPoint[intI].vy += -gbobjPoint.arrPoint[intI].vy / 16;

    // if (dblGapX>0){
    //   gbobjPoint.arrPoint[intI].vx+=1/(Math.pow( dblGapX+6,2))/3;
    // }else{
    //   gbobjPoint.arrPoint[intI].vx+=-1/(Math.pow( -dblGapX-6,2))/3;
    // };
    // if (dblGapY>0){
    //   gbobjPoint.arrPoint[intI].vy+=1/(Math.pow( dblGapY+6,2))/3;
    // }else{
    //   gbobjPoint.arrPoint[intI].vy+=-1/(Math.pow( -dblGapY-6,2))/3;
    // };

    gbobjPoint.arrPoint[intI].nowx += gbobjPoint.arrPoint[intI].vx;
    gbobjPoint.arrPoint[intI].nowy += gbobjPoint.arrPoint[intI].vy;
    gbobjPoint.ctx.beginPath();
    gbobjPoint.ctx.arc(gbobjPoint.arrPoint[intI].nowx, gbobjPoint.arrPoint[intI].nowy, gbobjPoint.R, 0, 2 * Math.PI);
    gbobjPoint.ctx.closePath();
    gbobjPoint.ctx.fill();
  };
  if (gbobjPoint.fading) {
    gbobjPoint.ctx.fillStyle = gbobjPoint.backColor + (gbobjPoint.fadeTime / 10).toString() + ")";
    gbobjPoint.ctx.fillRect(0, 0, gbobjPoint.canW, gbobjPoint.canH);
    if (gbobjPoint.fadeIn) {
      gbobjPoint.fadeTime -= 0.3;
      if (gbobjPoint.fadeTime < 0) {
        gbobjPoint.fading = false;
        gbobjPoint.fadeIn = false;
      };
    } else {
      gbobjPoint.fadeTime += 0.3;
      if (gbobjPoint.fadeTime >= 10.5) {
        gbobjPoint.fading = false;
        clearInterval(gbobjPoint.roundInv);
        gbobjPoint.roundInv=null;
        gbobjPoint.wordInv=setInterval( funWordInv,30);
      };
    };
  };
};

Page({
  onPullDownRefresh() {
    wx.stopPullDownRefresh()
  },
  data: {
    motto: 'Hello World',
    userInfo: {},
    hasUserInfo: false,
    canIUse: wx.canIUse('button.open-type.getUserInfo')
  },

  onLoad: function () {

  },
  onTouchMove: function (e) {
    cleanLine(false);
    gbobjPoint.isTouch = true;
    let tmPoint = e.touches[0];
    if (tmPoint.x < 0) {
      gbobjPoint.touchX = 0;
    } else if (tmPoint.x > gbobjPoint.canW) {
      gbobjPoint.touchX = gbobjPoint.canW;
    } else {
      gbobjPoint.touchX = tmPoint.x;
    };
    if (tmPoint.y < 0) {
      gbobjPoint.touchY = 0;
    } else if (tmPoint.y > gbobjPoint.canH) {
      gbobjPoint.touchY = gbobjPoint.canH;
    } else {
      gbobjPoint.touchY = tmPoint.y;
    };
    for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {
      if (!gbobjPoint.arrPoint[intI].clicked) {
        let dblFarFTouchX = tmPoint.x - gbobjPoint.arrPoint[intI].nowx;
        let dblFarFTouchY = tmPoint.y - gbobjPoint.arrPoint[intI].nowy;
        if ((dblFarFTouchX * dblFarFTouchX + dblFarFTouchY * dblFarFTouchY) < (gbobjPoint.R * gbobjPoint.R)) {
          gbobjPoint.arrPoint[intI].clicked = true;
          gbobjPoint.lineP[gbobjPoint.numOfP] = intI;
          gbobjPoint.numOfP++;
          if (gbobjPoint.numOfP == gbobjPoint.pwNum) {
            let bolRight = true;
            for (let intI = 0; intI < gbobjPoint.pwNum; intI++) {
              if (gbobjPoint.lineP[intI] != gbobjPoint.pw[intI]) {
                bolRight = false;
              };
            };
            if (bolRight) {
              gbobjPoint.fading = true;
            };
          };
        };
      };
    };
  },
  onTouchEnd: function (e) {
    gbobjPoint.isTouch = false;

    if (gbobjPoint.lineInv) {
      cleanLine(true);
    };
    gbobjPoint.lineInv = setInterval(invLine, 50);
  },
  onReady: function () {
    const query = wx.createSelectorQuery()
    query.select('#canDrawPoint')
      .fields({
        node: true,
        size: true
      })
      .exec((res) => {
        const canvas = res[0].node;
        const ctx = canvas.getContext('2d');

        gbobjPoint.ctx = ctx;
        gbobjPoint.dpr = wx.getSystemInfoSync().pixelRatio;
        const dpr =gbobjPoint.dpr;
        canvas.width = res[0].width * gbobjPoint.dpr;
        canvas.height = res[0].height * gbobjPoint.dpr;
        ctx.scale(gbobjPoint.dpr , gbobjPoint.dpr );

        gbobjPoint.canW = canvas.width/gbobjPoint.dpr;
        gbobjPoint.canH = canvas.height/gbobjPoint.dpr;
        let dblR = Math.min(gbobjPoint.canW/ 13.2, gbobjPoint.canH / 16.2);
        gbobjPoint.R = dblR;
        console.log(gbobjPoint);
        // for (let intI = 0; intI < gbobjPoint.intPoint; intI++) {

        for (let intJ = 0; intJ < 3; intJ++) {
          for (let intK = 0; intK < 3; intK++) {
            let objTmpPoint = new claPoint();
            objTmpPoint.color = arrColor[(intK + intJ * 3) % 10];
            let bolOverlap = false;
            let intTmp = 0;
            // do {
            //   bolOverlap = false;
            //   objTmpPoint.orgx = (Math.random() * 6 + 1) * canvas.width / 8;
            //   objTmpPoint.orgy = (Math.random() * 8 + 1) * canvas.height / 10;
            //   for (let intJ = 0; intJ < intI; intJ++) {
            //     if (Math.sqrt(Math.pow(objTmpPoint.orgx - gbobjPoint.arrPoint[intJ].orgx, 2) + Math.pow(objTmpPoint.orgy - gbobjPoint.arrPoint[intJ].orgy, 2)) < (dblR * 2.5)) {
            //       bolOverlap = true;
            //       // console.log(Math.sqrt(Math.pow(objTmpPoint.x - gbobjPoint.arrPoint[intJ].x, 2) + Math.pow(objTmpPoint.y - gbobjPoint.arrPoint[intJ].y, 2)));
            //     };
            //   };
            //   intTmp++;

            // } while (bolOverlap && intTmp < 100);

            objTmpPoint.orgx = (intK + 1) * gbobjPoint.canW / 4;
            objTmpPoint.orgy = (intJ + 1) * gbobjPoint.canH / 4;
            objTmpPoint.nowx = objTmpPoint.orgx + dblR * (Math.random() * 2 - 1);
            objTmpPoint.nowy = objTmpPoint.orgy + dblR * (Math.random() * 2 - 1);

            // 速度的没有再根据画布大小随机
            gbobjPoint.arrPoint.push(objTmpPoint);
            gbobjPoint.lineP.push(-1);
          };
        };
        // };
        gbobjPoint.roundInv = setInterval(funRoundInv, 30);
      });
  }
})
```


# 6. 真正走心的内容

其实我想了好久都没想出到底怎么写这东西比较好，说是深的内容其实也不算，但我也怕以后我忘了代码。

还有因为我妈霸占了厕所我没办法睡觉所以写上来而已。

我一开始本来打算做复杂一点的动画，但后来想着想着还是算了，以后接着做吧，最近工作也很忙，公司压力也大，一直暗示着不乖点干活就要炒人，所以还是得用心工作的呀。