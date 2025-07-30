---
title: docker容器技术
date: 2024-02-09 03:04:22
tags: [docker, 笔记, linux]
---

constructing

toBeContinued

# 简介

> ​				docker是一个用Go语言实现的开源项目，可以让我们方便的创建和使用容器，docker将程序以及程序所有的依赖都打包到docker container，这样你的程序可以在任何环境都会有一致的表现，这里程序运行的依赖也就是容器就好比集装箱，容器所处的操作系统环境就好比货船或港口，**程序的表现只和集装箱有关系(容器)，和集装箱放在哪个货船或者哪个港口(操作系统)没有关系**。
>
> ​				因此我们可以看到docker可以屏蔽环境差异，也就是说，只要你的程序打包到了docker中，那么无论运行在什么环境下程序的行为都是一致的，程序员再也无法施展表演才华了，**不会再有“在我的环境上可以运行”**，真正实现“build once, run everywhere”。

# 概念

​	docker 中的几个概念：

> 1. dockerfile
> 2. image
> 3. container

## dockerfile

​	`Dockerfile` 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。感觉就像`makefile`一样。

## image

​	Docker镜像（Docker Image）就是一个只读的模板。比如，一个镜像可以包含一个完整的[Ubuntu](https://so.csdn.net/so/search?q=Ubuntu&spm=1001.2101.3001.7020)操作系统环境。镜像可以用来创建Docker容器。

## container

​	容器是镜像的运行时实例，容器是独立运行的一个或一组应用，以及它们的运行态环境。

​	
