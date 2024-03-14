---
layout: post
title:  拾伍-上网抄录的 Markdown CSS
date:   2019.03.21 22:10:43 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---




```css

/* Please visit the URL below for more information: */
/*   https://shd101wyy.github.io/markdown-preview-enhanced/#/customize-css */

.markdown-preview.markdown-preview {
  // modify your style here
  // eg: background-color: blue;
  font-family: "Calibri", "微软雅黑";

  body {

    font-weight: 400;
    font-size: 16px;
    word-spacing: 1px;
    color: #666;
    margin: 0;
  }
  h1, h2, h3, h4, h5, h6 {
    font-weight: 400;
    color: #444;
  }
  h1 {
    font-size: 32px;
    margin: 0 0 45px;
  }
  h2 {
    position: relative;
    font-size: 24px;
    padding-left: 15px;
  }
  h3 {
    font-size: 1.17em;
    color: rgb(54, 14, 80);
    font-weight: bold;
    border-bottom: 1px solid #ddd;
    padding-bottom: 5px;
  }
  h4 {
    margin: 15px 0;
  }
  h2:before {
    content: '';
    width: 5px;
    background: rgb(194, 102, 255);
    position: absolute;
    left: 0px;
    height: 75%;
    top: 12%;
  }
  blockquote {
    margin: 2em 0;
    padding-left: 30px;
    border-left: 10px solid #e6e6e6;
  }
  p {
    line-height: 1.8em;
  }
  li {
    line-height: 1.8em;
  }
  a {
    text-decoration: none;
    color: rgb(194, 102, 255);
    border-bottom: 2px solid transparent;
  }
  a:hover {
    color: rgb(124, 53, 172);
    border-bottom-color: rgb(124, 53, 172);
  }

  code {
    padding: 2px 4px;
    background-color: #f6f6f6;
    border: none;
    font-size: 13px;
    white-space: pre-wrap;
    vertical-align: middle;
    color: #7b3fc9;
    border-radius: 4px;
  }
}
```