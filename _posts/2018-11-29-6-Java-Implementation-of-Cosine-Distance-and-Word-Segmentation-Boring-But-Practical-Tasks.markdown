---
layout: post
title:  陆-余弦距离以及分词等无聊实宜的Java实现
date:   2018.11.29 17:58:10 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

# 1、起源

因为一个叫做`谭翠平`的女人的懒惰，使得另外一个更懒惰的人（我）作出了如此无聊的事情。

而这件事情呢，就是要把一堆题目里面找出重复的然后删掉，有人会问，为什么不在`Excel`里面去重就行了呢？看到下面这图就懂了：
![恶心的题目](/photo/InPost/6-1.png)

明显发现即使删掉`逗号`、`问号`等题目来去重，有些重复题目也是不能直接去重的。

#  2、思考出路

既然这样下去不行，那么该如何做了呢？

忽然想起之前看过一篇文章介绍过[网易云音乐的推荐算法](https://blog.csdn.net/feelinghappy/article/details/81626008)，里面有一个 [余弦相似度_百度百科](https://www.baidu.com/link?url=JHBUmOu48NGhpUdVzNngwsOZ4WY5fKhxgZz6PCFKfFnW8O1I1IUGo7nyxIBTkgL4HmJXsMp1BU6hhXcd733LbHejGVuj800SXpN9yC7F-U_yx5loS-MvQor2PQqzzVD10UoxNNNvQ49Z5ULV9I4g__&wd=&eqid=87605740000029b7000000035bffa945)使我惊为天人！

我可以通过题目的各个`关键字`作为一个`维度`来计算不同题目的余弦相似度，然后判断`相似度`比较高的题目就可以定为相同的题目！不是么！？

# 3、需要思考的人生不止 - 分词的Java实现

对于`中文分词`，我以前用`JS`的时候是做过的，`Java`下有许多`分词`包。但问题来了，我该选用哪个呢？

诶，有一个[介绍](https://github.com/ysc/cws_evaluation)哦！还有评分呢~

二话不说先选速度评分第二（[HanLP](https://github.com/hankcs/HanLP)，别问我不选第一，因为那个我不会用）的来用，使用效果如下：
![很不错哦！](/photo/InPost/6-2.png)

但！忽然发现一个问题，这个包的字典是需要另外下载一个100M左右的字典，虽然这不是硬伤但我还是觉得很懒惰，所以选用了另外一个大小为22M的包[ansj分词器](https://github.com/ansjsun/ansj_seg)（`自定义字典`比较方便，而且直接能用）。
![使用效果](/photo/InPost/6-3.png)

我觉得其实，差不多，但这个比`HanLP`有个不足之处就是，`空格`跟`下划线`等判断词性为`Null`，而且`加载速度`比较慢（虽然不是硬伤啦）。

另外还有一个问题，对于`专业名词`之类的肯定得需要自建`字典`的，这个官网上有详细介绍，可以后续再弄。

**至此，分词完毕。**

# 4、余弦相似度。

我是根据`关键词`的`词频`进行计算的，具体公式可以看 [余弦相似度_百度百科](https://www.baidu.com/link?url=JHBUmOu48NGhpUdVzNngwsOZ4WY5fKhxgZz6PCFKfFnW8O1I1IUGo7nyxIBTkgL4HmJXsMp1BU6hhXcd733LbHejGVuj800SXpN9yC7F-U_yx5loS-MvQor2PQqzzVD10UoxNNNvQ49Z5ULV9I4g__&wd=&eqid=87605740000029b7000000035bffa945)，大概思路如下：
![好长啊....](/photo/InPost/6-4.png)


具体代码如下：
```java
	/**
	 * @param alstrSpltiWord	第一题关键词数组
	 * @param alintWordFrq		第一题关键词词频数组，与关键词位置对应
	 * @param alstrSpltiWordIn	第二题关键词数组
	 * @param alintWordFrqIn	第二题关键词词频数组，与关键词位置对应
	 * @return	返回double类型变量，范围为 0-1 的余弦相似度
	 */
	double cosineSimilarity(ArrayList<String> alstrSpltiWord, ArrayList<Integer> alintWordFrq,
			ArrayList<String> alstrSpltiWordIn, ArrayList<Integer> alintWordFrqIn) {
		//定义计算中间值及结果
		double fResult = 0;
		double dA = 0;
		double dB = 0;
		double dAB = 0;
		//定义两题关键字并集的关键字位置及各题对于这些关键字的词频
		Map<String, Integer> msHasWord = new HashMap<>();
		int intHWNum = 0;
		ArrayList<String> asCheckWord = new ArrayList<>();
		ArrayList<Integer> aiCehckOne = new ArrayList<>();
		ArrayList<Integer> aiCehckTwo = new ArrayList<>();
		//把第一题的词频导入并集，并生成第一题的词频
		for (int intI = 0; intI < alstrSpltiWord.size(); intI++) {
			String strEle = alstrSpltiWord.get(intI);
			if (!msHasWord.containsKey(strEle)) {
				msHasWord.put(strEle, intHWNum);
				intHWNum++;
				asCheckWord.add(strEle);
				aiCehckOne.add(0);
				aiCehckTwo.add(0);
			}
			aiCehckOne.set(msHasWord.get(strEle), alintWordFrq.get(intI));
		}
		//对第二题进行处理
		for (int intI = 0; intI < alstrSpltiWordIn.size(); intI++) {
			String strEle = alstrSpltiWordIn.get(intI);
			if (!msHasWord.containsKey(strEle)) {
				msHasWord.put(strEle, intHWNum);
				intHWNum++;
				asCheckWord.add(strEle);
				aiCehckOne.add(0);
				aiCehckTwo.add(0);
			}
			aiCehckTwo.set(msHasWord.get(strEle), alintWordFrqIn.get(intI));
		}

		//进行计算
		for (int intI = 0; intI < asCheckWord.size(); intI++) {
			dAB += aiCehckOne.get(intI) * aiCehckTwo.get(intI);
			dA += Math.pow(aiCehckOne.get(intI), 2);
			dB += Math.pow(aiCehckTwo.get(intI), 2);
		}
		fResult = dAB / (Math.pow(dA, 0.5) * Math.pow(dB, 0.5) + 1E-20);

		//输出
		System.out.println("---------------------");
		System.out.println(alstrSpltiWord);
		System.out.println(alstrSpltiWordIn);
		System.out.println(asCheckWord);
		System.out.println(fResult);
		System.out.println(aiCehckOne);
		System.out.println(aiCehckTwo);

		return fResult;
	}
```

# 5、桥接处理

因为我打算是做一个`JSP`然后用一个500M内存的`VPS`去跑的，所以我把题目全部从`CSV`上读取到`MongoDB`里面去，然后再从`MongoDB`里面导出所有未处理的题目进行处理。本来打算减少`内存`消耗只是消耗`硬盘`，但总感觉其实`MongoDB`也是耗内存的。

我在自己的电脑上跑就发现`内存`占用得很厉害，我害怕在`VPS`上跑会跑死那台机子，不过那些东西，`God Knows~`

# 6、效果

因为`余弦相似度`达到多少我算是相似呢？其实我只是拍脑袋写了个80%，但我总觉得感觉还是挺好的，以下为部分结果：

##### 较好的判断出相同题目：
![第1组](/photo/InPost/6-5.png)
![第2组](/photo/InPost/6-6.png)
![第3组](/photo/InPost/6-7.png)

##### 有争议的判断：
![第1组](/photo/InPost/6-8.png)

##### 错误的判断：
![第1组](/photo/InPost/6-9.png)
![第2组](/photo/InPost/6-10.png)

# 7、改进

其实我觉得不用改进了啦，但实际说需要改进的话，我觉得可以从两点着手：

1. 自定义字典
对于`通信专有名词`是必须要进行`自定义词典`（或许可以用[HanLP](https://github.com/hankcs/HanLP)，里面有`新词发现`，但考虑效率及`硬件开销`方面还是`自建字典`比较稳妥）；
2. 专有名词词频加权
从上述题目看到例如**“MME的具体作用及接口”**和**“SGW的具体作用及接口”**里面的**`MME`**以及**`SGW`**被其他关键词被抹消了，但其实这两个才是重点，后续可以在计算词频的时候把相应的`关键字`给加个权。

# 8、后续

这个历时3天（你需要给点面子初学者呵）的东西终于做完了，但是大部分都是测试代码，后面肯定需要重写，并且写在`JSP`里面（方便别人上网使用），那以后的事情就以后再说了。