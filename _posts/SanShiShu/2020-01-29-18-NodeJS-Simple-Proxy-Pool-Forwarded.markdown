---
layout: post
title:  拾捌-NodeJS简单代理池（转）
date:   2020.01.29 20:54:55 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



# 1.缘起

原本的`架构`介绍如下：

[拾肆-NodeJS简单代理池（起）](https://blog.csdn.net/BerryBC/article/details/104110171)

然后今天为了做其他需要把这个完善一下。

# 2.配置文件

原本就没用 `ini` 的`配置文件`存储方式，所以就用一个 `JSON` 文件存储配置，其中代码如下：

```js
/**
 *读取配置文件并初始化。
 */
function funInit() {
    //读取配置文件
    let strConfig = fs.readFileSync(path.join(__dirname, '/cfg.json'), { encoding: "utf-8" });
    //文本转为对象
    let objConfig = JSON.parse(strConfig);
    //应用各种配置
    let arrSaveSet = objConfig.saveSet;
    let objWebCfg = objConfig.webCfg;
    objCTLIO = new cControllerIO(arrSaveSet);
    objCTSpy = new cControllerRequest(objWebCfg);
    objTimeConfig = objConfig.timeConfig;
    console.log(' 完成初始化 ');
    funGoPro();
};
```

但 `JSON` 有一个坏处，就是无法`注释`，只能增加多一个`字段`作为注释。

```js
//原本为：
"saveSet": [{
  "libPath": "/IOEng/cIOMongodb.js",
  "dbName": "dbProxy",
  "host": "27017",
  "user": "Berry",
  "pw": "Berry",
  "col": "tbProxy"
}, {
  "libPath": "/IOEng //cIOJSON.js",
  "file": "../../proxy.json"
}]

//新增一字段，把内容写到该内容里面并作为字符改为：
"saveSet": [{
  "libPath": "/IOEng/cIOMongodb.js",
  "dbName": "dbProxy",
  "host": "27017",
  "user": "Berry",
  "pw": "Berry",
  "col": "tbProxy"
}],
"Comment": {
  "ForsaveSet": ", {'libPath': '/IOEng //cIOJSON.js','file': '../../proxy.json'}"
}
```

# 3.保存时加入时间

原本保存的内容只有两个内容：

```js
ProxyContent:[
  {
    u:"119.101.118.126",
    p:"9999"
  }
]
```


现在需要增加保存时间为：

```js
ProxyContent:[
  {
    u:"119.101.118.126",
    p:"9999",
    ft:1580267620322
  }
]
```

# 4.修改检测数据库是否存在该代理

因为前期存入`数据库`只存入`代理地址`、`代理端口`，故在新增字段之后是会检测不到存在的，所以只能修改为查找数据库时只查找`地址`以及`端口`，以针对 `MongoDB` 的`驱动`为例，修改一下：

```js
//修改前
checkProxyExist(objProxy = {}, funCB) {
    ....
      db.collection(that.dbSet.col).findOne(objProxy, {}, function(err, item) {
    ....
    };

//修改后
checkProxyExist(objProxy = {}, funCB) {
    ....
      db.collection(that.dbSet.col).findOne({ u: objProxy.u, p: objProxy.p }, {}, function(err, item) {
    ....
    };
```

# 5.修改循环流程

原本的`流程`图为：

![原始的流程图](/photo/InPost/18-1.png)

更改后的流程图：

![好像很复杂](/photo/InPost/18-2.png)

# 6.修改这个算是完成了吧

测试好像还需要一段时间，就先整理一下今天要做的事情：

- [x] 把前期做的代理池修改一下
- [ ] 在腾讯云服务器上配置PM2
- [ ] 找寻 Python 爬虫相关包及方法
- [ ] 思考关键字舆情该怎么保存
- [x] 吃个好早餐