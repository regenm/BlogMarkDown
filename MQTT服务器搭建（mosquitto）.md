---
title: MQTT服务器搭建（mosquitto）
date: 2024-02-09 00:58:48
tags: [笔记, linux, mqtt, IoT, mosquitto]
---

# MQTT服务器搭建（mosquitto）

## 0.简介

1. 简介

>MQTT也称为"消息代理"（Broker）。
>
>MQTT是一个基于客户端-服务器的消息发布/订阅传输协议。MQTT协议是轻量、简单、开放和易于实现的，这些特点使它适用范围非常广泛。在很多情况下，包括受限的环境中，如：机器与机器（M2M）通信和物联网（IoT）。其在，通过卫星链路通信传感器、偶尔拨号的医疗设备、智能家居、及一些小型化设备中已广泛使用。

2. 原理

> MQTT协议原理
> MQTT协议实现方式
> 实现MQTT协议需要客户端和服务器端通讯完成，在通讯过程中，MQTT协议中有三种身份：发布者（Publish）、代理（Broker）（服务器）、订阅者（Subscribe）。其中，消息的发布者和订阅者都是客户端，消息代理是服务器，消息发布者可以同时是订阅者。
>
> MQTT传输的消息分为：主题（Topic）和负载（payload）两部分：
>
> （1）Topic，可以理解为消息的类型，订阅者订阅（Subscribe）后，就会收到该主题的消息内容（payload）
>
> （2）payload，可以理解为消息的内容，是指订阅者具体要使用的内容。



## 1. mosquitto 服务端

* 安装

```bash
apt install mosquitto
```

* 配置

```bash
cd /etc/mosquitto
```

* 修改配置文件

​				在 **/etc/mosquitto/conf.d**目录下有一个叫 README 的文件，上面写了信息：

> # Any files placed in this directory that have a .conf ending will be loaded as config files by the broker. Use this to make your local config.

```bash
cd /etc/mosquitto/conf.d

vim config.conf
# 添加下面配置
# 不允许匿名访问
allow_anonymous false
# 配置端口
listener 1883
# 设置密码文件保存目录
password_file /etc/mosquitto/pwfile.example
```

* 运行配置文件

```bash
mosquitto -c /etc/mosquitto/conf.d/config.conf -v
```

* 添加用户

```bash
mosquitto_passwd -c /etc/mosquitto/pwfile.example username
```

* **关闭防火墙！！！**

```bash
ufw disable
# 或者添加规则
ufw allow 1883
```

## 2. mosquitto-clients 客户端

* 用户登录订阅，发布

​	其实一般都是接近被控设备的系统，比如各种Mcu（esp32，esp8266）等等。这部分将在寒假进行补充（在另外一篇博客）。[esp8266或者esp32等](https://regenm.gitee.io)

对于Linux客户，可以是模拟器（ ISH ，Termux）

* 安装

```bash
apt install mosquitto-clients
```

* 订阅

```bash
mosquitto_sub -h "IP address" -p 1883 -u username -P regen -t "test"
```

* 发布

```bash
mosquitto_pub -h "IP address" -p 1883 -u username -P regen -t "test" -m "hello mqtt! hello ! mosquitto!"
```

 

## 3. mosquitto使用

* **脚本制作**

​				为了方便 mosquitto 的使用，可以写几个脚本用于发布和订阅

1.  发布

```bash
vim regenMosquittoScriptSendRemote.sh

#!/bin/bash

mosquitto_pub -h "IP address" -p 1883 -u username -P regen -t "test" -m "$1"
```

​	赋权

```bash
chmod 777 regenMosquittoScriptSendRemote.sh
```

​	运行

```bash
./regenMosquittoScriptSendRemote.sh hello!
```

2. 订阅，同理

```bash
vim regenMosquittoScriptSubRemote.sh

#!/bin/bash

mosquitto_sub -h "IP address" -p 1883 -u username -P regen -t "$1"
```

​	赋权

```bash
chmod 777 regenMosquittoScriptSubRemote.sh
```

​	运行

```bash
./regenMosquittoScriptSendRemote.sh topic
```

* 连接esp32或8266

看看这个esp8266连接mqtt服务器的项目吗？([esp8266连接服务器（基于mosquitto的mqtt server） | Regen (gitee.io)](https://regenm.gitee.io/2024/02/09/esp8266连接服务器（基于mosquitto的mqtt-server）/)
