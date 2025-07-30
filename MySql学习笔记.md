---
title: MySql学习笔记
date: 2023-10-31 12:27:48
tags: [笔记, mysql]
---

# MySQL学习笔记（8.0）

​		一切只因花几十块买了一年的云服务器。刚好目前需要用 用 JDBC ，于是就把数据库部署到了服务器上面。然而这其中却遇到了难以想象的困难（后来才发现没有必要这么弄，且一般也不这么弄，安全性大打折扣）。

## 1.部署环境以及初始化配置

* Ubuntu

### 1.下载安装，配置密码

以root用户登录之后：

```shell
apt update
apt upgrade	#不解释
apt install mysql-server -y	#下载
```

**此时你可以直接进入mysql**

```sql
mysql

mysql> use mysql;
mysql> apt install mysql-server -y #（将密码置为空）
mysql> quit;
#	然后重启mysql，再次进入
systemstl restart mysql
mysql

mysql> use mysql;
mysql> ALTER USER 'root'@'localhost' (如果不用远程链接则为'localhost')IDENTIFIED WITH mysql_native_password BY '新密码';
mysql> quit #修改完成
```

* 开启远程链接功能

```
mysql
mysql> use mysql;
mysql> apt install mysql-server -y #修改访问权限，默认只能本地访问。
```

* 修改MySQL配置文件

```shell
vim /etc/mysql/mysql.conf.d/mysqld.cnf
将[mysqld]下面的 bind-address ***以及mysqlx-bind-address ****注释
同时取消注释
port
user
socket
server-id
重启测试即可
```

## 2. 开始正式学习mysql

