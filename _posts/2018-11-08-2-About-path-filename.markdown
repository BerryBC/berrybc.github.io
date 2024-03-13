---
layout: post
title:  贰-关于路径以及文件名
date:   2024-03-13 15:45:14 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
description: 路径以及文件名
tail: 就是完成了
---
# 1、为什么是路径以及文件名？

你猜呢？

这东西是很可怕的，特别是你用```Windows```用多了，你会发现对于这方面的处理简直是喵了狗，**大小写不分、文件名或目录名有空格、建立特别深的文件夹.......**这种情况一大堆。当在```Linux```上时，就会发现建立的程序莫名其妙的失败、想复制修改文件特别麻烦等情况。特别还有相对绝对这种概念真太他喵烦躁了。

所以对于烦躁的东西我觉得是比较好说的，所以先说路径。

# 2、文件大小写

其实这东西在```Windows```里面是根本不用在意的东西，可是在```Linux```就悲剧了，就好像：

```
zhifu.JPG
跟
zhifu.jpg
```

这两个文件，在```Windows```下是表达同一个文件，但在```Linux```上，他们是两个不同的文件！

（虽然这是常识）

但就因为这个文件名大小写的原因我耗费了1个小时的时间去找为什么。

# 3、相对路径、绝对路径

基本在中国从小学过```DOS```的人都知道  ```CD ..```  这个意思是返回上级目录，然后对于路径来说， ```..``` 是代表上级目录，但基本对于没用过```Linux```的人来说 ```/``` 这个符号其实没什么人知道是根目录的意思，对于```Windows```根目录是酱紫的 ```C:\``` 。

1. ```/ste```
    - 表征绝对路径下的根目录；
    - 在```Linux```下，它是代表系统根目录下的```ste文件或文件夹```，无论你当前在哪个目录，他都指向根目录的```ste文件或文件夹```；
    - 在```网站```里，它是代表网站根目录下的ste，无论你当前是在```https://sanshishu.top/photo/album?dir=iPhoneSE```还是在```https://sanshishu.top/bbs```，它都是指向```https://sanshishu.top/ste```的。

2. ```../ste```
    - 表示相对于当前文件夹的```上一级目录```；
    - 在```Linux```下，如果你当前在```/home/BerryBC/hellobaby/loveyou```，他是指向```/home/BerryBC/hellobaby/ste```；
    - 在```网站```里，当前在```https://sanshishu.top/bbs```的话，它是指向```https://sanshishu.top/ste```，如果你在```https://sanshishu.top/photo/album?dir=iPhoneSE```，它是指向```https://sanshishu.top/photo/ste```。
    - 当然```..```是可以叠加的，例如你当前在```/home/BerryBC/hellobaby/loveyou```，这个时候你用```cd ../../baby```，就到达了```/home/BerryBC/baby```

3. ```./ste``` 类似但不等价于 ```ste```
    - 现在说明比较尴尬；
    - 在```网站```里表示当前目录下的```ste文件或文件夹```，例如以下是等价的：
    ```html
    <img src="./images/me.JPG" >
    上下是等价的
    <img src="images/me.JPG" >
    ```
   - 但在```Linux```下，```./nginx```与```nginx```不一定是等价的：
    ```Bash
    --假如nginx是一个文件夹，那么：
    cd ./nginx
    以及
    cd nginx
    是等价的
    ----------
    --但加入nginx是一个可执行文件时
    nginx
        -bash nginx: command not found
    ./nginx
        nginx: [alert]
    ````

    明显看到直接执行```nginx```是没有办法执行这个文件，需要用```./nginx```才行，这个其实需要定义[环境变量(只是随手一搜，我不知道说得好不好)](https://blog.csdn.net/LF_2016/article/details/53843580)。

    就是说，作为一个路径，有没有```./```其实是一样的，但如果需要执行文件的话就不一样了。

4. ```/```
    - 当然这个就是绝对路径的根目录了。无论```网站```（例如直接返回```https://sanshishu.top/```）还是```操作系统```（直接返回根目录，```Linux```为返回到```/```目录，```Windows```为返回到```C:\```目录）。

5. ```~/```
    - 这个呢！紧张了，因为你在```ASP.net```以及```Linux```里面是完全两回事！！
    - 在```ASP.net```里面，他代表当前```Web应用程序```的目录，详情可以参看[一个论坛帖子]([/photo/InPost/14763760-819d378c30955ae3.png](https://bbs.csdn.net/topics/390684664/))。
    > 你有一个网站，你在iis里面发布为www.abc.com，这是一个网站，然后你又做了一个网站，在iis里面你是以虚拟目录放在www.abc.com下的（就是你是在www.abc.com下右键，新建虚拟目录这样形式），比如虚拟目录名称叫test，那么这时候两个路径就不一样了，比如你这个 test虚拟目录下有一个images文件夹，文件夹里面有一个bg.jpg，那么你访问的时候其实是www.abc.com/test/images/bg.jpg，这个时候，你在test程序里面的路径如果引用这个图片的时候用的是~/images/bg.jpg是没有任何问题的，但是如果你引用的是/images/bg.jpg那就表示引用的是www.abc.com/images/bg.jpg

    - 在```Linux```里面呢（其实```Windows```的```PowerShell```也一样），他代表当前用户的目录，我登陆```Linux```后无论屈到哪个目录，只需要```cd ~/```就能返回到```/home/用户名```这个目录下（还有什么骚操作我没查哦，管他呢）。

# 4、用Nginx反向代理后的路径处理

因为我选用的建站方案是比较奇怪的（其实就是懒惰），逻辑如下图：
![好尴尬的图片](/photo/InPost/14763760-819d378c30955ae3.png)
我的BBS以及Photo分别是两个```Express```工程，然后分别是开放在3081端口以及3082端口，再用```Nginx```反向代理到本地的这两个端口。还有部分静态文件（如顶级页面的index文件等）。

但！问题来了！

1. **相对路径的使用情况**
2. 因为在单工程调试的时候，我引用的所有```css```以及```js```文件都是用绝对路径的，但当放到网站上的时候，绝对路径却变成了引用静态文件部分（就是网站根目录下的），例如：
    ```html
    --当我源码为
    <script src="/javascripts/main.js" type="text/javascript"></script>

    --在单工程调试时我是引用了

    https://127.0.0.1:3081/javascripts/main.js

    --放到网站上我本来想引用

    https://sanshishu.top/bbs/javascripts/main.js

    --却变成了引用

    https://sanshishu.top/javascripts/main.js

    ```
    这个时候因为引用```js```时在路径前使用了```/```，所以变成了绝对路径。在单工程调试的时候是完全没问题，可是在```Nginx```使用了```反向代理```之后，就引用错了位置。

3. **反向代理设置**
    这个是```Nginx```里面设置反向代理之后请求路径的转换问题，很复杂（因为我也不是很懂）。

# 5、完事

基本有问题的就都有问题，虽然都是基本功问题，可是写一次记录就很有助于由意识过度到潜意识这个过程，不是么？