---
title: windows环境下使用pigz进行压缩和解压缩
date: 2025-07-25 13:55:10
tags: [笔记,运维,部署,pigz,tar,数据迁移]
categories: [软件技术]
---

# windows环境下使用pigz进行压缩和解压缩

> ​	pigz，全称 Parallel Implementation of GZip，是 gzip 的多线程替代工具，由 zlib 作者之一 Mark Adler 开发。它通过将数据分块并用多个 CPU 核心并行压缩，大幅提高大文件或大量文件的压缩与解压速度，适合 Linux、macOS 和 WSL 等环境。pigz 只支持 gzip 格式，通常与 tar 搭配使用生成 .tar.gz 压缩包。相比 gzip，pigz 的压缩率基本相同但速度更快，缺点是只能用于 gzip 格式，没有图形界面，更适合服务器或需要高性能压缩的场景。
>
> ​	在跨平台迁移数据的场景中，会遇到文件很大（几百g），并且还从Linux跨平台到Windows，例如数据库文件。这个时候数据的压缩和解压缩对于整个过程消耗的时间有巨大影响。
>
> ​	场景：windows下有一个data.tar.gz大文件，使用pigz的多线程解压到tar，然后再使用tar或者7zip解压出最后结果。

# 安装pigz

> 可以用wsl安装或者cygwin安装。这里仅介绍cygwin。

## 安装cygwin

> Cygwin 是一个在 Windows 上提供类似 Unix 环境的工具集和运行时库，由 Red Hat 维护开发。它通过实现一个兼容层，将大部分 GNU 和开源工具移植到 Windows，使用户能够在 Windows 中使用熟悉的 Bash shell、GNU 工具链和常用命令行程序。Cygwin 适合需要在 Windows 上执行脚本、编译 Linux 程序或进行类 Unix 开发的用户，优点是工具丰富、兼容性好；缺点是相较于原生 Linux 性能略低，占用空间较大，不适合作为正式生产环境，仅适合开发和学习使用。

> Cygwin官网以及下载链接：[Cygwin](https://www.cygwin.com/)

### 1. 下载`setup-x86_64.exe`

> 这个文件可以不用删除，后续增加别的功能也可以用到。

![image-20250725141440951](../images/linux/运维/pigz/image-20250725141440951.png)

### 2. 打开`setup-x86_64.exe`，点击下一页，选择 **从互联网安装**

​	![image-20250725141603845](../images/linux/运维/pigz/image-20250725141603845.png)

### 3. 选择目录

> 这里需要准备好两个目录，一个用于放cygwin本体，以用于放cygwin的包，比如我们待会要安装的pigz

![image-20250725141755149](../images/linux/运维/pigz/image-20250725141755149.png)

![image-20250725141812822](../images/linux/运维/pigz/image-20250725141812822.png)

### 4. 选择连接方式 -直接连接

![image-20250725141938929](../images/linux/运维/pigz/image-20250725141938929.png)

### 5. 选择下载站点-这里可以选择腾讯的站点。

![image-20250725142059073](../images/linux/运维/pigz/image-20250725142059073.png)

### 6. 此时会弹出一个下载界面，里面有很多Linux工具

进入下一步安装pigz

## 安装pigz

### 1. 搜索pigz

![image-20250725142340074](../images/linux/运维/pigz/image-20250725142340074.png)

### 2. 选择版本，然后下载

![image-20250725142422049](../images/linux/运维/pigz/image-20250725142422049.png)

### 3. 等待下载即可

![image-20250725142501210](../images/linux/运维/pigz/image-20250725142501210.png)

### 4. 下载完成后打开

界面：

![image-20250725142706463](../images/linux/运维/pigz/image-20250725142706463.png)

## 使用pigz

### 压缩

* 切换到对应的目录

> 切换方式，cd /cygdrive/ + 文件所在目录

```bash
cd /cygdrive/g/pigzTest/
```

![image-20250725143153095](../images/linux/运维/pigz/image-20250725143153095.png)

* 开始压缩

> 默认 pigz 会使用所有可用核心，也可以用 `-p N` 指定线程数

```bash
tar cf - ssh-images | pigz > ssh-images.tar.gz
```

![image-20250725143502286](../images/linux/运维/pigz/image-20250725143502286.png)

* 指定线程数压缩

```
tar cf - ssh-images | pigz -p 4 > ssh-images.tar.gz
```

![image-20250725143632683](../images/linux/运维/pigz/image-20250725143632683.png)

* 也可以超线程压缩，需要cpu支持

> 超线程性能不一定好。

```bash
tar cf - ssh-images | pigz -p 16 > ssh-images-16-thread.tar.gz
tar cf - ssh-images | pigz -p 32 > ssh-images-32-thread.tar.gz
tar cf - ssh-images | pigz -p 64 > ssh-images-64-thread.tar.gz
tar cf - ssh-images | pigz -p 128 > ssh-images-128-thread.tar.gz
```

![image-20250725144208927](../images/linux/运维/pigz/image-20250725144208927.png)

### 解压

#### 解压到tar

* 解压到tar，并保留源文件

```bash
pigz -dc ssh-images.tar.gz > pigzTar1.tar
```

![image-20250725144623659](../images/linux/运维/pigz/image-20250725144623659.png)

* 指定线程解压

```bash
pigz -d -p 4 -c ssh-images.tar.gz > pigzTarThread4.tar
```

![image-20250725144814483](../images/linux/运维/pigz/image-20250725144814483.png)

* 超线程解压

```bash
 pigz -d -p 32 -c ssh-images.tar.gz > pigzTarThread32.tar
```



![image-20250725144931193](../images/linux/运维/pigz/image-20250725144931193.png)

#### 解压出原始文件

* 默认模式解压

```bash
pigz -dc yourfile.tar.gz | tar xf -
```

![image-20250725150026148](../images/linux/运维/pigz/image-20250725150026148.png)

* 指定线程数解压

```
pigz -dc -p 8 ssh-images-out.tar.gz | tar xf -
```

![image-20250725150208510](../images/linux/运维/pigz/image-20250725150208510.png)
