---
layout: post
title:  肆拾陆- Tableau Dashboard Extension 的绝望之旅 (一)
date:   2020-07-22 00:19:28 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

# **一。什么是 Tableau 扩展？**

其实如果不知道什么是 `Tableau 扩展`应该也不会搜到这里来了，但循例我也应该介绍一下。

原文如下：

[What is a Tableau Dashboard Extension](https://tableau.github.io/extensions-api/docs/trex_overview.html)

`Tableau 扩展` 本质来说其实在 `Dashboard` 里面插入一个`网页`，然后该网页可以通过 `JS` 文件与 `Tableau` 上的`内容`进行交互。

交互的方式可以是改变 `Dashboard` 中`嵌套` `Worksheet` 的`筛选器`，修改`参数`等。

# **二。这个扩展与浏览器的扩展有啥不同？**

以 `Chrome 扩展`为例， `Chrome 扩展`是一个后缀为 `crx` 的文件，而 `Tableau 扩展`是后缀为 `trex` 的文件。

以下为主要对比：

 -  | Chrome 扩展 | Tableau 扩展
--------| ------------| -------------
插件文件后缀 | crx | trex
插件文件内容 | 包含 配置文件、Html、JS 文件的完整页面内容 | 只是一个配置文件
宿主| Chrome 浏览器 | Tableau Desktop、Tableau Server
扩展API | 多且自由 ( 如增加右键菜单 ) | 少且很多限制 |

( 其实我想指出真正有意义只有第二点 )

# **三。Tableau 扩展文件内容**

`Tableau 扩展文件`是`不包含`任何内容的，只是一个`描述文件`，`内容`可以参考：

[Tableau Extension Manifest File](https://tableau.github.io/extensions-api/docs/trex_manifest.html)


例子可以参考：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest manifest-version="0.1"
  xmlns="http://www.tableau.com/xml/extension_manifest">
  <dashboard-extension id="top.berrybc" extension-version="0.1.0">
    <default-locale>en_US</default-locale>
    <name resource-id="name"/>
    <description>Show Table</description>
    <author name="Berry Cui" email="berry_BC_@163.com" organization="Berry" website="https://berrybc.top"/>
    <min-api-version>0.1</min-api-version>
    <source-location>
      <url>http://localhost/static/tableau/showtable/index.html?Love=Berry</url>
    </source-location>
    <icon>iVBORw0KGgoAAAANSUhEUgAAAEYAAABGCAYAAABxLuKEAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAABuBSUR....</icon>
    <permissions>
      <permission>full data</permission>
    </permissions>
    <context-menu>
      <configure-context-menu-item />
    </context-menu>
  </dashboard-extension>
  <resources>
    <resource id="name">
      <text locale="en_US">Show Table</text>
    </resource>
  </resources>
</manifest>
```

这里必须说明有三点是十分重要的

#### 1.\<url\>
>Specifies the scheme (HTTPS, HTTTP), the name of the server, the port (optional) and the path to extension (optional). The url must use HTTPS. For example: https://example.com/extension. The exception is for localhost. In this case, HTTP is allowed. For example: http://localhost:8080.

意思是，除非你在`本地测试` ( 必须为 localhost ,127.0.0.1 可不行 )，不然`扩展`的引用地址必须是 `https` ( 保证不被中间替换的安全措施哈~ )。

#### 2.\<icon\>
>If specified, the icon is what appears in the About dialog box. The icon must be a 70x70 pixel PNG file that is Base64 encoded. If you need an encoder, see https://www.base64-image.de/
>

意思是，其实这个 `Icon` 真的没啥意义，而且，想要用的话你还必须上那个网站把`图片`转化为`字符串`，再直接写在这里。

#### 3.\<permissions\>
>Declares the types of permissions that this extension requires. The only option is full data. If your extension can access the underlying data or information about the data sources, you must declare full data permission in the manifest. Full data permissions are required if you use any one of the following APIs: Worksheet.getUnderlyingDataAsync(), Datasource.getUnderlyingDataAsync(), Datasource.getActiveTablesAsync(), Datasource.getConnectionSummariesAsync(). If your extension does not use one of these APIs, you do not need include permissions element.

就是说这个设置有且只有一个`配置值`，就是 `'full data'` ，配置了这个值之后，你会发现这个`扩展`在 `Tableau Server` 上会被卡得很死 ( 需要配置`白名单` )，超烦，而且`配置`只是为了获得`底层数据` ( 直接访问数据源 ) ，我就觉得你直接访问`数据源`那就干脆写个`网站`算了呀，干嘛还加多一层 `Tableau` ？

#### 4.\<context-menu\>

`Tableau 扩展权限`其实控制得很死，这是没办法的事情，选择菜单也只有这一项，详细可以看：

[Add a Configuration Popup Dialog](https://tableau.github.io/extensions-api/docs/trex_configure.html)

( 其实我想说这一点不重要 )


# **四。Tableau 扩展的限制**

#### 1、 插件虽然是 Html + JS ，但...

首先需要明确一点，插件针对 `Tableau Desktop` 其实意义并不大，因为我们要实现复杂的功能的话，我们何不求助`其余工具`？

所以只是针对 `Tableau Server` 上展示的 `Dashboard` 进行开发才是重点。

但问题来了，我们看过 `Tableau Server` 上的 `Dashboard` `HTML` 的话，会发现数据展示基本都是一个个 `Canvas` 画出来的，我们会以为如果用上插件之后，就真的会像一个网页一样。

但错了！我们用 `EDGE 跟 IE` 浏览器时，会发现里面的内容并不好像网页一样是可以选取的，只有用 `Chrome` 时才可以选取！


#### 2、 Tableau 原生下载数据按钮并不对扩展生效

在 `Tableau Server` 上，我们有时候会选择` "下载数据"` 按钮来下载当前页面的 `截图/PPT/PDF` ，但针对插件，你是无法使用那个插件下载当前展示的内容。

就是说，`原生`的 `Tableau` `内容`可以完全下载`当前页面` `截图/PPT/PDF` ，但对 `Tableau 扩展` 是`不生效`的 ( 扩展中数据只会显示空白 )。


#### 3、 扩展限制大

说白了，`Tableau` 就是一个`比较封闭`的数据展示系统，功能的确算是强大，可在中国这种前端工人遍地皆是，便宜且好用，花买 `Tableau License` 同样的钱我能请一堆好用的`前端`来开发`可视化报表`了。

# **五。后续我会从头到尾做一个 Tableau 扩展**

好了，我睡了。

后续的东西后续算。