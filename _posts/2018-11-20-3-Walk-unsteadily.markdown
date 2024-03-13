---
layout: post
title:  叁-蹒步
date:   2024-03-13 16:39:14 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
description: 艰难的往前走
tail: 就是完成了
---

# 一、Import CSV to Mongodb by Java

传说你标题写满你的关键字的话你的```SEO```就会好一点，关于这一点我觉得是一个迷信，但我还是这样做了（虽然其实我并不觉得要这样做，因为这样并没什么意味）。

我正在想列一个表格来说明时间线，但是还在纠结应该放在最开头还是最后头，好吧，思考了很久（大概10多秒），我还是决定放在最上面。

| 时间 | 内容 |
| ------ | ------ |
| 2018-11-20 16:03 | 开始写第一章 |
| 2018-11-20 16:15 | 尝试用Java链接MongoDB |
| 2018-11-20 17:51 | 正在崩溃 |
| 2018-11-20 18:29 | 能读取MongoDB内容，暂时休战 |

好吧，反正现在想做的东西就是：

**现在有一堆题目，想要去重，但很多题目格式或者字眼是有部分改动的，所以需要分词后用欧氏距离来把相似度高的去重。但此篇先做个最基础的，就是把题目入库。**

# 二、Java链接MongoDB

首先，我是一个啥也不懂的人，如何下载Java其他人写的包呢！？放在哪里呢！？啊！！好慌张啊。

还是先打开```Eclipse```吧，然后打开[菜鸟教程](http://www.runoob.com/mongodb/mongodb-java.html)，它上面说，```Java```工程是需要用```Maven```管理，草，什么是```Maven```！
- 极端问题--`MongoDB`登陆验证

不管```Maven```了，好不容易下载了`MongoDB`驱动的`Jar`，在`Java`工程根目录下新建一个`lib`文件夹放置并链接到当前工程时，根据教程的内容，首先遇到一个极端的问题--教程写，链接数据库时附带用户名和密码登陆是需要引入：

```java
import com.mongodb.MongoClient;
import com.mongodb.MongoCredential;
import com.mongodb.ServerAddress;
import com.mongodb.client.MongoDatabase;
```

最后一个引入了，但前面那三个是什么鬼！？我怎么都引入不了！！于是我放弃了，继续百度。

结果百度都是不行，正当我放弃的时候，我碰巧看到那个[驱动的官网
](http://mongodb.github.io/mongo-java-driver/3.9/driver/getting-started/installation/)里面有一行
> If downloading `mongodb-driver-sync` manually, you must also download its dependencies: [`bson`](https://oss.sonatype.org/content/repositories/releases/org/mongodb/bson/3.9.0) and [`mongodb-driver-core`](https://oss.sonatype.org/content/repositories/releases/org/mongodb/mongodb-driver-core/3.9.0)

然后你会发现其实，即使成功引入了那个包，那个教程的代码还是在报错！！

这个时候我觉得我是需要放弃的，但是我的内心不允许我放弃，于是我觉得，看官网的教程是最稳妥的，结果被我找到了[MongoDB官网](http://mongodb.github.io/mongo-java-driver/3.9/driver/tutorials/connect-to-mongodb/)。

MD，这才是教程嘛！

直接就跟你说：

```java
String user; // the user name
String database; // the name of the database in which the user is defined
char[] password; // the password as a character array
// ...

MongoCredential credential = MongoCredential.createCredential(user, database, password);

MongoClientSettings settings = MongoClientSettings.builder()
        .credential(credential)
        .applyToSslSettings(builder -> builder.enabled(true))
        .applyToClusterSettings(builder ->
            builder.hosts(Arrays.asList(new ServerAddress("host1", 27017))))
        .build();

MongoClient mongoClient = MongoClients.create(settings);
```

好了，至此我终于能链接到MongoDB了。

```bash
十一月 20, 2018 5:29:23 下午 com.mongodb.diagnostics.logging.JULLogger log
信息: Cluster created with settings {hosts=[127.0.0.1:27017], mode=SINGLE, requiredClusterType=UNKNOWN, serverSelectionTimeout='30000 ms', maxWaitQueueSize=500}
Connect to database successfully

```

# 三、测试是否真的链接上MongoDB

我觉即使按教程说的，输出了连接成功也不一定是链接成功的，所以我必须查出数据才是链接成功的你说对不？

- 必须先说一件事情，我觉得`Eclipse`有个东西很贴心，如下图：
![贴心小Eclipse](photo/InPost/14763760-edacda124b7b3af1.png)

好了，又到诱导放弃的阶段了，我尝试了好久好久，他一直在报错，到底是什么问题？是因为我用了`MongoDB 4.0`还是因为代码出错！？我可是用官网的代码的！继续研究一下。
- 我崩溃了

经过两个小时我知道我崩溃了，原来我登陆那里设错了，我的`MongoDB`登陆是没有开`SSL登陆`的，结果前期一直在登陆那里加了一句`.applyToSslSettings(builder -> builder.enabled(true))`，前期一直是登陆不上的其实，现在可以了。

# 四、全部代码

其实到最后肯定需要列出所有代码的，以下就是了：

```java
package top.sanshishu;

import java.util.Arrays;

import org.bson.Document;

import com.mongodb.*;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;

public class MainToDo {

	public static void main(String[] args) {
		// TODO 自动生成的方法存根
		try {

			String strMDBUser; // the user name
			String strMDBDB; // the name of the database in which the user is defined
			char[] achMDBPW; // the password as a character array
			// ...

			strMDBUser = "Berry";
			strMDBDB = "dbBBS";
			achMDBPW = "Berry".toCharArray();

			MongoCredential credential = MongoCredential.createCredential(strMDBUser, strMDBDB, achMDBPW);

			MongoClientSettings settings = MongoClientSettings.builder().credential(credential)
					.applyToClusterSettings(
							builder -> builder.hosts(Arrays.asList(new ServerAddress("127.0.0.1", 27017))))
					.build();


			MongoClient mongoClient = MongoClients.create(settings);

			// 连接到数据库
			MongoDatabase mongoDatabase = mongoClient.getDatabase("dbBBS");
			System.out.println("Connect to database dbBBS successfully");

			// 列出所有Collection的名字
			for (String name : mongoDatabase.listCollectionNames()) {
			    System.out.println(name);
			};


			MongoCollection<Document> collection = mongoDatabase.getCollection("tbMessage");
			System.out.println("集合 tbMessage 选择成功");

			// 检索所有文档
			/**
			 * 1. 获取迭代器FindIterable<Document> 2. 获取游标MongoCursor<Document> 3. 通过游标遍历检索出的文档集合
			 */
			FindIterable<Document> findIterable = collection.find();
			MongoCursor<Document> cursor = findIterable.iterator();
			try {
				while (cursor.hasNext()) {
					System.out.println(cursor.next().toJson());
				}
			} finally {
				cursor.close();
			}

			//就显示第一条文档
//			Document myDoc = collection.find().first();
//			System.out.println(myDoc.toJson());

		} catch (Exception e) {
			System.err.println(e.getClass().getName() + ": " + e.getMessage());
		}
	}
}

```

# 五、没错！今天是没有CSV什么鸟事的

结果我给自己定下的目标，今天仅仅是能链接到`MongoDB`而已，其他什么都没做过，好可怜啊我觉得我，今晚如果有空的话继续写呗。

# 六、本章小结

小结的问题呢，有以下几点得着：
1. 没事别百度，直接找官网；
1. `Java包`的引入其实很方便，但最好还是学用`Maven`；
2. `MongoDB`很麻烦，`Java`很麻烦；
3. `MongoDB`链接模式有很多种，需要找到自己设置数据库的那种；
5. `Java`遍历不像`JS`那种用`ForEach`或者`For`，一般用`游标`配`while`。

**未完事**