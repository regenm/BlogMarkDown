---
title: Linux Projects
date: 2023-11-28 19:49:32
tags: [笔记, linux]
---



​	以下项目都是我在已经完成之后补录的笔记，包括但不限于遇到的报错，问题。便于以后复现。实际上不能说是项目，仅仅是复现并且享受大佬的成果而已。开源不易，感谢大佬们开源！



# ZeroTier 内网穿透

## 1.简介

​	官网：[ZeroTier](https://www.zerotier.com/)

> **ZeroTier Client :**
>
> The ZeroTier client is used to connect to virtual networks previously created in the ZeroTier Central web-based [UI](https://en.wikipedia.org/wiki/User_interface). Endpoint connections are peer-to-peer and [end-to-end encrypted](https://en.wikipedia.org/wiki/End-to-end_encryption). [STUN](https://en.wikipedia.org/wiki/STUN) and [hole punching](https://en.wikipedia.org/wiki/Hole_punching_(networking)) are used to establish direct connections between peers behind [NAT](https://en.wikipedia.org/wiki/Network_Address_Translation). Direct connection route discovery is made with the help of a global network of *root servers* via a mechanism similar to [ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) in [WebRTC](https://en.wikipedia.org/wiki/WebRTC).

## 2.安装

​		实际上十分简单，对于Windows，iPad，Android，macOS都有相应软件。

![](/images/ZeroTIerDownload.png)

### linux

1. 安装

```bash
curl -s https://install.zerotier.com | sudo bash
```

2. 操作：加入，离开，查看已经连接的网络

```bash
zerotier-cli join <NETWORK ID>
#加入
zerotier-cli leave <NETWORK ID>
#离开
zerotier-cli listnetworks
#连接列表
```

## 3.管理

​	直接登录官网即可进行对已组网络的管理，例如授权用户，修改网络内部IP地址以及备注信息等等

![Admin Page](/images/ZeroTierAdmin.jpg)



## 4.Last

​	世上还是好人多啊，ZeroTier的出现真的让内网穿透方便了很多，免费用户的配置也很不错！



# FTP 服务器搭建



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

# VPN隧道搭建

## 1.协议选择 V2RAY的VMess协议

## 2.一键安装脚本 ：v2ray.sh

```bash
sudo su root

bash <(curl -s -L https://git.io/v2ray.sh)
```

## 3.查看信息

信息目录：` /etc/v2ray/config.json`

# 自建服务器（搭配ZeroTier食用）

## 1. begin

​	虽然和hxy合伙买的白菜价国外服务器性能也还够用，但是也仅仅是够用，之前发现的内网穿透神器Zerotier刚好可以拿来当我的梦中情机的最后一块拼图。使用ZeroTier就可以让我方便的在SH连接到JX的家中局域网内的服务器了！

## 2.介绍

### 	1.硬件介绍

​			硬件部分的清单：

> CPU : E3 1230 v2 (4核8线，3.3ghz)
>
> 显卡 ： gt610 	（亮机即可，且低功耗）
>
> 内存：8g
>
> 外存：240（固态）+500（机械）

### 	2.软件介绍

​		由于我正在使用的pc中恰好有四个下载好的镜像文件，分别是 macOS.Ventura_13.4 , deepin ，ubuntu22.04，kali2023，最终使用的是kali,原因在于kali已经安装好绝大部分软件和其需运行的环境，例如：httpd，JDK，javarun，makefile等等。

## 3.组装（跳过）

## 4.配置ZeroTier(如上篇ZeroTier内网穿透)

## 5.结束

最终效果，通过ipad的终端ssh外网连接kali

![ipadConnectKali](/images/ipadConnectKali.jpg)

# makefile



## 1.make简要介绍



> ​	In [software development](https://en.wikipedia.org/wiki/Software_development), **Make** is a [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that [builds](https://en.wikipedia.org/wiki/Software_build) [executable programs](https://en.wikipedia.org/wiki/Executable_program) and [libraries](https://en.wikipedia.org/wiki/Library_(software)) from [source code](https://en.wikipedia.org/wiki/Source_code) by reading [files](https://en.wikipedia.org/wiki/File_(computing)) called *makefiles* which specify how to derive the target program. Though [integrated development environments](https://en.wikipedia.org/wiki/Integrated_development_environment) and [language](https://en.wikipedia.org/wiki/Programming_language)-specific [compiler](https://en.wikipedia.org/wiki/Compiler_(computing)) features can also be used to manage a build process, Make remains widely used, especially in [Unix](https://en.wikipedia.org/wiki/Unix) and [Unix-like](https://en.wikipedia.org/wiki/Unix-like) [operating systems](https://en.wikipedia.org/wiki/Operating_system).

> ​	Make is not limited to building programs. It can also be used to manage any project where some files need to be updated automatically from other files whenever the other files change.



## 2.makefile简要介绍

​	makefile文件用于告诉make命令如何执行编译和链接。

​	当一个文件具有很多个.c文件和.h文件时，如果将makefile写好了，就只需要一行代码`make`即可完成编译链接或者`make clean`删除所有目标文件，同时还能具备以下特性：

> ​      1.如果这个工程没有编译过，那么所有C文件都会编译并被链接。
>
> ​      2.如果这个工程的某几个C文件被修改，那么只编译被修改的C文件，并链接目标程序。
>
> ​      3.如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的C文件，并链接目标程序。





## 3.makefile的规则

```makefile
 target ... : prerequisites ...

          command

          ...

          ...
         -------------------------------------------------------------------------------
```

* target : 目标文件（可以使object文件，可执行文件，还可以是**标签**）
* prerequisites ：生成target所需要的文件或是目标。
* command也就是make需要执行的命令。（Shell命令）

## 4.makefile的使用

​	makefile的规则看似十分简单，但是写好一个makefile却是不简单的。下面举例说明```

```bash
#假设已经存在3个c文件，4个.h文件，分别是:
cFile1.c cFile2.c cFile3.c
hFile1.h hFile2.h hFile3.h hFile4.h
```

​		如果我们最终想要生成可执行文件main，那么makefile可以这样写，以下是我使用在线makefile生成器生成的。

​		同时介绍一下gcc的参数：

> * -g可执行程序包含调试信息
>   -g为了调试用的
>   加个-g 是为了gdb 用，不然gdb用不到
>
> * -o指定输出文件名
>   -o output_filename，确定输出文件的名称为output_filename，同时这个名称不能和源文件同名。如果不给出这个选项，gcc就给出
>
> 
>
> * -c 只编译不链接
>   产生.o文件，就是obj文件，不产生执行文件
>
> * -D 其意义是添加宏定义
>
> 
>
> * -w的意思是关闭编译时的警告，也就是编译后不显示任何warning，因为有时在编译之后编译器会显示一些例如数据转换之类的警告，这些警告是我们平时可以忽略的。
>
> 
>
> * -W选项类似-Wall，会显示警告，但是只显示编译器认为会出现错误的警告。
>
> * -Wall选项意思是编译后显示所有警告

```makefile
#
# In order to execute this "Makefile" just type "make"
#	A. Delis (ad@di.uoa.gr)
# web: https://solver.assistedcoding.eu/makefilegen
#

OBJS	= cFile1.o cFile2.o cFile3.o
SOURCE	= cFile1.c cFile2.c cFile3.c
HEADER	= hFile1.h hFile2.h hFile3.h hFile4.h
OUT	= main.out
CC	 = gcc
FLAGS	 = -g -c -Wall				 
LFLAGS	 = 
# -g option enables debugging mode 
# -c flag generates object code for separate files


all: $(OBJS)
	$(CC) -g $(OBJS) -o $(OUT) $(LFLAGS)


# create/compile the individual files >>separately<<
cFile1.o: cFile1.c
	$(CC) $(FLAGS) cFile1.c -lcunit

cFile2.o: cFile2.c
	$(CC) $(FLAGS) cFile2.c -lcunit

cFile3.o: cFile3.c
	$(CC) $(FLAGS) cFile3.c -lcunit


# clean house
clean:
	rm -f $(OBJS) $(OUT)

# run the program
run: $(OUT)
	./$(OUT)
```



# cmake

## 1. 简要介绍

> From Wikipedia, the free encyclopedia
>
> In [software development](https://en.wikipedia.org/wiki/Software_development), **CMake** is [cross-platform](https://en.wikipedia.org/wiki/Cross-platform) [free and open-source software](https://en.wikipedia.org/wiki/Free_and_open-source_software) for [build automation](https://en.wikipedia.org/wiki/Build_automation), [testing](https://en.wikipedia.org/wiki/Software_testing), [packaging](https://en.wikipedia.org/wiki/Package_(package_management_system)) and [installation](https://en.wikipedia.org/wiki/Installation_(computer_programs)) of software by using a [compiler](https://en.wikipedia.org/wiki/Compiler)-independent method.[[3\]](https://en.wikipedia.org/wiki/CMake#cite_note-3) CMake is not a build system itself; it generates another system's build files.[[4\]](https://en.wikipedia.org/wiki/CMake#cite_note-:1-4) It supports directory hierarchies and applications that depend on multiple libraries. It can invoke native build environments such as [Make](https://en.wikipedia.org/wiki/Make_(software)), [Qt Creator](https://en.wikipedia.org/wiki/Qt_Creator), [Ninja](https://en.wikipedia.org/wiki/Ninja_(build_system)), [Android Studio](https://en.wikipedia.org/wiki/Android_Studio), Apple's [Xcode](https://en.wikipedia.org/wiki/Xcode), and [Microsoft Visual Studio](https://en.wikipedia.org/wiki/Microsoft_Visual_Studio).[[4\]](https://en.wikipedia.org/wiki/CMake#cite_note-:1-4) It has minimal dependencies, requiring only a [C++](https://en.wikipedia.org/wiki/C%2B%2B) compiler on its own build system.[[4\]](https://en.wikipedia.org/wiki/CMake#cite_note-:1-4)
>
> CMake is distributed as [free and open-source software](https://en.wikipedia.org/wiki/Free_and_open-source_software) under a permissive [BSD-3-Clause](https://en.wikipedia.org/wiki/BSD_licenses) license.[[](https://en.wikipedia.org/wiki/CMake#cite_note-5)

​	我的理解就是cmake是一款能够夸平台的生成makefile的工具，这样就不需要写复杂的makefile了。



## 2.	cmake使用

### 	1.使用流程 

	1. 在项目根目录下创建一个名为` CMakeLists.txt`的文件

2. 编写该文件（当文件比较少的时候）

```cmake
cmake_minimum_required(VERSION 3.15)	# 版本要求

# set the project name
project(firstProject)

# add the executable（包括所有的用到的源文件）
add_executable(firstOut a.cpp b.cpp)
```

3. 使用cmake命令生成makefile

* cmake命令的使用

```bash
Usage

  cmake [options] <path-to-source>
  cmake [options] <path-to-existing-build>
  cmake [options] -S <path-to-source> -B <path-to-build>

Specify a source directory to (re-)generate a build system for it in the
current working directory.  Specify an existing build directory to
re-generate its build system.

```

4. 使用make得到输出

### 	2.单个源文件的项目	

1. 编写main.cpp

```cpp
#include<stdio.h>
int main(){printf("hello\n");return 0;}
```

2. 编写CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)

# set the project name
project(firstProject)

# add the executable
add_executable(firstOut main.cpp)
```

3. 创建输出文件夹并且进行输出

```bash
mkdir build
cmake -S . -B ./build/
```

4. 使用make进行编译链接即可得到可执行文件`firstOut`

```bash
make
```

### 3. CMakeLists.txt 的写法

#### 1.使用set

* 定义变量 **set** ，类型为字符串

```cmake
set(outName[forstOut])
```

* 使用宏

```cmake
set(CMAKE_CXX_STANDARD 11)   			# 指定c++标准的宏
set(EXECUTABLE_OUTPUT_PATH 	/home/regen/CMake/regen_1/build)# 指定可执行文件的输出目录
```

* 取值

```cmake
${outName}
```

#### 2.文件搜索（便于源文件很多的情况）获取源文件名字

​	建议将源文件放在独立的目录下，便于获取源文件名字。

1. 使用`aux_source_directory(< dir > < variable >)`

```cmake
aux_source_directory(< dir > < variable >)
aux_source_directory(${PROJECT_SOURCE_DIR} SRC)
```

2. 使用file（用于应对很多源文件在不同目录的情况）

* 后缀搜索目录下所有源文件

```cmake
file(GLOB MAIN_SRC ${PROJECT_SOURCE_DIR}/src/*.cpp)
file(GLOB MAIN_HEAD ${PROJECT_SOURCE_DIR}/include/*.h)
```

3. 头文件与源文件分开放的处理方法

* 使用宏

```cmake
include_directories(headpath)
```





## 3.静态库和动态库

​	pause.打算在下次用到的时候补充

























































​                                



