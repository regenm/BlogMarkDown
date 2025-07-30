---
title: Raspberry Pi
date: 2024-03-16 18:18:50
tags: [Raspberry Pi, 笔记,linux]
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

