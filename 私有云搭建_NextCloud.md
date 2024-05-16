---
title: 一行代码搭建私有云（基于docker NextCloud） 
date: 2024-05-15 14:43:31
tags: [私有云,Linux,docker,NextCloud,云盘,跨平台,github项目,笔记]
---


# 一行代码搭建私有云（基于docker NextCloud） 
## 简介
​	老早以前就很想有一朵属于自己的云了，机缘巧合发现了**NextCloud**	这个项目，使用Docker一键部署简直不要太简单！    

​	项目地址：	[NextCloud repo on github](https://github.com/nextcloud/docker)
​	搭完以后再也没有用过文件传输助手，多平台文件传输秒传。太厉害了。
​	开源不易，感谢大佬

## 一行代码

````bash
docker run -d -p 8080:80 nextcloud
````

然后直接`IP:8080`就访问到了

![1](/images/nc/1.png)

![2](/images/nc/2.png)
