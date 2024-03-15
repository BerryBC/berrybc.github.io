---
layout: post
title:  廿伍-爬虫接受JS渲染
date:   2020.02.07 00:55:15 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 开始

研究了好久，终于发现了如何做，不过无法异步完成，只能用 `Supervisor` 开多进程去做了。

# 2.使用 selenium 渲染JS

首先我必须放出参考文章
- [https://blog.csdn.net/hy_696/article/details/80101803](https://blog.csdn.net/hy_696/article/details/80101803)
- [https://blog.csdn.net/qq_23926575/article/details/72571637](https://blog.csdn.net/qq_23926575/article/details/72571637)
- [https://zhuanlan.zhihu.com/p/97785241](https://zhuanlan.zhihu.com/p/97785241)
- [https://blog.csdn.net/qew110123/article/details/87708659](https://blog.csdn.net/qew110123/article/details/87708659)
- [https://zhang0peter.com/2018/10/29/selenium/](https://zhang0peter.com/2018/10/29/selenium/)
- [https://blog.csdn.net/weixin_43968923/article/details/87899762](https://blog.csdn.net/weixin_43968923/article/details/87899762)
- [Selenium 官网](https://selenium-python.readthedocs.io/api.html)

实在参考太多了，不能一步步来了。

尝试过很多种 `JS渲染` 的方式，最后还是选择了 `selenium` ，然后后来为了安装那个 `chromedriver` 也花费了很大力气，现在叫我重来一遍我也真忘了怎么做了。

以下是`单一请求`的代码：

```python
# 测试新方法
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from bs4 import BeautifulSoup
import time

# 各种设置及配置，可参考上述链接
options= Options()
options.add_argument('--no-sandbox')
options.add_argument('--disable-gpu')
options.add_argument('--hide-scrollbars')
options.add_argument('blink-settings=imagesEnabled=false')
options.add_argument('--headless')
options.add_argument('--incognito')
options.add_argument('--ignore-certificate-errors')
options.add_argument('--disable-software-rasterizer')
options.add_argument('--disable-extensions')
options.add_argument('--user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.87 Safari/537.36"')
options.add_argument('--window-size=1280x1024')
options.add_argument('--start-maximized')
options.add_argument('--disable-infobars')
# options.add_argument("–proxy-server=http://ip:端口")

# ChromeDriver 也是一个坑，安装也是参照上述某个链接
browser = webdriver.Chrome('/usr/bin/chromedriver',chrome_options = options)
# 可以参照官网 API
browser.set_page_load_timeout(20)
browser.set_script_timeout(20)
browser.implicitly_wait(20)
browser.get('https://weibo.com/1239160407/GcSidx58Y')
# 等待JS运行完及各种跳转
time.sleep(10)
html=browser.page_source
# 必须退出，不然浏览器进程会驻留
browser.close()
browser.quit()
soup = BeautifulSoup(html, 'lxml')
import re
# 爬微博必须 div 爬
data = soup.select('div')
for item in data:
    result =  item.get_text()
    strOut=''
    strOut=re.sub('\n+', '\n', result)
    if len(strOut)>10 :
        print(strOut)

```

# 3. 后续工作
- [ ] 为交互界面页增加删减含某些关键字的内容。
- [ ] 增加展示数据库数据量的页面
- [ ] 有可能写专题爬虫，如微博或者其他