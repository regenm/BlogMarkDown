---
title: Linux磁盘操作
date: 2025-07-14 17:50:39
tags: [笔记, linux, system,command,运维,磁盘操作]
categories: [软件技术]
---

# Linux 磁盘操作总览

> 常用命令：`lsblk`、`fdisk`、`parted`、`mkfs`、`mount`、`blkid`、`df`、`lsblk`、`vim /etc/fstab`

------

##  查看磁盘和分区信息

### `lsblk`

显示块设备（disk、part）的结构：

```bash
lsblk
```

示例：

![image-20250715092022905](../images/Linux/运维/disk/image-20250715092022905.png)

------

### `fdisk -l`

列出所有磁盘和分区的详细信息（包含分区类型、UUID 等）：

```bash
fdisk -l
```

![image-20250715092323809](../images/Linux/运维/disk/image-20250715092323809.png)

---



##  分区

假设要对 `/dev/sdb` 新硬盘进行分区：

### MBR 分区  `fdisk /dev/sdb`

交互式工具：

- `n` → 新建分区
- `p` → 主分区
- `1` → 分区号
- 回车 → 默认第一个扇区
- 回车 → 默认最后一个扇区（或输入 +100G 指定大小）
- `w` → 写入分区表并退出

![image-20250715092714083](../images/Linux/运维/disk/image-20250715092714083.png)

![image-20250715092726925](../images/Linux/运维/disk/image-20250715092726925.png)

可以发现已经创建好了sdb1

> ⚠ 注意：GPT 分区表可以用 `parted` / `gdisk`，大磁盘（>2TB）推荐 GPT。

### GPT  分区`parted` 大容量以及现代机器推荐

```bash
parted /dev/sdb
mklabel gpt
mkpart primary xfs 0% 100%
```

![image-20250715093446008](../images/Linux/运维/disk/image-20250715093446008.png)

* 如果是大容量磁盘，例如几十T需使用xfs文件系统。

### 查看磁盘分区类型

```bash
parted /dev/sdb print
```

![image-20250715093327413](../images/Linux/运维/disk/image-20250715093327413.png)

------



## 格式化（创建文件系统）

假设新建的分区是 `/dev/sdb1`：

### `mkfs` 工具：

- ext4：

```bash
mkfs.ext4 /dev/sdb1
```

![image-20250715093626175](../images/Linux/运维/disk/image-20250715093626175.png)

- xfs（适合大文件/高性能场景）：

```bash
mkfs.xfs /dev/sdb1
```

- 查看支持的文件系统：

```bash
ls /sbin/mkfs*
```

![image-20250715093712405](../images/Linux/运维/disk/image-20250715093712405.png)

---

## 挂载分区

假设要挂载到 `/data`：

```bash
mkdir -p /data
mount /dev/sdb1 /data
```

查看当前挂载情况：

```bash
lsblk
df -h
```

![image-20250715093903586](../images/Linux/运维/disk/image-20250715093903586.png)

![image-20250715093950133](../images/Linux/运维/disk/image-20250715093950133.png)

---

## 开机自动挂载

获取分区 UUID：

```bash
blkid /dev/sdb1
```

输出：

![image-20250715094012956](../images/Linux/运维/disk/image-20250715094012956.png)

**复制uuid**

编辑 `/etc/fstab`：

```bash
vim /etc/fstab
```

添加一行：

```
UUID=098f85d8-3753-444b-9d03-309052d9c645 /data ext4 defaults 0 0
```



- UUID：分区唯一标识符
- `/data`：挂载点
- `ext4`：文件系统类型
- `defaults`：默认参数（读写、自动挂载等）
- `0 0`：是否做 dump 和 fsck 检查

最后可以通过`reboot`测试一下是否成功挂载

------

##  文件系统查看 & 检查

- 查看磁盘使用情况：

```bash
df -h
```

- 查看分区类型和 UUID：

```bash
blkid
```

- 查看详细挂载信息：

```bash
mount
```

- 检查文件系统（例如 ext4）：

```bash
fsck /dev/sdb1
```

------

## **总结：Linux 磁盘完整操作流程**：

| 步骤       | 命令                              | 说明                     |
| ---------- | --------------------------------- | ------------------------ |
| 查看磁盘   | lsblk / fdisk -l                  | 确认设备                 |
| 分区       | fdisk /dev/sdb                    | 创建新分区               |
|            | parted                            | 创建新分区、查看分区类型 |
| 格式化     | mkfs.ext4 /dev/sdb1               | 创建文件系统             |
| 创建挂载点 | mkdir /data                       | 挂载目录                 |
| 挂载       | mount /dev/sdb1 /data             | 手动挂载                 |
| 自动挂载   | blkid 获取 UUID + 编辑 /etc/fstab | 开机自动挂载             |

------

#### 附：

1. 表格 ①：**MBR 内部分区结构**（主分区 / 扩展分区 / 逻辑分区 对比）
2. 表格 ②：**MBR vs GPT 分区表 对比**

## 📦 表格①：MBR 分区结构

| 类型                | 数量限制                            | 是否可直接挂载 | 用途与特点                            |
| ------------------- | ----------------------------------- | -------------- | ------------------------------------- |
| 主分区 (Primary)    | 最多 4 个                           | ✅ 可以         | 最简单直接，可直接格式化挂载          |
| 扩展分区 (Extended) | 最多 1 个（必须占一个主分区的位置） | ❌ 不可以       | 容器分区，用来突破最多 4 个分区的限制 |
| 逻辑分区 (Logical)  | 无限（通常 /dev/sda5 开始）         | ✅ 可以         | 建在扩展分区内部，也可直接格式化挂载  |



------

## 🧰 表格②：MBR vs GPT 分区表

| 特点           | MBR                                    | GPT                                |
| -------------- | -------------------------------------- | ---------------------------------- |
| 最大支持容量   | 单盘最大约 2TB                         | 理论支持到 18EB（几乎无限）        |
| 分区总数       | 最多 4 个主分区；超过需用扩展+逻辑分区 | 默认可建 128 个分区（都平等）      |
| 扩展/逻辑分区  | 有                                     | 无                                 |
| 容错           | 无                                     | 有主备分区表，更可靠               |
| 启动方式兼容性 | 传统 BIOS 启动（只支持 MBR）           | 推荐 UEFI 启动；也能配合 BIOS 启动 |
| 使用场景       | 小盘（≤2TB），老系统                   | 大盘、新硬件、新系统               |

---

# 问题

# 1. ~~tmd~~死活挂不上

情景：

1. 一块1T磁盘sdb
2. 挂载到`/data`
3. 直接挂载或者是fstab用UUID挂载都失败

解决方式：

1. 先查看一下日志，看看是什么导致的。

```
dmesg | tail -30
journalctl -xe | tail -30
```

例如我的：

```SPARQL
-- data.mount 单元已结束停止操作。
7月 16 15:26:52 localhost.localdomain kernel: EXT4-fs (sdb1): mounted filesystem with ordered data mode. Opts: (null)
7月 16 15:26:52 localhost.localdomain systemd[1]: data.mount: Unit is bound to inactive unit dev-disk-by\x2duuid-8d489e7f\x2d3f79\x2d4678\x2db082\x2d85fc6fe18c4.device. Stopping, too.
7月 16 15:26:52 localhost.localdomain systemd[1]: Unmounting /data...
-- Subject: data.mount 单元已开始停止操作
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
-- 
-- data.mount 单元已开始停止操作。
7月 16 15:26:52 localhost.localdomain systemd[1]: data.mount: Succeeded.
-- Subject: Unit succeeded
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
-- 
-- The unit data.mount has successfully entered the 'dead' state.
7月 16 15:26:52 localhost.localdomain systemd[1]: Unmounted /data.
-- Subject: data.mount 单元已结束停止操作
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
```

这一行

`data.mount: Unit is bound to inactive unit dev-disk-by\x2duuid8d489e7f\x2d3f79\x2d4678\x2db082\x2d85fc6fe18c4.device. Stopping, too.`

喂给Ai:

> 出现“data.mount”单元在不停挂载卸载的情况，说明 systemd 里可能残留了老的挂载单元配置，绑定了之前的错误 UUID。

* 接下来：

```bash
# 取消残留的 systemd 挂载单元
systemctl stop data.mount
systemctl disable data.mount
# 清理可能的残留文件
ls /etc/systemd/system/data.mount
ls /usr/lib/systemd/system/data.mount
# 如果存在，执行
rm /etc/systemd/system/data.mount
# mv /etc/systemd/system/data.mount /root/
mv /etc/systemd/system/data.mount /root/
systemctl daemon-reload

```

然后就可以挂上了，可能是第一次没操作好，乱挂之后更挂不上了。新技巧：**取消残留的 systemd 挂载单元**

