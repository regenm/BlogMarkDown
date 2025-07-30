---
title: Linux Notes
date: 2023-11-28 19:49:18
tags: [笔记, linux, system,command]
---



A note about linux basic operation.

# *系统操作

## 1.显示后台指令

​	1.提供所有进程信息

```shell
ps aux
```

2. ## 提供父进程信息

```shell
ps lax
```

## 	2. 终止某项进程

```shell
kill pid
```

* kill的意思实际上不是“杀死” ， kill 只是像程序发送一个信号

3. ## 查看硬件信息

* 查看内存占用和 swap 分区占用

```bash
free -h
```

* ## 查看CPU使用情况

```
top
```

# *文件操作

## vim 编辑器

![img](https://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)



## more

- 空白键 (space)：代表向下翻一页；
- Enter     ：代表向下翻『一行』；
- /字串     ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；
- :f      ：立刻显示出档名以及目前显示的行数；
- q       ：代表立刻离开 more ，不再显示该文件内容。
- b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。

## less

- 空白键  ：向下翻动一页；
- [pagedown]：向下翻动一页；
- [pageup] ：向上翻动一页；
- **/字串   ：向下搜寻『字串』的功能；**
- **?字串   ：向上搜寻『字串』的功能；**
- **n     ：重复前一个搜寻 (与 / 或 ? 有关！)**
- **N     ：反向的重复前一个搜寻 (与 / 或 ? 有关！)**
- q     ：离开 less 这个程序；

## head

​	读取前几行文本

```shell
	head -n 20 text.txt
```

## tail

​	与head相反

```shell
	tail -n 20 text.txt
```

## 	使用gzip进行压缩和解压缩

​	压缩：

```bash
gzip linuxFiles.tar
```

​	

​	解压缩： 

```
gunzip linuxFiles.tar.gz
gzip -d linuxFiles.tar.gz
```

2.文件打包tar

​	1.打包文件

```shell
tar -cvf linuxFiles.tar home/
```

​	2.解开打包后的文件

```shell
tar -xvf linuxFiles.tar
```

	3. tar -w 选项，每次将选择单个文件抽出或加入

```shell
tar -xvwf linuxFiles.tar //
```

4. tar -z 自动调用gzip进行解压缩

```shell
tar -czvf linuxFiles.tar.gz home/
等价于
tar -cvf linuxFiles.tar home/
gzip linuxFiles.tar
```

```shell
tar -xzvf linuxFiles.tar.gz home/
等价于
gzip -d linuxFiles.tar.gz
tar -xvf linuxFiles.tar
```

# *用户权限

![img](https://www.runoob.com/wp-content/uploads/2014/06/file-llls22.jpg)

# *开放端口

## 1. 查看端口

### 1.查看某个端口（使用管道+过滤）

#### 1. 一般情况下

```bash
netstat -anp | grep 8080	# -a(all)n(numeric)p(programs)

```

#### 2. netstat的其他参数

> -a (all) 显示所有选项，默认不显示LISTEN相关。
> -t (tcp) 仅显示tcp相关选项。
> -u (udp) 仅显示udp相关选项。
> -n 拒绝显示别名，能显示数字的全部转化成数字。
> -l 仅列出有在 Listen (监听) 的服务状态。
> -p 显示建立相关链接的程序名
> -r 显示路由信息，路由表
> -e 显示扩展信息，例如 uid 等
> -s 按各个协议进行统计
> -c 每隔一个固定时间，执行该 netstat 命令
> LISTEN 和 LISTENING 的状态只有用 -a 或者 -l 才能看到



## 2.开放端口(通用)

### 1.防火墙的设置

**防火墙这一块后续还需要学习一下，很多情况下我都是直接关。。。**

#### 1. 查看防火墙状态

```
systemctl status firewalld
```

或者（ufw）

```bash
sudo ufw status
```



#### 2.开启防火墙

```bash
systemctl start firewalld
systemctl enable firewalld
```

或者（ufw）

```bash
sudo ufw enable
```



#### 3. 关闭防火墙

```
systemctl stop firewalld
```

或者（ufw）

```bash
sudo ufw disable
```



### 2.端口操作

#### 1.开放端口

* 例如开放ssh端口

```bash
firewall-cmd --zone=public --add-port=22/tcp --permanent
```

* 使之生效

```bash
firewall-cmd --reload
```

* 检查是否生效

```
firewall-cmd --zone=public --query-port=22/tcp
```

或者

```bash
sudo ufw allow 端口
```

#### 2.关闭端口

* 限制ssh端口

```bash
firewall-cmd --zone=public --remove-port=22/tcp --permanent
```

或者（ufw）

```bash
sudo ufw denty 端口
```

# *任务调度

## 关键词

> * **任务调度**：系统在某个时间定时执行特定的命令或程序
> * 任务调度的分类：
>   * **系统任务调度**：系统某些工作需要周而复始、重复地执行，如病毒扫描、日志清理
>   * **用户任务调度**：个别用户可能需要定时执行的工作，如备份数据库、用户数据备份

## crontab

* 基本介绍

> 1. 系统任务调度配置文件目录：`/etc/crontab`
> 2. 用户的crontab文件放置于`/var/spool/cron`

* 指令介绍

1. 参数选项

| 参数 | 解释                                                       |
| :--: | ---------------------------------------------------------- |
|  -e  | e是edit的意识，即编辑/设置/修改当前用户的定时任务          |
|  -l  | l是list的意思，即列出/查看/打印/输出当前用户的全部定时任务 |
|  -r  | r是remove的意思，即移除/删除当前用户的全部定时任务         |

2. 语法介绍

```bash
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed

```

* 开头`*  *  *  *  * `参数的解释

| 符号      | 案例        | 解释                    |
| --------- | ----------- | ----------------------- |
| *         | 1 2 * * *   | 每天的2:01执行一次      |
| */n       | */5 * * * * | 每隔5分钟执行一次       |
| ,(和)     | 1 2 3,4 * * | 每月的3,4日2:01执行一次 |
| -（范围） | 1 2 3-6 * * | 每月的3-6日2:01执行一次 |

* 使用方法

1. 进入编辑模式

```bash
crontab -e
```

2. 编写任务

```bash
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
```

* 编辑后保存退出即可

# * 磁盘，分区，挂载操作

## linux硬盘文件系统介绍:

### ext4:

* 与ext3相比，ext4文件系统可支持最高1EB的分区与最大16TB的文件

* 拓展了子目录的数量，理论上可以无限个

* 与ext3相比，引入了块组的概念，提高了存取的效率

* 预留空间、延迟获取空间，减少了文件的分散

* 更详细的inodes，提高了系统的性能

* 可以实现快速的文件系统检查

* 提供日志校验和，提高了可靠性

## 查看设备挂载情况

1. 所有设备

```bash
lsblk
lsblk -f
```

2. 查看硬盘使用情况

```bash
df -h
```



## 新硬盘的分区以及挂载流程

1. 查看是否已经存在设备

```bash
lsblk
lsblk -f
```

或者查看`/dev/`下是否存在设备

2. 使用fdisk进行分区设置

```bash
fdisk /dev/sdb
```

​	3.分区格式化

```bash
mkfs  -t  ext4     /dev/sdb1
```

4. 挂载硬盘到文件夹

使用`mount`

```bash
mount /dev/sdb1 /newDisk
```

5. 取消挂载

```bash
unmount /dev/sdb1
```

