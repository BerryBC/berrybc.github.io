---
layout: post
title:  自动写文章并发表的代码
date:   2024-10-10 19:09:20 +0800
categories: 更无聊的人生
author: 崔秉龙
location: Guangzhou, China
---


## Python 代码

```python

'''
Descripttion: 主程序
Version: 1.0
Author: BerryBC
Date: 2024-10-08 17:54:50
LastEditors: BerryBC
LastEditTime: 2024-10-10 19:00:46
'''


import requests
import json
from bs4 import BeautifulSoup
from urllib.parse import urlparse, parse_qs
from zhipuai import ZhipuAI
import mysql.connector
from datetime import datetime, timedelta
import asyncio
import time



#      ........................        .   5PPY   .     5P5Y.
#      &@@@@@@@@@@@@@@@@@@@@@@@.     J&@B. @@@@ .G@@5  7@@@@
#      5BGBBGBBBBGGGGB&@@@@@@@#.     .#@@Y.@@@& 5@@#: .@@@@P^^^^^~^
#                 .~5#@@@@#5~.      ^B#@&BB@@@@#B&@#^ #@@@@@@@@@@@@.
#    Y&&&?:5&?   5@@@@#Y~~GY:B&&&:  ~&#B&@@@@@@####&~5@@@&?J?Y@@@&?
#    G@@@P5@@@@5 P@@@& :G@@@&@@@@:   .~5&@@@@@@5B?: Y@@@@G   :@@@5
#    G@@@Y .G@&Y P@@@# G@@B^ @@@@:  #@@@@5^@@@@#@@@@@@@@@@.  5@@@^
#    G@@@Y   ^7PB#@@@&J?!:   @@@@:  ~&P^.5G@@B5  ^PY?@&@@@5 .@@@&
#    G@@@P^Y#@@@@@@@@@@@@&5^.@@@@:  7PJ?B@@@@YJYJJJ7 . Y@@@~B@@@~
#    G@@@&@@@@P~ P@@@&~P@@@@&@@@@:  B@@@@@@&@@@@@@@&    &@@@@@@5
#    G@@@Y^5^7GPG@@@@#   ~BP:@@@@:   :&@@@J.  7@@@&:    .@@@@@B
#    G@@@Y   .@@@&&&B:       @@@@:   !B&@@@@##@@@P     ~B@@@@@&?.
#    G@@@B7777JYJJJ?77777777?@@@@:  ..:7G@@@@@@@@&#?^5&@@@@&@@@@@B!
#    G@@@@@@@@@@@@@@@@@@@@@@@@@@@:  G@@@@@@#Y~7G&@BB@@@@@B^ .P@@@@^
#    ^777777777777777777777!7&&&&:   PGY!:       .  ^##?.     .?P


# -----------------------------------------------------------------------
# 函数 : 先抓取热搜
def funFetchWeiboHotSearch():
    # Fetch the hot search data from Weibo's AJAX endpoint and return it as a dictionary.
    url = "https://weibo.com/ajax/side/hotSearch"
    try:
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36'
        }
        response = requests.get(url, headers=headers)
        # Raises an HTTPError if the HTTP request returned an unsuccessful status code
        response.raise_for_status()
        return json.loads(response.text)
    except Exception as e:
        print(f"An error occurred: {e}")
        return None


# -----------------------------------------------------------------------
# 函数 : 发布文章
def funSubmitArticle(strAccToken,strMediaID):
    try:

        # 设置API的URL
        url = f"https://api.weixin.qq.com/cgi-bin/freepublish/submit?access_token={strAccToken}"

        # 设置要发布的草稿的media_id
        payload = {
            "media_id": strMediaID
        }

        # print(url)
        # print(strMediaID)

        # 发送POST请求
        response = requests.post(url, data=json.dumps(payload))


        # 打印响应内容
        # print(response.text)

        dictFB = json.loads(response.text)
        return dictFB

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")



# -----------------------------------------------------------------------
# 函数 : 去查 google 相关图片
def funGetPicture(strInKeyword):

    # 设置关键词和代理
    # keyword = "易烊千玺谈恋爱"
    proxy = {
        'http': 'http://127.0.0.1:1088',
        'https': 'http://127.0.0.1:1088'
    }

    # 返回的数组
    listFB = []

    # 搜索图片的URL
    search_url = f"https://www.google.com/search?q={strInKeyword}&tbm=isch&udm=2"

    # 设置请求头，模拟浏览器行为
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'
    }

    try:
        # 发送请求，使用代理
        response = requests.get(search_url, headers=headers, proxies=proxy)

        # 检查请求是否成功
        if response.status_code == 200:
            soup = BeautifulSoup(response.text, 'html.parser')
            # 查找图片链接
            # print(soup)
            images = soup.find_all("img", class_="DS1iW")
            # images = soup.find_all("img")
            # print(images)
            for img in images:
                try:
                    # 提取图片链接
                    img_url = img['src']
                    # img['src']
                    listFB.append(img_url)
                    # print(img_url)
                except KeyError:
                    continue
        else:
            print("请求图片失败，状态码：", response.status_code)
    except Exception as e:
        print(f"An error occurred: {e}")
        # return False
    return listFB



# -----------------------------------------------------------------------
# 函数 : 下载相关图片
def funDownloadImage(image_url, save_dir="./img"):
    """
    Download an image from a given URL and save it to a specified directory with the same name as in the URL.
    Args:
    - image_url (str): The URL of the image to download.
    - save_dir (str): The directory where the image should be saved.
    Returns:
    - bool: True if the image was successfully downloaded and saved, False otherwise.
    """
    proxy = {
        'http': 'http://127.0.0.1:1088',
        'https': 'http://127.0.0.1:1088'
    }
    # 设置请求头，模拟浏览器行为
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'
    }
    # 准备返回的数据
    strFBSaveImg = ''
    try:
        # Parse the URL to get the file name
        # url_path = urlparse(image_url).path
        # filename = url_path.split("/")[-1]

        # 解析URL
        parsed_url = urlparse(image_url)

        # 提取查询字符串参数
        query_params = parse_qs(parsed_url.query)

        # 打印所有参数
        # print("All query parameters:", query_params)

        # 提取特定的参数
        filename = query_params.get('q', [None])[0]  # 返回值是一个列表，我们取第一个元素
        filename = filename[4:]

        # Ensure the save directory ends with a slash
        if not save_dir.endswith("/"):
            save_dir += "/"

        # Full path where the image will be saved
        strFBSaveImg = save_dir + filename

        response = requests.get(image_url, headers=headers, proxies=proxy)
        response.raise_for_status()
        with open(strFBSaveImg, "wb") as file:
            file.write(response.content)

    except Exception as e:
        print(f"An error occurred: {e}")
    return strFBSaveImg


# -----------------------------------------------------------------------
# 函数 : 先抓取热搜
def funGetOffiAccAccToken(strAppid, strAppSecret):
    # Fetch the hot search data from Weibo's AJAX endpoint and return it as a dictionary.
    url = f"https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={strAppid}&secret={strAppSecret}"
    try:
        response = requests.get(url)
        # Raises an HTTPError if the HTTP request returned an unsuccessful status code
        response.raise_for_status()
        dictFB = json.loads(response.text)
        # print(response.text)
        strAccToken = dictFB['access_token']
        return strAccToken
    except Exception as e:
        print(f"An error occurred: {e}")
        return None


# -----------------------------------------------------------------------
# 函数 : 上传图片素材给那啥
def funUploadImgToOffiAcc(strAccToken, strFileName):
    try:
        # 设置API的URL
        url = "https://api.weixin.qq.com/cgi-bin/material/add_material"

        # 设置参数
        params = {'access_token': strAccToken, 'type': 'image'}

        # 设置表单数据
        description = f'{"title":"{strFileName}", "introduction":"{strFileName}"}'
        files = {
            'media': ('jj.jpg', open(strFileName, 'rb'), 'image/jpeg'),
            'description': (None, description, 'application/json')
        }

        # 发送请求
        response = requests.post(url, params=params, files=files)

        # 打印响应内容
        # print(response.text)

        dictFB = json.loads(response.text)
        return dictFB

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    except FileNotFoundError:
        print(f"The file at path {strFileName} was not found.")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")



# -----------------------------------------------------------------------
# 函数 : 上传文章
def funPublishDocument(strAccToken, listArticles):

    try:
        # 设置API的URL
        url = f"https://api.weixin.qq.com/cgi-bin/draft/add?access_token={strAccToken}"

        # 构建请求的数据
        data = {"articles": listArticles}

        # 发送POST请求
        response = requests.post(url, data=json.dumps(
            data, ensure_ascii=False).encode('utf-8'))

        # 打印响应内容
        # print(response.text)

        return json.loads(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# -----------------------------------------------------------------------
# 函数 : 插入热搜到数据库中
def funInsertWordsIfNotExists(connMySQL,listWords):
    # 数据库连接信息
    listFBWords=[]
    # 录入多少个热点
    intMax=5
    intNow=0
    try:
        with connMySQL.cursor() as cursor:
            # 获取当前时间
            current_time = datetime.now()
            # 计算10天前的时间
            ten_days_ago = current_time - timedelta(days=10)

            for word_dict in listWords:
                # 检查word是否在数据库中，且create_time在10天内
                sql_check = """
                SELECT * FROM db_genarticle.tb_hotsearch
                WHERE word = %s AND create_time > %s
                """
                cursor.execute(sql_check, (word_dict['word'], ten_days_ago))
                result = cursor.fetchone()

                # 如果不存在，则插入
                if result is None:
                    sql_insert = """
                    INSERT INTO db_genarticle.tb_hotsearch (word, create_time)
                    VALUES (%s, %s)
                    """
                    cursor.execute(sql_insert, (word_dict['word'], current_time))
                    # 提交事务
                    connMySQL.commit()
                    listFBWords.append(word_dict['word'])
                    intNow=intNow+1
                    if intNow>= intMax:
                        connMySQL.close()
                        return listFBWords

    except Exception as e:
        print(f"An error occurred: {e}")
    finally:
        # 关闭数据库连接
        connMySQL.close()
    return listFBWords




#                                     ...........................
#   B&&&&&&&&&&&&&&@!~&&&&&&&&&&&7    @@@@@@@@@@@@@@@@@@@@@@@@@@@7
#   G&##&@@@#@@@&##&~!@@@@@@@@@@@Y   .@@@@~::^@@@#:::J@@@J:::#@@@?
#        @@& B@@      ...... 5@@@Y    @@@@@&&&@@@@@@@@@@@@&&&@@@@7
#    #&&@@@@@@@@@&&&         Y@@@Y   :!777777777J@@@@&!7777777777^.
#    @@@G@@@P&@@G&@@. .......P@@@Y  !@@@@@@@@@@@@@@@@@@@@@@@@@@@@@G
#    @@# @@# G@@ P@@  &@@@@@@@@@@Y   ::~????????Y@@@@&?????????7::.
#    @@B?@@Y B@@^B@@  @@@@#BBBBB#!     J@@@@@@@@@@@@@@@@@@@@@@@&
#    @@@@@@. ~&@@@@@  @@@@.            Y@@@@YJJJJJJJJJJJJJJ#@@@&
#    @@@?J7 . .:?@@@  @@@@.    .       Y@@@@PPPPPPPPPPPPPPP&@@@&
#    @@@@@@@@@@@@@@@  @@@@.    &@#P    Y@@@@BBBBBBBBBBBBBBB&@@@&
#    @@@5!!!!!!!5@@@  @@@@    .@@@@    J@@@@YYYYYYYYYYYYYYY#@@@&
#    @@@#PGGGGGG#@@@  @@@@?:^^B@@@Y    Y@@@@PPPPPPPPPPPPPP5&@@@&
#    @@@@&&&&&&&@@@@. P@@@@@@@@@@&  #&&@@@@@&&&&&&&&&&&&&&#@@@@@&&&~
#    B##:       :&&#   ^J5PPPPPY!   JYYYJJJYYYYYYYYYYYYYYYYYJJJYYY5:

dictGblCfg = {'offiAccAppid': '1024',
              'offiAppSecret': '1024',
              'offiAccToken': '',
              'hotSearchKeywordList': [],
              'bigModelKey': '10241024',
              'mysqlPW':'1024',
              'mysqlUser':'LoveSummer',
              'mysqlPort':3698,
              'mysqlDB':'db_genarticle',
              'mysqlHosts':'127.0.0.1',
              'mysqlConn':None
              }



async def funGenArticle():
    #               .~YG.                 ~:           ^7?55                    ..:
    #              .&@@@@!              ^&@@&?.  ..... J@@@@#........   5BB##&&@@@@5 &@@@@@@@@@@@@@@@~
    #               :&@@&G.             .7#@@@@~&@@@@@@@@@@@@@@@@@@@@5  B@@@@@@@&#BY.@@@@#GBBBBBB@@@@!
    #    ?@&&&&&&&&&&@@@&&&&&&&&&&&@G      :BB: YGGPG@@@@@&GGGPG&BPGB7   :..#@@@^    @@@@.       #@@@!
    #    J@@@@@@@@@@@@@@@@@@@@@@@@@@B    .7.       :B@@@&7    ?#@&~     :^^:&@@@J^^: @@@@&#######@@@@!
    #               !@@@@?              5@@@#7. .~B@@@@G^::^^!#@@@@#~   @@@@@@@@@@@& #&&&&@@@@@@@&&&@~
    #               ^@@@@!              :5@@@@#.5@@@@@@@@@@@@@@@@@@@@#. 7J?#@@@@P7J! .................
    #      ^~^^^^^^^Y@@@@5^^^^^^^^~        !B7   PBGPPYJJJ??!!77!!B@B7.   :@@@@@!.  G@@@@@@@@@@@@@@@@@7
    #      @@@@@@@@@@@@@@@@@@@@@@@@!       !~.    P@&&: &&&# :@@@G       ^@@@@@@@@#~YGGGGGG@@@@#PGGGGG^
    #      5PPPPPPPP#@@@@#PPPPPPPPP:      ^@@@&.  #@@@^.@@@@ :@@@#      ?@@@&@@@#@@@5. .. .@@@@7 ....
    #               ^@@@@7                #@@@P   @@@@:.@@@@ :@@@# .&B? B@@~B@@@^.Y:.@@@@@@@@@@@@@@@@5
    #               ^@@@@7               7@@@@.  5@@@& .@@@@ :@@@# ^@@& .@7 B@@@^    JYYYJ5@@@@GJYYYY^
    #  :GPPPPPPPPPPP#@@@@#PPPPPPPPPPPG~ .@@@@?  5@@@@^ .@@@@ ^@@@# J@@5  .  B@@@^  :......:@@@@7......:
    #  !@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@Y B@@@& ^&@@@&^  .@@@@ .@@@@&@@@:     #@@@^ ^@@@@@@@@@@@@@@@@@@@@:
    #  .!~~~~~~~~~~~~~~~~~~~~~~~~~~~~!. ^75B^  :P&?     5PPY  :P####5:      G@@@^ .YYYYYYYYYJJJYYYYYYYY


    while True:
        # -----------------------------------------------------------------------
        # 一、配置整体配置
        try:
            dictGblCfg['mysqlConn'] = mysql.connector.connect(
                host=dictGblCfg['mysqlHosts'], user=dictGblCfg['mysqlUser'], password=dictGblCfg['mysqlPW'], port=dictGblCfg['mysqlPort'], database=dictGblCfg['mysqlDB'])

            curLog = dictGblCfg['mysqlConn']
            strLogSQL = "INSERT INTO db_genarticle.tb_hotsearch (word, create_time) VALUES (%s, now())"
            curLog.execute(strLogSQL, ('我开机了'))
            # 提交事务
            curLog.commit()
            curLog.close()
        except Exception as e:
            print(f'一、配置 方面错误，错误为 : {e}')



        # -----------------------------------------------------------------------
        # 二、获取微信 Access_Token
        try:
            dictGblCfg['offiAccToken'] = funGetOffiAccAccToken(
                dictGblCfg['offiAccAppid'], dictGblCfg['offiAppSecret'])

            # print(dictGblCfg['offiAccToken'])
        except Exception as e:
            print(f'二、获取微信 Access Token 方面错误，错误为 : {e}')



        # -----------------------------------------------------------------------
        # 三、Fetch the hot search data
        try:
            dictHotSearch = funFetchWeiboHotSearch()

            listHotSearch =dictHotSearch['data']['realtime']

            # print(strKeyword)
            listKeyword = funInsertWordsIfNotExists(dictGblCfg['mysqlConn'],listHotSearch)


            # dictGblCfg['hotSearchKeywordList'] = dictHotSearch['data']['realtime']

            # strKeyword = listKeyword[0]

            # print(listKeyword)
        except Exception as e:
            print(f'三、获取微信热搜 方面错误，错误为 : {e}')




        # -----------------------------------------------------------------------
        # 开始循环写文章

        listArticle=[]
        for strGenKeyword in listKeyword:

            # -----------------------------------------------------------------------
            # 四、搜相关图片
            try:
                listKeywordPic = funGetPicture(strGenKeyword)
            except Exception as e:
                print(f'四、搜个图片 方面错误，错误为 : {e}')

            # print(listKeywordPic)

            # -----------------------------------------------------------------------
            # 五、下载图片
            listImgList = []
            listImgMediaID = []
            try:
                for strAKeywordPic in listKeywordPic[:3]:
                    # print(strAKeywordPic)
                    strImgFile = funDownloadImage(strAKeywordPic)
                    # print(strImgFile)
                    listFBOffiImg=funUploadImgToOffiAcc(dictGblCfg['offiAccToken'], strImgFile)
                    listImgList.append(listFBOffiImg['url'])
                    listImgMediaID.append(listFBOffiImg['media_id'])
                # print("','".join(listImgList))
            except Exception as e:
                print(f'五、下载图片 方面错误，错误为 : {e}')


            # -----------------------------------------------------------------------
            # 六、写文章
            strTitle='默认标题'
            strArticle='默认内容'
            try:
                client = ZhipuAI(api_key=dictGblCfg['bigModelKey'])  # 填写您自己的APIKey
                response = client.chat.completions.create(
                    model="glm-4-flash",
                    temperature=1,
                    top_p=1,
                    messages=[
                        {"role": "system", "content": '''
你是一个文采风趣的作家，内容是结合时下热点输出文章，要求：
1.输出内容需要以 HTML 的格式（并只生成 <body> 之中的内容，但不需要包含 <body> 标签）
2.每篇文章需要在600字左右。需要在最后加入一行 3px 的白色字，内容是“文章由AI生成”，必须要是白色 1px 的！！！
3.不需要以 Markdown 形式展示，直接输出能运行的 HTML 代码。
'''},
                        {"role": "user", "content": "帮忙根据关键词 '"+strGenKeyword +
                            "' 生成文章，并在其中加入图片,图片 URL 如下:'"+"','".join(listImgList)+"'"},
                    ],
                )
                strArticle = response.choices[0].message.content
                # print(strArticle)

                if strArticle[:7] == "``html":
                    strArticle = strArticle[7:]
                    strArticle = strArticle[:-3]

                response = client.chat.completions.create(
                    model="glm-4-flash",
                    temperature=1,
                    top_p=1,
                    messages=[
                        {"role": "system", "content": "你是一个文采风趣的作家，内容是结合时下热点输出简单不超10字的文章标题，仅需输出标题，不需要输出其他东西"},
                        {"role": "user", "content": "帮忙根据内容 '"+strGenKeyword+"' 生成标题"},
                    ],
                )
                strTitle = response.choices[0].message.content
                # print(strTitle)


            except Exception as e:
                print(f'六、写文章 方面错误，错误为 : {e}')
            if listImgMediaID[0]==None:
                listImgMediaID[0] = '0'
            dictOneArticle={
                    "title": strTitle,
                    "author": '吴教授',
                    "content": strArticle,
                    "thumb_media_id": listImgMediaID[0],
                    "need_open_comment": 1,
                    "only_fans_can_comment": 0,
                }
            listArticle.append(dictOneArticle)


        # -----------------------------------------------------------------------
        # 七、上传文章
        try:
            dictPublishStatus=funPublishDocument(dictGblCfg['offiAccToken'],listArticle)
            # print(dictPublishStatus)
        except Exception as e:
            print(f'七、上传文章 方面错误，错误为 : {e}')



        # -----------------------------------------------------------------------
        # 八、发布文章
        try:
            dictSubmitStatus=funSubmitArticle(dictGblCfg['offiAccToken'],dictPublishStatus['media_id'])
            # print(dictSubmitStatus)

        except Exception as e:
            print(f'八、发布文章 方面错误，错误为 : {e}')


        try:
            dictGblCfg['mysqlConn'].close()
        except Exception as e:
            print(f'关闭数据库链接 方面错误，错误为 : {e}')

        await asyncio.sleep(1800)

time.sleep(120)
asyncio.run(funGenArticle())

```



## MySQL DDL

```SQL
-- 导出 db_genarticle 的数据库结构
CREATE DATABASE IF NOT EXISTS `db_genarticle` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `db_genarticle`;

-- 导出  表 db_genarticle.tb_hotsearch 结构
CREATE TABLE IF NOT EXISTS `tb_hotsearch` (
  `word` varchar(300) DEFAULT NULL,
  `create_time` datetime DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```