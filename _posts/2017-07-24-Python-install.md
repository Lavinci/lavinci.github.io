---
layout: post
title: "Intstller"
excerpt: "memo"
date: 2017-07-24 18:52
tags: [Python]

---

一、电脑需安装32位的python3.6(注意是32位的Python而不是操作系统是32位，安装64的python会发现pyinstall无法提供服务)

二、安装python之后安装py利用的模块，我的程序时利用了openpyxl模块，直接cmd模式到python路径下C:\Python36\Scripts，C:\Python36\Scripts>pip install openpyxl

三、安装pyinstall,C:\Python36\Scripts>pip install pyinstaller,提示成功之后到目录C:\Python36\Lib\site-packages下删掉PyInstaller文件夹，到https://github.com/pyinstaller/pyinstaller下载ZIP安装包，解压之后将PyInstaller文件夹复制到C:\Python36\Lib\site-packages目录下。因为pyinstall暂时不支持python3.6

四、到目前为止已经安装成功，可以生成exe了。C:\python3\Scripts>pyinstaller -F mytest.py，然后到目录C:\Python36\Scripts\dist下就可以看到mytest.exe了。
