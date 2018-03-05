---
title:  "一个适用于我校的简单爬虫"
excerpt: "咳咳咳"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Tool
tags:
  - 爬虫
---
这东西理论上能爬到07年以后全校的基本信息，我没那么无聊，爬了四五组就停了。

昨天现学了10分钟，被某页面的简陋度震惊了……删了些关键部分，补上就能用，应该很容易找到填入的内容。

```python
import requests
import urllib

def geturl(i):
    url = '咳咳咳'+str(i)
    header = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 UBrowser/6.1.2107.204 Safari/537.36'}
    html = requests.get(url,headers = header)
    html.encoding='utf-8'
    urllib.urlretrieve(url,'%s.htm'%i)
    pic='咳咳咳'
    urllib.urlretrieve(pic,'%s.jpg'%i)
    # print(html.text)

#for i in range(学号a,学号b):
    #geturl(i)
    #为所欲为
```