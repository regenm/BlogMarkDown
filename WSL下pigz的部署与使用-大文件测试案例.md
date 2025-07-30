---
title: WSL下pigz的部署与使用-大文件测试案例
date: 2025-07-30 11:19:20
tags: [笔记,运维,部署,pigz,tar,数据迁移]
categories: [软件技术]
---

# WSL使用pigz进行压缩和解压缩

> ​	**WSL**（Windows Subsystem for Linux）是微软为 Windows 提供的一个兼容层，允许用户在 Windows 上原生运行 Linux 环境，而无需安装虚拟机或双系统。它支持大部分常用的 Linux 命令行工具、应用和开发框架，方便开发者在同一台电脑上同时使用 Windows 与 Linux 系统资源，尤其适合进行跨平台开发、测试和日常运维等工作，让 Windows 用户能够轻松体验和使用 Linux 的强大生态。	
>
> ​	**pigz**，全称 Parallel Implementation of GZip，是 gzip 的多线程替代工具，由 zlib 作者之一 Mark Adler 开发。它通过将数据分块并用多个 CPU 核心并行压缩，大幅提高大文件或大量文件的压缩与解压速度，适合 Linux、macOS 和 WSL 等环境。pigz 只支持 gzip 格式，通常与 tar 搭配使用生成 .tar.gz 压缩包。相比 gzip，pigz 的压缩率基本相同但速度更快，缺点是只能用于 gzip 格式，没有图形界面，更适合服务器或需要高性能压缩的场景。
>
> ​	在跨平台迁移数据的场景中，会遇到文件很大（几百g），并且还从Linux跨平台到Windows，例如数据库文件。这个时候数据的压缩和解压缩对于整个过程消耗的时间有巨大影响。
>
> ​	场景：windows下有一个13.tar.gz大文件（15g图片），使用pigz的多线程解压到tar，然后再使用tar或者7zip解压出最后结果。

# 安装pigz

## 安装WSL

> winserver版本必须在2022以上。
>
> WSL系统默认自带，但是由于版本等问题需要自行设置或者升级。

### 查看WSL版本

```
wsl --status
```

![image-20250730112938722](../images/linux/运维/pigz/image-20250730112938722.png)

这里已经安装好了所以显示了版本2和ubuntu

如果没有安装好则需要去下载wsl最新稳定版release进行安装，下载链接：[Releases · microsoft/WSL](https://github.com/microsoft/WSL/releases)

### 安装WSL

![image-20250730113425196](../images/linux/运维/pigz/image-20250730113425196.png)

双击安装即可

## 安装子系统

> 这里采用下载安装的方法便于管理。
>
> 也可以通过`wsl --install -d Ubuntu`直接从微软安装，安装路径在C盘。

### 下载解压appx文件

下载链接：[Manual installation steps for older versions of WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/install-manual#downloading-distributions)

放到想要安装的路径`D:\wsl`并且解压

![image-20250730132350039](../images/linux/运维/pigz/image-20250730132350039.png)

### 打开wsl

解压后进去打开应用程序即可开启wsl，之后会提示设置用户名密码。

![image-20250730132439756](../images/linux/运维/pigz/image-20250730132439756.png)

## 安装pigz

```bash
sudo apt update
sudo apt install pigz
```

![image-20250730132805942](../images/linux/运维/pigz/image-20250730132805942.png)

# 测试pigz并总结

目标文件：13.tar.gz

> pigz仅能负责gz部分的解压，剩余部分需要使用tar或者7zip进行解tar，后文还测试了多进程解tar、拆tar的过程。
>
> 需要注意的是，13.tar.gz全部是图片文件，而图片文件本身就是压缩过的文件，所以从tar.gz到源文件的过程总性能瓶颈在于解tar。后文的时间记录结果页印证了这一点。

## pigz部分测试

测试结果：13.tar.gz   ----> 13.tar

以下为shell解压脚本：

```shell
#!/bin/bash

# 要解压的文件
INPUT_FILE="../../13.tar.gz"
# 输出的 tar 文件名
OUTPUT_FILE="13.tar"

echo "[*] 开始使用 pigz 解压：$INPUT_FILE -> $OUTPUT_FILE"
echo "[*] 使用 8 核"

# 测量用时
START=$(date +%s)

# 使用 pigz 解压到 tar
pigz -d -p 8 -c "$INPUT_FILE" > "$OUTPUT_FILE"

END=$(date +%s)
DURATION=$((END - START))

echo "[*] 解压完成，用时: ${DURATION} 秒"
```

### 测试结果：

用时： 802 秒 13mins

![image-20250730133904246](../images/linux/运维/pigz/image-20250730133904246.png)

看任务管理器可以发现8核确实在使用：

![image-20250730134129445](../images/linux/运维/pigz/image-20250730134129445.png)

## tar部分测试

### tar单进程解压

shell脚本如下：将pigz的结果直接给tar进行单进程解压

```shell
#!/bin/bash

# 设定文件夹路径（你可以根据自己的需要修改）
DIR="/mnt/d/share"              # 目标文件夹路径
FOLDER="13"                     # 要压缩的文件夹名（在 $DIR 下）
FILE="$DIR/$FOLDER"             # 要压缩的文件夹路径
OUTPUT_COMPRESSED="$DIR/$FOLDER.tar.gz"  # 压缩后的文件路径

# 压缩函数
function compress {
    echo "[*] 开始压缩：$FILE"
    # 使用 tar 打包并通过 pigz 压缩，-p 8 表示使用 8 个核心
    time tar -cf - "$FILE" | pigz -p 8 > "$OUTPUT_COMPRESSED"
}

# 检查文件夹是否存在
if [ ! -d "$FILE" ]; then
    echo "[*] 错误：文件夹 $FILE 不存在！"
    exit 1
fi

# 运行压缩函数
time compress

echo "[+] 压缩完成！结果保存在：$OUTPUT_COMPRESSED"

```

#### 用时 389mins



### tar8进程解压

#### 全过程脚本：

1. 拆tar
    1. 导出文件列表目录
    2. 均分为8个文件目录
    3. 拆分为8个tar
2. 8进程解tar

```shell
#!/bin/bash

# ================================
# 脚本: 8coreUntar.sh
# 用途: 把大 tar 拆分成8小 tar 并多进程解压
# ================================

BIG_TAR="13.tar"
EXTRACT_DIR="./extract_dir"
PARTS=8

mkdir -p "$EXTRACT_DIR"
###########################################################################
echo "[*] Step 1: 列出大 tar 文件里的所有文件..."
start_list=$(date +%s)
tar -tf "$BIG_TAR" > filelist_all.txt
end_list=$(date +%s)
echo "[✔] 列出文件耗时：$((end_list - start_list)) 秒"
###########################################################################
echo "[*] Step 2: 把文件列表平均拆成 $PARTS 份..."
start_split=$(date +%s)
split -n l/$PARTS filelist_all.txt filelist_
end_split=$(date +%s)
echo "[✔] 拆分文件耗时：$((end_split - start_split)) 秒"
###########################################################################
echo "[*] Step 3: 根据拆分的列表，打包成小 tar..."
start_pack=$(date +%s)
for list in filelist_*; do
    [ "$list" = "filelist_all.txt" ] && continue
   # [ "$list" = "filelist_aa" ] && continue
    echo "  -> 打包 $list ..."
    sleep 3
    # 关键修改：使用 -C 参数指定根目录为当前目录
    tar -cvf "${list}.tar" -C "/" -T "$list"
done
end_pack=$(date +%s)
echo "[✔] 打包小 tar 总耗时：$((end_pack - start_pack)) 秒"
###########################################################################
echo "[*] Step 4: 解压8小 tar 文件..."
start_extract=$(date +%s)
for t in filelist_*.tar; do
    (echo "解压 $t ..."; tar -xvf "$t" -C "$EXTRACT_DIR") &
done
wait
end_extract=$(date +%s)
echo "[✔] 单个解压总耗时：$((end_extract - start_extract)) 秒"
###########################################################################
```

##### 用时：总共201.5

13(pigz8线程解压)+15（导出文件列表）+64（拆分8个tar）+109.5（8进程解tar）=201.5 mins

#### 拆tar过程

shell对应的代码部分：

```shell
###########################################################################
echo "[*] Step 1: 列出大 tar 文件里的所有文件..."
start_list=$(date +%s)
tar -tvf "$BIG_TAR" > filelist_all.txt
end_list=$(date +%s)
echo "[✔] 列出文件耗时：$((end_list - start_list)) 秒"
###########################################################################
echo "[*] Step 2: 把文件列表平均拆成 $PARTS 份..."
start_split=$(date +%s)
split -n l/$PARTS filelist_all.txt filelist_
end_split=$(date +%s)
echo "[✔] 拆分文件耗时：$((end_split - start_split)) 秒"
###########################################################################
echo "[*] Step 3: 根据拆分的列表，打包成小 tar..."
start_pack=$(date +%s)
for list in filelist_*; do
    [ "$list" = "filelist_all.txt" ] && continue
   # [ "$list" = "filelist_aa" ] && continue
    echo "  -> 打包 $list ..."
    sleep 3
    # 关键修改：使用 -C 参数指定根目录为当前目录
    tar -cvf "${list}.tar" -C "/" -T "$list"
done
end_pack=$(date +%s)
echo "[✔] 打包小 tar 总耗时：$((end_pack - start_pack)) 秒"
###########################################################################
```

用时 79mins

#### 多进程解tar过程

shell对应的代码部分：

```shell
###########################################################################
echo "[*] Step 4: 解压8小 tar 文件..."
start_extract=$(date +%s)
for t in filelist_*.tar; do
    (echo "解压 $t ..."; tar -xvf "$t" -C "$EXTRACT_DIR") &
done
wait
end_extract=$(date +%s)
echo "[✔] 单个解压总耗时：$((end_extract - start_extract)) 秒"
###########################################################################
```

用时109.5mins



# 问题总结：

## 拆tar过程会重新打包一遍整个tar

​	拆tar过程中生成的file_lists文件需要删除第一行，因为第一行是文件夹的根，使用命令`tar -cvf "${list}.tar" -C "/" -T "$list"会重新打包一遍根
