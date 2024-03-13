---
layout: post
title:  伍- MongoDB登陆那些小事
date:   2024-03-13 16:43:14 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

关于`MongoDB`，刚从`3.X`换成了`4.X`，其实已经不是很想懂到底什么回事了，引擎不对啊、常用的工具无法登陆啊一堆问题，不过其实这些都不是很是问题，顶多把数据库删掉重做呗（这是不负责任的）。

重申一次，没什么事千万`别直接升级`，请先`备份`！

# 1、关于MongoDB跨版本登陆

服务器端运行`4.X`的时候，客户端使用`3.X`是无法登陆的，完事。

# 2、MongoDB权限的问题

首先，`MongoDB`有许多权限，以下有说明：
>Built-In Roles（内置角色）：
    1. 数据库用户角色：read、**readWrite**;
    2. 数据库管理角色：dbAdmin、**dbOwner**、userAdmin；
    3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
    4. 备份恢复角色：backup、restore；
    5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
    6. 超级用户角色：root
    // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
    7. 内部角色：__system

引用自 [追不上老鼠的猫](https://me.csdn.net/u011191463)的博客，不过估计他也是引用其他人的（因为看到网上一堆一样的）。

------

关键的只是上面加粗的两个权限，其中只赋予`dbOwner`权限是不能读写的，必须同时赋予`readWrite`权限。

###### P.S. 常用命令

以下这些也是抄自上述的博客：

1. 创建用户
```bash
> use admin
switched to db admin
> db.createUser(
...   {
...     user: "dba",
...     pwd: "dba",
...     roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
...   }
... )
Successfully added user: {
    "user" : "dba",
    "roles" : [
        {
            "role" : "userAdminAnyDatabase",
            "db" : "admin"
        }
    ]
}
```


不行了，我还是引用[百度](https://jingyan.baidu.com/article/d169e18609d989436611d82e.html)。

2. 删除用户

```bash
db.dropUser('usertest')
```

3. 修改权限

```bash
db.updateUser("usertest",{roles:[ {role:"read",db:"testDB"} ]})
#注：updateuser它是完全替换之前的值，如果要新增或添加roles而不是代替它
#则使用方法： db.grantRolesToUser() 和 db.revokeRolesFromUser(）
------------------------------------------------------------------------------------------
db.grantRolesToUser("usertest", [{role:"readWrite", db:"testDB"},{role:"read", db:"testDB"}])   # 修改权限
db.revokeRolesFromUser("usertest",[{role:"read", db:"testDB"}])   # 删除权限：
```


###### P.S.. 我还是去冲凉吧。