---
layout: post
title: "Python 网络爬虫一"
excerpt: "爬虫的简单使用"
date: 2017-07-23 11:33
tags: [Python]
---




[信息](http://www.python-requests.org/en/master/)


## 安装
> pip install  requests

## 测试
```python
import requests
r=requestsget("http://www.baidu.com")
r.status_code
r.encoding='utf-8'
r.text
```
## 7个主要方法

> requests.request() 	构造一个请求，支撑以下各方法的基础方法
> requests.get() 		获取HTML网页的主要方法，对应于HTTP的GET
> requests.head()		获取HTML网页头信息的方法，对应于HTTP的HEAD
> requests.post() 		向HTML网页提交POST请求的方法，对应于HTTP的POST
> requests.put() 		向HTML网页提交PUT请求的方法，对应于HTTP的PUT
> requests.patch()		向HTML网页提交局部修改请求，对应于HTTP的PATCH
> requests.delete() 	向HTML页面提交删除请求，对应于HTTP的DELETE

## Get

> requests.get(url,params=None,**kwargs) 返回Reponse对象
> url: url链接
> params: 中的额外参数
> **kwargs: 12个控制访问参数

12个参数
> params : 字典或字节序列，作为参数增加到url中
> data    : 字典、字节序列或文件对象，作为Request的内容
> json : JSON格式的数据，作为Request的内容
> headers : 字典，HTTP定制头
> cookies : 字典或CookieJar，Request中的cookie
> auth : 元组，支持HTTP认证功能
> files   : 字典类型，传输文件
> timeout : 设定超时时间，秒为单位
> proxies : 字典类型，设定访问代理服务器，可以增加登录认证
> allow_redirects : True/False，默认为True，重定向开关
> stream  : True/False，默认为True，获取内容立即下载开关
> verify  : True/False，默认为True，认证SSL证书开关
> cert    : 本地SSL证书路径



## Reponse

> r.status_code 	HTTP请求的返回状态，200表示连接成功，404表示失败
> r.text 			HTTP响应内容的字符串形式，即，url对应的页面内容
> r.encoding 		从HTTP header中猜测的响应内容编码方式
> r.apparent_encoding 	从内容中分析出的响应内容编码方式（备选编码方式）
> r.content 		HTTP响应内容的二进制形式


## 异常

> requests.ConnectionError 	网络连接错误异常，如DNS查询失败、拒绝连接等
> requests.HTTPError 			HTTP错误异常
> requests.URLRequired 		URL缺失异常
> requests.TooManyRedirects 超过最大重定向次数，产生重定向异常
> requests.ConnectTimeout 	连接远程服务器超时异常
> requests.Timeout 			请求URL超时，产生超时异常

## post

> payload = {'key1':'value','key2':'value2'}
> url='http://httpbin.org/post'
> r=requests.post(url,data=payload)
> print(r.text)
 
```json
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "key1": "value", 
    "key2": "value2"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Connection": "close", 
    "Content-Length": "22", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.18.1"
  }
```

写一个自动框架
```python
import requests as re

def getHTMLText(url):
    try:
        hd={'user-agent':'Chrome/10'}
        r = re.get('http://'+url,headers=hd,timeout=3)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        return "Wrong"

if __name__=="__main__":
    while(True):
        
        url = input("请输入地址:")
        text = getHTMLText(url)  
        print(text)
    
```

