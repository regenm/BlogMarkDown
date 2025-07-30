---
title: Linux Notes
date: 2023-11-28 19:49:18
tags: [笔记, linux, system,command,运维]
categories: [软件技术]
---



A note about linux basic operation.



### 常用命令

1. 文件查看类

    - ```
        more
        ```

        ：

        - 空白键 (space)：代表向下翻一页；
        - Enter ：代表向下翻『一行』；
        - / 字串 ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；
        - :f ：立刻显示出档名以及目前显示的行数；
        - q ：代表立刻离开 more ，不再显示该文件内容；
        - b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。

    - ```
        less
        ```

        ：

        - 空白键 ：向下翻动一页；
        - [pagedown]：向下翻动一页；
        - [pageup] ：向上翻动一页；
        - **/ 字串 ：向下搜寻『字串』的功能；**
        - **? 字串 ：向上搜寻『字串』的功能；**
        - **n ：重复前一个搜寻 (与 / 或？有关！)**
        - **N ：反向的重复前一个搜寻 (与 / 或？有关！)**
        - q ：离开 less 这个程序；

    - `head`：读取前几行文本，如 `head -n 20 text.txt`

    - `tail`：与 `head` 相反，如 `tail -n 20 text.txt`

2. 文件压缩解压缩类

    - ```
        gzip
        ```

        ：

        - 压缩：`gzip linuxFiles.tar`
        - 解压缩：`gunzip linuxFiles.tar.gz` 或 `gzip -d linuxFiles.tar.gz`

    - ```
        tar
        ```

        ：

        - 打包文件：`tar -cvf linuxFiles.tar home/`

        - 解开打包后的文件：`tar -xvf linuxFiles.tar`

        - `tar -w` 选项，每次将选择单个文件抽出或加入，如 `tar -xvwf linuxFiles.tar`

        - ```
            tar -z
            ```

             

            自动调用

             

            ```
            gzip
            ```

             

            进行解压缩：

            - 压缩：`tar -czvf linuxFiles.tar.gz home/` （等价于 `tar -cvf linuxFiles.tar home/` 和 `gzip linuxFiles.tar` ）
            - 解压缩：`tar -xzvf linuxFiles.tar.gz home/` （等价于 `gzip -d linuxFiles.tar.gz` 和 `tar -xvf linuxFiles.tar` ）

3. 系统信息查看类

    - 查看内存占用和 swap 分区占用：`free -h`
    - 查看 CPU 使用情况：`top`
    - 查看设备挂载情况：
        - 所有设备：`lsblk` 、`lsblk -f`
        - 查看硬盘使用情况：`df -h`

4. 端口查看类

    - 查看某个端口（使用管道 + 过滤）：

        - 一般情况下：`netstat -anp | grep 8080` （`-a(all)n(numeric)p(programs)` ）

        - ```
            netstat
            ```

             

            的其他参数：

            - `-a (all)` ：显示所有选项，默认不显示 LISTEN 相关；
            - `-t (tcp)` ：仅显示 tcp 相关选项；
            - `-u (udp)` ：仅显示 udp 相关选项；
            - `-n` ：拒绝显示别名，能显示数字的全部转化成数字；
            - `-l` ：仅列出有在 Listen (监听) 的服务状态；
            - `-p` ：显示建立相关链接的程序名；
            - `-r` ：显示路由信息，路由表；
            - `-e` ：显示扩展信息，例如 uid 等；
            - `-s` ：按各个协议进行统计；
            - `-c` ：每隔一个固定时间，执行该 netstat 命令；

5. 任务调度类

    - ```
        crontab
        ```

        ：

        - 参数选项：

            - `-e` ：编辑 / 设置 / 修改当前用户的定时任务；
            - `-l` ：列出 / 查看 / 打印 / 输出当前用户的全部定时任务；
            - `-r` ：移除 / 删除当前用户的全部定时任务；

        - 语法中

             

            ```
            * * * * * 
            ```

            参数的解释：

            - `*` ：如 `1 2 * * *` 表示每天的 2:01 执行一次；
            - `*/n` ：如 `*/5 * * * *` 表示每隔 5 分钟执行一次；
            - `,(和)` ：如 `1 2 3,4 * *` 表示每月的 3,4 日 2:01 执行一次；
            - `-（范围）` ：如 `1 2 3-6 * *` 表示每月的 3-6 日 2:01 执行一次；

        - 使用方法：

            - 进入编辑模式：`crontab -e`
            - 编写任务后保存退出。

### 文件权限与管理



1. 文件系统相关

    - ```
        ext4
        ```

         

        文件系统介绍：

        - 与 `ext3` 相比，`ext4` 文件系统可支持最高 1EB 的分区与最大 16TB 的文件；
        - 拓展了子目录的数量，理论上可以无限个；
        - 与 `ext3` 相比，引入了块组的概念，提高了存取的效率；
        - 预留空间、延迟获取空间，减少了文件的分散；
        - 更详细的 `inodes`，提高了系统的性能；
        - 可以实现快速的文件系统检查；
        - 提供日志校验和，提高了可靠性；

2. 新硬盘的分区以及挂载流程

    - 查看是否已经存在设备：`lsblk` 、`lsblk -f` 或者查看 `/dev/` 下是否存在设备；
    - 使用 `fdisk` 进行分区设置：`fdisk /dev/sdb`
    - 分区格式化：`mkfs -t ext4 /dev/sdb1`
    - 挂载硬盘到文件夹：`mount /dev/sdb1 /newDisk`
    - 取消挂载：`unmount /dev/sdb1`

3. 文件在已有服务器之间的传输

    * 通过scp或者rsync

        1. scp

            > **传过去**
            >
            > `scp local_file.txt user@remote_host:/path/to/destination/`
            >
            > `scp -r local_folder user@remote_host:/path/to/destination/`
            >
            > **传过来**
            >
            > `scp user@remote_host:/path/to/remote_file local_destination/`
            >
            > 

        2. rsync

            > **传过去**
            >
            > `rsync -avz local_file user@remote_host:/path/to/destination/`
            >
            > `rsync -avz local_folder/ user@remote_host:/path/to/destination/`
            >
            > **传过来**
            >
            > ` rsync -avz user@remote_host:/path/to/remote_file local_destination/`

            * **`scp`**: Simple and straightforward, good for one-time transfers.
            * **`rsync`**: More efficient for syncing directories and updating files.

### 系统服务与进程管理

1. 进程相关

    - 提供所有进程信息：`ps aux`
    - 提供父进程信息：`ps lax`
    - 终止某项进程：`kill pid` （`kill` 只是向程序发送一个信号）

2. 系统服务（防火墙）相关

    - 查看防火墙状态：

        - `systemctl status firewalld`
        - `sudo ufw status`

    - 开启防火墙：

        - `systemctl start firewalld` 、`systemctl enable firewalld`
        - `sudo ufw enable`

    - 关闭防火墙：

        - `systemctl stop firewalld`
        - `sudo ufw disable`

    - 端口操作（以

         

        ```
        firewalld
        ```

         

        为例）：

        - 开放端口：
            - 例如开放 `ssh` 端口：`firewall-cmd --zone=public --add-port=22/tcp --permanent`
            - 使之生效：`firewall-cmd --reload`
            - 检查是否生效：`firewall-cmd --zone=public --query-port=22/tcp`
            - 或者 `sudo ufw allow 端口`
        - 关闭端口：
            - 限制 `ssh` 端口：`firewall-cmd --zone=public --remove-port=22/tcp --permanent`
            - 或者 `sudo ufw denty 端口`

### 日志管理与分析

