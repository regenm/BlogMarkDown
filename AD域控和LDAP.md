---
title: AD域控和LDAP
date: 2025-06-30 06:31:59
tags: [笔记, linux, system,command,运维,WinServer,AD,LDAP]
categories: [软件技术]
---

##  LDAP（Lightweight Directory Access Protocol）

LDAP 是一种 **目录服务协议**，用于访问和管理目录信息（比如用户、组织、设备等）。它是一个跨平台的协议，可以被很多系统支持，如：

- OpenLDAP（Linux 中常用的开源 LDAP 实现）
- Active Directory（微软的目录服务就是基于 LDAP 的）
- Apache Directory、389 DS 等

LDAP 的用途主要是：

- 用户认证（如登录系统）
- 用户信息查询（如获取邮箱、电话等）
- 应用统一认证（SSO）

LDAP 中的典型结构是类似于树状目录的，如：

```
bash复制编辑dc=example,dc=com
 ├── ou=users
 │    ├── cn=alice
 │    └── cn=bob
 └── ou=groups
```

------

###  什么是 AD 域控（Active Directory Domain Controller）

Active Directory（简称 AD）是微软开发的一套 **基于 LDAP 的目录服务系统**，它不仅实现了 LDAP 协议，还扩展了许多功能：

- 用户、组、计算机、权限的集中管理
- 集中身份认证和授权（基于 Kerberos 和 LDAP）
- 集成 DNS、组策略（GPO）、证书等服务
- 控制 Windows 网络的“域”环境

一个 **AD 域控（Domain Controller）** 是一台负责管理 AD 目录的服务器，负责处理：

- 用户登录请求（如验证用户名密码）
- 域内设备管理
- 安全策略分发

## **AD 域控的核心价值是“集中管理身份与设备”**

# 

