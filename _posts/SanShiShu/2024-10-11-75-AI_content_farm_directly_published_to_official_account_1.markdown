---
layout: post
title:  柒拾伍- AI内容农场生产文章自动发布至公众号 (一)
date:   2024-10-11 12:08:33 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

# 一、内容农场 X AI

看过很多的新闻说 `AI 产生` 内容 **污染网络**，我也想试一下到底能污染成怎样。

恰逢最近看到 [智谱](https://open.bigmodel.cn/) 居然有一个免费的大模型！！马上试一下！！

![alt text](/photo/InPost/SanShiShu/75/image.png)

然后为了编写爆款的内容，我选用这个 `内容农场` 的种子是来源于 `微博热搜`，让生长出来的垃圾文章更加火爆

# 二、编写代码

关于代码的编写，我决定用 `脚本语言 Python` 进行编写 ( 主要就是脚本语言比较方便 )。

但很多东西我又不是很会写 ( 特别是 request 那些，我真的不太熟 )

于是！聪明的我决定直接用 [智谱 ChatGLM](https://chatglm.cn/) 帮我写代码

例如：

![alt text](/photo/InPost/SanShiShu/75/image-1.png)

直接放到上面就很简单了

# 三、内容发布平台

因为搜过了一下，`小红书` 跟 `知乎` 等平台均没有直接 Post 的 API 接口，只能用 `selenium ` 等测试工具模拟登录 ( 就好烦 )

所以只能通过 `微信公众号` 这个有自动发布 API 的方式发布

![alt text](/photo/InPost/SanShiShu/75/image-2.png)

基于这个接口我们就可以全自动的发了

# 四、感觉

现在 AI 生成的内容越来越多，垃圾是越来越多，举个例子：

### 1) 苏醒的热搜

下面这个内容是微博原文：

![alt text](/photo/InPost/SanShiShu/75/image-3.png)

然后下面这个文章是搜狐的文章：

![alt text](/photo/InPost/SanShiShu/75/image-4.png)

再下面这个是通过内容农场长出来的垃圾文章：

![alt text](/photo/InPost/SanShiShu/75/image-5.png)

### 2) 余华英的热搜

![alt text](/photo/InPost/SanShiShu/75/image-6.png)


### 3) Kimi的热搜

![alt text](/photo/InPost/SanShiShu/75/image-7.png)


明显有以下问题：

1. 内容跟微博热搜完全不符
2. 幻觉的内容类似
3. 文笔虽然不粗糙，错别字也没看见，但就是有些缺了个字，导致意义完全不一样

AI 生成的东西其实质量是很低的，而模型是不会进化，只能学习 ( 而学习的来源又因为更多 AI 的生成导致越来越垃圾 )

只要在以后有一种算法是能让 AI 自行进化 ( 自行扩展自己的模型，扩展后不需要重新训练 ) ，才能从这种困局中跳出

# 五、代码

完整代码在 [这里](https://berrybc.github.io/%E6%9B%B4%E6%97%A0%E8%81%8A%E7%9A%84%E4%BA%BA%E7%94%9F/Auto_Write_Article/)

往后会输出更详细的思路以及代码解释