---
title: Raspberry Pi
date: 2024-03-16 18:18:50
tags: [Raspberry Pi, 笔记,linux]
categories: [软硬件技术]
---

# 嘿嘿开新坑！（目前基于python）

​	早就久仰树莓派大名，奈何前两年芯片减产导致树莓派价格居高不下，甚至翻倍。现在终于降下来了，于是斥巨资买了一块3B前来学习。本篇Blog用于记录我的探索历程。

​	目前打算先使用python进行学习，待到有一定理解之后再使用C。

## 1. 装系统！

​	要什么桌面环境，直接上命令行！

## 2.联网-WIFI

* 方法一：使用系统工具

```bash
sudo raspi-config
```

选择系统选项，地区之后就可以通过`ssid`和`pask`进行联网

再使用`ifconfig`查看是否连接成功（会分配新的 ip 地址）

* 方法二：使用系统配置`/etc/`

```bash
# 一开始没有VIM
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

# add 

network={
        ssid="ssid_name"
        key_mgmt=WPA-PSK
        psk="password"
}
```



## 3. 创建新用户并授权

​	总不能一直用root吧，qwq

```bash
# add user
adduser regen
# grant privilege
visudo
# add regen to sudoers
regen   ALL=(ALL:ALL)       ALL
```

## 4.来点个灯吧！

1. 代码界的点灯

先看看这个gcc工具

```bash
gcc -v
```

嘿嘿，是`aarch-linux-gnu`也就是arm架构的gcc版本啦，终于不是` x86_64-w64-mingw32`啦。

```bash
vim hello_RaspberryPi.c
```

2. 物理点灯

### 4.1 引脚信息

* 看看引脚配置

```bash
pinout
```

或者

```bash
gpio readall
```

然后就报错了。。。

没有安装

```bash
sudo apt install wiringpi
```

又报错了。。。没有依赖

```bash
sudo apt --fix-broken install
```

解决

### 4.2  代码控制

#### 4.2.1 使用Python的命令模式

```python
>>> import RPi.GPIO as GPIO
>>> GPIO.setmode(GPIO.BOARD)
>>> GPIO.setup(11,GPIO.OUT)
>>> GPIO.output(11,True)
```

于是报错了，没有权限，直接换root就好了。好了现在又精通一个开发板的点灯了嘿嘿

## 5. 远程ssh

​	学以致用，之前在` Linux Projects`里面学习了使用`ZeroTier`进行内网穿透以提供远程服务。试试在我的内网频道加入新的成员`Raspberry PI`吧 ！

* 开头也需要加`sudo`，否则报错

```bash
sudo curl -s https://install.zerotier.com | sudo bash
```

* 加入我的内网

```bash
zerotier-cli join <NETWORK ID>
```

* 开机自启动

1. 新建一个脚本

```bash
vim AutoStart/ZeroTierAutoStart.sh
```

```bash
#!/bin/sh
sudo zerotier-cli join <network ID>
echo "zerotier network joined successfully"
```

2. 添加自启动

```
vim /etc/rc.local
```

添加

```bash
su regen -c "exec /home/regen/AutoStart/ZeroTierAutoStart.sh"
```

3. 结束！

​	现在就可以只需要给树莓派供个电就行了，可以远程写脚本，玩树莓派咯。

# 基础操作

### 1. 查看系统版本

```bash
cat /etc/os-release
```

### 2. 查看CPU信息

```bash
cat /proc/cpuinfo
```

### 3. 查看内存使用情况

```bash
free -h
```

### 4. 查看磁盘空间使用情况

```bash
df -h
```

### 5. 查看当前系统负载

```bash
top
```

或

```bash
htop  # (需要先安装：`sudo apt install htop`)
```

### 6. 查看树莓派的温度

```bash
vcgencmd measure_temp
```

### 7. 查看网络信息

```bash
ifconfig
```

### 8. 查看启动时间和运行时长

```bash
uptime
```



## 系统特有命令 GNU/Linux Debian 11 (Bullseye)

### 1. **改进的 `apt` 命令**

- Bullseye 使用改进版的 

    ```
    apt
    ```

    ，可以更简便地更新和管理软件包：

    ```bash
    sudo apt update        # 更新软件源列表
    sudo apt upgrade       # 升级已安装的软件包
    sudo apt full-upgrade  # 升级并处理依赖关系（更适合大版本更新）
    ```

### 2. **查看系统信息的改进命令**

- Debian 11 引入了一些新工具，例如 

    ```bash
    neofetch
    ```

### 3. **硬件信息查询工具 `lscpu` 和 `lsblk`**

- 这些命令在 Bullseye 上改进，可以详细查看硬件信息：

    ```bash
    lscpu    # 查看 CPU 信息
    lsblk    # 查看存储设备信息
    ```

### 4. **网络管理工具 `nmcli` 和 `nmtui`**

- Network Manager 提供的 

    ```
    nmcli
    ```

     和 

    ```
    nmtui
    ```

     命令方便管理网络连接，特别适合命令行环境：

    ```bash
    nmcli device status     # 查看网络设备状态
    nmtui                    # 图形化界面配置网络连接（基于终端）
    ```

### 5. **防火墙设置：`ufw` 简化版**

- Debian 11 支持 

    ```
    ufw
    ```

    （Uncomplicated Firewall），可以简化防火墙管理：

    ```bash
    sudo apt install ufw
    sudo ufw enable            # 启用防火墙
    sudo ufw status            # 查看防火墙状态
    sudo ufw allow 22/tcp      # 允许端口，例如 SSH
    ```

### 6. **进程监控工具 `bpytop`**

- ```
    bpytop
    ```

     是 Bullseye 上一个增强的进程监控工具，提供类似 

    ```
    htop
    ```

     的界面但更加丰富：

    ```
    bash复制代码sudo apt install bpytop
    bpytop
    ```

### 7. **系统日志管理 `journalctl`**

- ```
    journalctl
    ```

     是查看系统日志的命令行工具，可以通过以下方式查询系统状态和故障信息：

    ```
    bash复制代码journalctl -b            # 查看本次启动的所有日志
    journalctl -u ssh        # 查看 SSH 服务日志
    journalctl --disk-usage  # 查看日志占用的磁盘空间
    ```

### 8. **时间同步 `timedatectl`**

- Bullseye 默认使用 

    ```
    systemd-timesyncd
    ```

     来管理时间同步，

    ```
    timedatectl
    ```

     可查询和调整时间设置：

    ```
    bash复制代码timedatectl status       # 查看时间状态
    sudo timedatectl set-timezone Asia/Shanghai  # 设置时区
    ```

### 9. **用户和权限管理**

- 可以通过 

    ```
    usermod
    ```

     等命令快速配置用户权限：

    ```
    bash复制代码sudo usermod -aG sudo username  # 添加用户到 sudo 组
    sudo passwd username            # 设置或更改用户密码
    ```

### 10. **软件包清理 `autoremove` 和 `clean`**

- 可以清理不再需要的依赖包和缓存，以释放系统空间：

    ```
    bash复制代码sudo apt autoremove      # 移除不再使用的依赖
    sudo apt clean           # 清理软件包缓存
    ```

这些命令和工具在 Bullseye 上都能流畅使用，适合从硬件到网络的各种管理需求。如果有具体需求，还可以深入某个方面进行详细配置。



# 作为硬件加速器

* 硬件加速器

> 专门设计用于加速特定计算任务的硬件设备。在这里主要用于网络优化，进行流量优化，缓存，路由优化。

* **代理服务器**：例如Shadowsocks、V2Ray等，可以作为本地代理服务，转发流量到远程服务器。
* **缓存服务器**：例如Squid，可以作为缓存代理，存储常用的数据来减少对外网的访问。
* **TCP优化工具**：如安装TCP BBR加速算法，优化数据传输效率。
