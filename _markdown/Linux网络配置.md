---
title: Linux网络配置
date: 2025-07-14 17:50:49
tags: [笔记, linux, system,command,运维]
categories: [软件技术]
---

通用命令：

| 命令         | 用途                     | 常用示例                   | 解释                                     |
| ------------ | ------------------------ | -------------------------- | ---------------------------------------- |
| `ip a`       | 查看网络接口和 IP 地址   | `ip a`                     | 类似 `ifconfig`，显示所有网卡状态        |
| `ip link`    | 查看或管理网卡           | `ip link set ens33 up`     | 激活或关闭网卡                           |
| `ping`       | 测试网络连通性           | `ping www.baidu.com`       | 连续 ping 测试                           |
| `traceroute` | 路由跟踪                 | `traceroute www.baidu.com` | 显示到目标的跳数路径                     |
| `netstat`    | 查看网络连接             | `netstat -tnlp`            | 查看监听端口与进程（需安装 `net-tools`） |
| `ss`         | 更快查看连接状态         | `ss -tuln`                 | 列出 TCP/UDP 监听端口                    |
| `route`      | 查看/配置路由            | `route -n`                 | 查看路由表（老工具，常用）               |
| `ip route`   | 查看路由表               | `ip route`                 | 建议用替代 `route`                       |
| `nmcli`      | 网络管理工具（命令行）   | `nmcli dev show`           | 查看设备详细信息                         |
| `nmtui`      | 网络管理图形界面（终端） | `nmtui`                    | 方便配置静态 IP                          |
| `ethtool`    | 查看/修改网卡参数        | `ethtool ens33`            | 查看网卡速率等信息                       |
| `dig`        | DNS 查询工具             | `dig www.baidu.com`        | 查看域名解析结果                         |
| `nslookup`   | DNS 查询工具             | `nslookup www.baidu.com`   | 类似 dig                                 |

#  **一、文件位置**

通常在：

```
/etc/sysconfig/network-scripts/ifcfg-<网卡名>
```

比如：

```
/etc/sysconfig/network-scripts/ifcfg-ens33
```

------

# **二、典型静态 IP 配置内容**

```
TYPE=Ethernet             # 网卡类型（固定写 Ethernet）
BOOTPROTO=static          # 启动时使用 static（静态 IP）
NAME=ens33                # 连接名
DEVICE=ens33              # 网卡名
ONBOOT=yes                # 开机自动启用
IPADDR=192.168.1.100      # 静态 IP 地址
NETMASK=255.255.255.0     # 子网掩码
GATEWAY=192.168.1.1       # 默认网关
DNS1=8.8.8.8              # DNS 服务器
```

------

# **三、修改后如何生效**

修改完后：

```
systemctl restart NetworkManager
```

或：

```
nmcli connection reload
```

查看是否生效：

```
ip a
ping www.baidu.com
```

------

# **四、常用字段解释**

| 字段      | 作用                              |
| --------- | --------------------------------- |
| TYPE      | 设备类型（通常写 Ethernet）       |
| BOOTPROTO | 启动时 IP 分配方式：static / dhcp |
| NAME      | 连接名                            |
| DEVICE    | 网卡名（如 ens33）                |
| ONBOOT    | 开机是否自动启动                  |
| IPADDR    | 静态 IP                           |
| NETMASK   | 子网掩码                          |
| GATEWAY   | 默认网关                          |
| DNS1/DNS2 | DNS 服务器                        |



------

#  **五、动态 IP（DHCP）配置**

只需：

```
BOOTPROTO=dhcp
ONBOOT=yes
```

删除或注释掉 IPADDR / NETMASK / GATEWAY 等行。

------
