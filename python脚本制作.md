---
title: python脚本制作
date: 2023-10-04 13:25:57
tags: [笔记, python]
---

​	我突然打算不用知识点记录的方式记录我的学习笔记，打算以发现问题解决问题的方法记录学习过程。

# 1.记录的第一个脚本。

## 	1.问题导入

​	过去我常常把我拍的照片导入桌面，p完图之后再粘贴到我的移动硬盘留底，过程虽然不长，但是我就是懒。

## 	2.分析

​	我的桌面一般没有东西，所以我可以直接将整个桌面文件夹复制到我的目标目录，或者在匹配后缀位jpg或者png的文件转移到目标文件夹。

![desktop](/images/desktop.png)

## 3.实现过程

	1. 转移工作目录到桌面
	1. 在目标文件夹创建文件夹
	1. 复制文件到该文件夹

```python
import os

import datetime
import shutil

fileName="null"

os.chdir('C:/Users/Regen/Desktop')#转到desktop

desktopPath=os.getcwd()#记录桌面路径


fileName=datetime.datetime.now().strftime("%Y-%m-%d")#以当前时间为文件夹名字

os.chdir('E:/代存外部硬盘')
os.mkdir(fileName)#创建文件夹

fileDir=os.getcwd()
fileDir=fileDir+"\\"+fileName


src = desktopPath
des = fileDir


for file in os.listdir(src):
    #遍历原文件夹中的文件
    full_file_name = os.path.join(src, file)#把文件的完整路径得到
    print("要被复制的全文件路径全名:",full_file_name)
    if os.path.isfile(full_file_name):#用于判断某一对象(需提供绝对路径)是否为文件
        shutil.copy(full_file_name, des)#shutil.copy函数放入原文件的路径文件全名  然后放入目标文件夹


```

## 4.遇到的问题

	1. 一开始想要直接复制整个文件夹，但是会遇到权限问题，windows的权限问题没有Linux好解决，进而转向遍历文件夹
	1. 发现其实可以用windows自带的脚本语言，CMD实现（相当于Linux的shell脚本）

## 5.收尾

​	很简单的脚本还写了两个小时。不过过程中又学会了一点东西。可以，好玩！

​	![result](/images/wenjianzhuanyi.PNG)

