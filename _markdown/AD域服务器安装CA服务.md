---
title: AD域服务器安装CA服务
date: 2025-07-04 16:26:34
tags: [笔记, linux, system,command,运维,WinServer,AD,LDAP,self-password-service]
categories: [软件技术]
---

> 操作系统：WinServer 2019

# **AD域服务器安装CA服务**

## 1. 点击管理-添加角色和功能

![image-20250704165642504](../images/Linux/运维/AD-CA/image-20250704165642504.png)

## 2. 点击下一页

![image-20250704170543964](../images/Linux/运维/AD-CA/image-20250704170543964.png)

## 3. 点击  基于角色或基于功能的安装

![image-20250704170636394](../images/Linux/运维/AD-CA/image-20250704170636394.png)

## 4. 点击 从服务器池中选择服务器-选择要安装的主机-点击下一页

![image-20250704170918182](../images/Linux/运维/AD-CA/image-20250704170918182.png)

## 5. 点击 Active Directory 证书服务-Web服务器、网络策略和访问网络服务-下一页

![image-20250704172212266](../images/Linux/运维/AD-CA/image-20250704172212266.png)

## 6. 点击下一页

![image-20250704172252061](H:\git_project\RegenBlogs\hexo\blog\source\images\Linux\运维\AD-CA\image-20250704172252061.png)

## 7. 选择 证书颁发机构、联机响应程序、网络设备注册服务、证书颁发机构web注册

![image-20250704172552008](H:\git_project\RegenBlogs\hexo\blog\source\images\Linux\运维\AD-CA\image-20250704172552008.png)

## 8. 点击安装

![image-20250704172707977](../images/Linux/运维/AD-CA/image-20250704172707977.png)

## 9. 可能需要重新启动，可以选择合适的时间重启。



# 配置CA服务器

## 1. 点击通知，此时应该湖出现感叹号

![image-20250704173007725](../images/Linux/运维/AD-CA/image-20250704173007725.png)

## 2. 点击 配置目标服务器上的Active Directory证书服务

![image-20250704173132719](../images/Linux/运维/AD-CA/image-20250704173132719.png)

## 3. 点击下一页

![image-20250704173206560](../images/Linux/运维/AD-CA/image-20250704173206560.png)

## 4. 点击证书颁发机构、证书颁发机构Web注册

![image-20250704173304085](../images/Linux/运维/AD-CA/image-20250704173304085.png)

## 5. 选择企业CA-下一页

![image-20250704173352625](../images/Linux/运维/AD-CA/image-20250704173352625.png)

## 6. 选择根CA -下一页



![image-20250704173436419](C:\Users\Regen\AppData\Roaming\Typora\typora-user-images\image-20250704173436419.png)

## 7. 创建新的私钥-下一页

![image-20250704173507035](../images/Linux/运维/AD-CA/image-20250704173507035.png)

## 8. 创建私钥，建议选择稍微高一点的策略，例如SHA-512  长度4096等。防止在ldap连接时遇到证书太过简单而拒绝的情况。

![image-20250704173718799](../images/Linux/运维/AD-CA/image-20250704173718799.png)

## 9. 点击下一页-下一页-下一页

![image-20250704173747190](../images/Linux/运维/AD-CA/image-20250704173747190.png)

![image-20250704173816862](../images/Linux/运维/AD-CA/image-20250704173816862.png)

![image-20250704173825386](../images/Linux/运维/AD-CA/image-20250704173825386.png)

## 10 . 点击配置

![image-20250704173849727](../images/Linux/运维/AD-CA/image-20250704173849727.png)

## 11. 点击下一页

![image-20250704173945896](../images/Linux/运维/AD-CA/image-20250704173945896.png)

## 12. 至此已结束CA服务角色的的配置

​	可以通过以下命令可以导出根证书。

```
certutil -ca.cert root.cer
```

