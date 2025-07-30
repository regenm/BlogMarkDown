---
title: jira、confluence、bitbucket、基于crowd实现sso的案例
date: 2025-07-21 16:05:56
tags: [笔记,运维,部署,jira,confluence,bitbucket,crowd,sso]
categories: [软件技术]
---

# jira、confluence、bitbucket、基于crowd实现sso的案例

## 案例简介

> 这是公司的旧生产环境备份案例：
>
> **版本信息**
>
> * jira:7.13
> * confluence:6.10.0
> * bitbucket:7.0.0
> * crowd:3.2.1
> * mysql:5.7
>
> ---
>
> 1. jira位于一台服务器`192.168.99.253`数据库也在此服务器，端口为8080
> 2. crowd和confluence位于**同一台服务器**`192.168.99.252`它们的数据库也在此服务器，crowd端口为8095，confluence端口为8090
> 3. bitbucket位于一台服务9器`192.168.99.251`数据库也在此服务器，端口为7990
>
> 整个项目是旧的项目迁移而来。
>
> 参考文章[jira、confluence、bitbucket、基于crowd实现sso | Regen](https://regenm.github.io/2025/07/18/jira、confluence、bitbucket、基于crowd实现sso/)
>
> ### **思路**
>
> 因为是新的环境，而且基础信息也已经全部丢失，例如各种账号密码，所以思路是：
>
> 1. 先满足每个软件能够正常使用内部账户登录使用
> 2. 再连接crowd进行测试
> 3. 最后实现SSO

图：

![image-20250721161435380](../images/Linux/sso/image-20250721161435380.png)

* 操作目标
* 实现三平台（jira、confluence、bitbucket）的单点登录。

---

## 1. 准备工作：

### 1. 找到各个软件的安装目录和数据目录

#### jira

1. 安装目录

![image-20250721165424260](../images/Linux/sso/image-20250721165424260.png)

2. 数据目录

![image-20250721165441962](../images/Linux/sso/image-20250721165441962.png)

#### confluence

1. 安装目录

![image-20250721165844897](../images/Linux/sso/image-20250721165844897.png)

2. 数据目录

![image-20250721165954165](../images/Linux/sso/image-20250721165954165.png)



#### bibucket

1. 安装目录

![image-20250721170035979](../images/Linux/sso/image-20250721170035979.png)

2. 数据目录

![image-20250721170015797](../images/Linux/sso/image-20250721170015797.png)



#### crowd

1. 安装目录

![image-20250721165757293](../images/Linux/sso/image-20250721165757293.png)

2. 数据目录

![image-20250721165811334](../images/Linux/sso/image-20250721165811334.png)

### 2. 停掉还在运行的服务

到对应的bin目录下运行对应脚本即可。

---

## 2. 使用内网DNS解析

服务器为Winserver2016，安装DNS服务。

### 1. 搭建与配置DNS服务器

#### 1. 搭建DNS服务器

1. 打开服务器管理器

![image-20250722161735278](../images/Linux/sso/image-20250722161735278.png)

2. 点击**添加角色和功能**

![image-20250722162042742](../images/Linux/sso/image-20250722162042742.png)

3. 点击下一步-基于角色或基于功能的安装-下一步，选择主机，选择DNS服务器

![image-20250722162253226](../images/Linux/sso/image-20250722162253226.png)

4. 一路下一步最后安装

![image-20250722162326485](../images/Linux/sso/image-20250722162326485.png)

​	可能会重启几次

#### 2. 配置DNS解析

1. 进入DNS管理器

![image-20250722162527041](../images/Linux/sso/image-20250722162527041.png)

2. 选择正向查找区域，新建区域

![image-20250722162647154](../images/Linux/sso/image-20250722162647154.png)

3. 选择主要区域并下一步

![image-20250722162728785](../images/Linux/sso/image-20250722162728785.png)

4. 输入域名，下一步

![image-20250722162809338](../images/Linux/sso/image-20250722162809338.png)

5. 一路下一步，右键新建的域名选择新建主机(A或AAAA)

![image-20250722162937500](../images/Linux/sso/image-20250722162937500.png)

6. 输入二级域名以及主机名，重复操作即可。

![image-20250722163102969](../images/Linux/sso/image-20250722163102969.png)

7. 最后显示

![image-20250722163210119](../images/Linux/sso/image-20250722163210119.png)

8. 至此已添加所有需要使用到的解析



### 2. 客户端配置DNS服务器

#### 1. windows客户端

1. 控制面板-网络和共享中心-选择联网的以太网连接

![image-20250722163419857](../images/Linux/sso/image-20250722163419857.png)

2. 点击属性后双击IPv4

![image-20250722163445352](../images/Linux/sso/image-20250722163445352.png)

3. 选择DNS服务器并输入

![image-20250722163610973](../images/Linux/sso/image-20250722163610973.png)

4. 至此已添加好DNS解析服务器，。
5. 测试一下是否是正确的IP。

​	第一次是未添加解析之前的结果，后面的为添加解析后的结果。至于为什么两次结果不一样，与DNS解析过程有关，最终结果是我们内网的解析结果即可。

![image-20250722163732245](../images/Linux/sso/image-20250722163732245.png)

### 2. Linux客户端或服务端

#### 1. 临时修改

```bash
sudo bash -c 'echo "nameserver 192.168.99.250" > /etc/resolv.conf'
```

#### 2. 永久修改

1. 首先查看上网用的网卡

```
ip a
```

![image-20250722164112282](../images/Linux/sso/image-20250722164112282.png)

2. 这里可以发现我的是ens33，然后去网卡的修改配置文件。

```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

增加DNS服务器IP地址即可

```
DNS1 192.168.99.205
```

![image-20250722164255588](../images/Linux/sso/image-20250722164255588.png)

3. 重启网卡即可

```bash
 systemctl restart NetworkManager
```

4. 测试解析

```bash
ping jira.suitbim.com
```

![image-20250722164633137](../images/Linux/sso/image-20250722164633137.png)

没有问题

4. 至此已配置好DNS服务器

    

## 3. 配置为内部用户正常登录并取消crowd登录



### 1. 修改各个软件的数据库root密码

> 因为后续需要改各个数据库中的管理员密码，所以这里改掉root便于后续查询修改。
>
> 通过各类手段确定数据库信息：
>
> ```bash
> mysql --version
> ```
>
> 如果没直接显示则可能docker容器中运行的。
>
> 汇总一下信息：
>
> 192.168.99.251	mysql  Ver 14.14 **Distrib 5.7.24**, for Linux (x86_64) using  EditLine wrapper
>
> 192.168.99.252	mysql  Ver 15.1 **Distrib 5.5.60-MariaDB**, for Linux (x86_64) using readline 5.1
>
> 192.168.99.253	mysql  Ver 14.14 **Distrib 5.7.20**, for Linux (x86_64) using  EditLine wrapper

#### 修改数据库流程

1.  停止数据库服务

```bash
sudo systemctl stop mysql
# 或
sudo systemctl stop mariadb
```



2. “跳过授权表”方式启动（安全模式

```bash
mysqld_safe --skip-grant-tables &
```

3. root 用户无密码登录

```bash
mysql -u root
```

4. 修改 root 密码

* MySQL 5.7+ / 8.0 / MariaDB 新版本

```
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
```

* 旧版本

```bash
FLUSH PRIVILEGES;
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('新密码');

```



5. 退出并重启数据库

```bash
exit

sudo systemctl stop mysql
# 或
sudo systemctl stop mariadb

sudo systemctl start mysql
# 或
sudo systemctl start mariadb

```



6. 用新密码验证

```bash
mysql -u root -p
```

### 2. 查看以及确认各个软件配置的数据库信息

#### jira

```
vim /home/atlassian/application-data/jira/dbconfig.xml
```

![image-20250721171451445](../images/Linux/sso/image-20250721171451445.png)

**经数据库连接测试后无问题**

#### confluence

```
vim /home/confluence_data/confluence.cfg.xml
```

![image-20250721171323721](../images/Linux/sso/image-20250721171323721.png)

**经数据库连接测试后无问题**

#### bitbucket

```bash
vim /data/atlassian/application-data/bitbucket/shared/bitbucket.properties
```

![image-20250721171616776](../images/Linux/sso/image-20250721171616776.png)

**经数据库连接测试后无问题**

这里的`plugin.auth-crowd.sso.enabled=true`无影响	

#### crowd

```
vim /var/crowd-home/shared/crowd.cfg.xml
```

![image-20250721170204578](../images/Linux/sso/image-20250721170204578.png)

**经数据库连接测试后无问题**

### 3. 查看以及修改各个软件的SSO配置

#### jira

##### 1. 修改认证配置文件`seraph-config.xml`

```bash
vim /home/atlassian/jira/atlassian-jira/WEB-INF/classes/seraph-config.xml
```

找到

```xml
<!-- CROWD:START - If enabling Crowd SSO integration uncomment the following SSOSeraphAuthenticator and comment out the JiraSeraphAuthenticator below -->
    
    <authenticator class="com.atlassian.jira.security.login.SSOSeraphAuthenticator"/>
   
    <!-- CROWD:END -->

    <!-- CROWD:START - The authenticator below here will need to be commented out for Crowd SSO integration -->
	<!--
    <authenticator class="com.atlassian.jira.security.login.JiraSeraphAuthenticator"/>
-->
<!-- CROWD:END -->
```

改为

```xml
<!-- CROWD:START - If enabling Crowd SSO integration uncomment the following SSOSeraphAuthenticator and comment out the JiraSeraphAuthenticator below -->
    <!--
    <authenticator class="com.atlassian.jira.security.login.SSOSeraphAuthenticator"/>
   -->
    <!-- CROWD:END -->

    <!-- CROWD:START - The authenticator below here will need to be commented out for Crowd SSO integration -->
	
   <authenticator class="com.atlassian.jira.security.login.JiraSeraphAuthenticator"/>
<!-- CROWD:END -->
```



##### 3. 重启jira

```bash
cd /home/atlassian/jira/bin
./stop-jira.sh
./start-jira.sh
```

#### confluence

##### 1. 修改认证配置文件`seraph-config.xml`

```bash
vim /opt/atlassian/confluence/confluence/WEB-INF/classes/seraph-config.xml
```

> 这里已经修改好。

找到

```xml
    <!-- Default Confluence authenticator, which uses the configured user management for authentication. -->
       <!--  
    <authenticator class="com.atlassian.confluence.user.ConfluenceAuthenticator"/>
     -->
    <!-- Custom authenticators appear below. To enable one of them, comment out the default authenticator above and uncomment the one below. -->

    <!-- Authenticator with support for Crowd single-sign on (SSO). -->

    <authenticator class="com.atlassian.confluence.user.ConfluenceCrowdSSOAuthenticator"/>

```

改为

```xml
    <!-- Default Confluence authenticator, which uses the configured user management for authentication. -->
         
    <authenticator class="com.atlassian.confluence.user.ConfluenceAuthenticator"/>
     
    <!-- Custom authenticators appear below. To enable one of them, comment out the default authenticator above and uncomment the one below. -->

    <!-- Authenticator with support for Crowd single-sign on (SSO). -->
<!--
    <authenticator class="com.atlassian.confluence.user.ConfluenceCrowdSSOAuthenticator"/>
--> 
```



##### 3. 重启confluence

```bash
cd /opt/atlassian/confluence/bin
./stop-confluence.sh
./start-confluence.sh
```

#### bitbucket

##### 1. 修改`bitbucket.properties`

在数据目录中找到`bitbucket.properties`，一般在`/var`下，也可能被移动或修改，可通过find查找

```bash
vim  /data/atlassian/application-data/bitbucket/shared/bitbucket.properties
```

增加一行`plugin.auth-crowd.sso.enabled=false`

```bash
#>*******************************************************
#> Migrated to database at jdbc:mysql://192.168.99.114:3306/bitbucket?characterEncoding=utf8&useUnicode=true
#> Updated on 2025-07-10T14:31:05.562+08:00
#>*******************************************************
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://192.168.99.114:3306/bitbucket?characterEncoding=utf8&useUnicode=true
jdbc.user=bitbucket
jdbc.password=bitbucket

# 增加以下项目
plugin.auth-crowd.sso.enabled=false
```

##### 2. 重启

```bash
cd /opt/atlassian/bitbucket/7.0.0/bin
./stop-bitbucket.sh  
./start-bitbucket.sh  
```

### 4. 使用初始管理员登录

改密码方式在另一篇中有介绍[jira、confluence、bitbucket、基于crowd实现sso | Regen](https://regenm.github.io/2025/07/18/jira、confluence、bitbucket、基于crowd实现sso/)

登录成功则可进行下一步配置。

## 4. 各个web平台配置SSO

1. 在各个应用内设置好允许访问的IP和域名

    这里只放jira的配置

    ![image-20250718111839304](../images/Linux/sso/image-20250718111839304.png)

### jira 集成crowd

1. 测试服务器连接

![image-20250718112040564](../images/Linux/sso/image-20250718112040564.png)

2. 测试用户

![image-20250718112135649](../images/Linux/sso/image-20250718112135649.png)

![image-20250718112208967](../images/Linux/sso/image-20250718112208967.png)

### confluence集成crowd

​	与jira集成crowd操作基本一致

### bitbucket集成crowd

​	与jira集成crowd操作基本一致

### Crowd的SSO设置

> 这一步设置了能够通过SSO登录的二级域名，子域名通过该域名来实现SSO

1. 进入crowd general 设置

![image-20250721145301659](../images/Linux/sso/image-20250721145301659.png)

2. 设置子域名、cookie名字以及base url，确保都是域名而不是IP。

![image-20250721145619726](../images/Linux/sso/image-20250721145619726.png)



## 5. 实现SSO

> 在我使用的版本中，如果允许sso登录，则会停用jira、confluence、bitbucket的管理员账户，因此在实现SSO之前建议给Crowd中的某个用户增加所有的权限便于管理。否则将失去管理员账号。如果忘记添加可以看文末解决方式
>
> 配置方法有两种，一种是直接在各个平台为该用户增加最大权限，一种是在各个平台增加一个最大权限用户组，并将该用户添加到该组。
>
> * 第一种方式（直接在各个平台为该用户增加最大权限）实现方式：
>
>     这里仅以jira为例，其余过程基本一致。
>
> 1. 打开用户管理
> 2. 搜索crowd用户并且添加到对应权限组
>
> * 第二种方式（最大权限用户组，添加该用户）实现方式：

### jira实现SSO的配置过程

#### 1. 修改认证配置文件`seraph-config.xml`

```bash
vim /home/atlassian/jira/atlassian-jira/WEB-INF/classes/seraph-config.xml
```

找到

```xml
<!-- CROWD:START - If enabling Crowd SSO integration uncomment the following SSOSeraphAuthenticator and comment out the JiraSeraphAuthenticator below -->
    <!-- 
    <authenticator class="com.atlassian.jira.security.login.SSOSeraphAuthenticator"/>
    -->
    <!-- CROWD:END -->

    <!-- CROWD:START - The authenticator below here will need to be commented out for Crowd SSO integration -->

    <authenticator class="com.atlassian.jira.security.login.JiraSeraphAuthenticator"/>

<!-- CROWD:END -->
```

将以下内容取消注释

```xml
    <!-- 
    <authenticator class="com.atlassian.jira.security.login.SSOSeraphAuthenticator"/>
    -->
```

改为

```xml
    <authenticator class="com.atlassian.jira.security.login.SSOSeraphAuthenticator"/>
```

并注释

```xml
        <!-- 
    <authenticator class="com.atlassian.jira.security.login.JiraSeraphAuthenticator"/>
        -->
```

#### 2. 重启jira

```bash
cd /home/atlassian/jira/bin
./stop-jira.sh
./start-jira.sh
```

### confluence实现SSO的配置过程、与jira类似

#### 1. 修改认证配置文件`seraph-config.xml`

```bash
vim /opt/atlassian/confluence/confluence/WEB-INF/classes/seraph-config.xml
```

> 这里已经修改好。

找到

```xml
    <!-- Default Confluence authenticator, which uses the configured user management for authentication. -->
        <!--    
    <authenticator class="com.atlassian.confluence.user.ConfluenceAuthenticator"/>
        -->
    <!-- Custom authenticators appear below. To enable one of them, comment out the default authenticator above and uncomment the one below. -->

    <!-- Authenticator with support for Crowd single-sign on (SSO). -->
    <authenticator class="com.atlassian.confluence.user.ConfluenceCrowdSSOAuthenticator"/>

```

将以下内容取消注释

```xml
    <!-- 
     <authenticator class="com.atlassian.confluence.user.ConfluenceCrowdSSOAuthenticator"/>
    -->
```

改为

```xml
    <authenticator class="com.atlassian.confluence.user.ConfluenceCrowdSSOAuthenticator"/>
```

并注释

```xml
        <!-- 
   <authenticator class="com.atlassian.confluence.user.ConfluenceAuthenticator"/>
        -->
```

#### 2. 重启confluence

```bash
cd /opt/atlassian/confluence/bin
./stop-confluence.sh
./start-confluence.sh
```

### Bitbucket实现SSO的配置过程

#### 1. 修改`bitbucket.properties`

在数据目录中找到`bitbucket.properties`，一般在`/var`下，也可能被移动或修改，可通过find查找

```bash
vim  /data/atlassian/application-data/bitbucket/shared/bitbucket.properties
```

增加一行`plugin.auth-crowd.sso.enabled=true`

```bash
#>*******************************************************
#> Migrated to database at jdbc:mysql://192.168.99.114:3306/bitbucket?characterEncoding=utf8&useUnicode=true
#> Updated on 2025-07-10T14:31:05.562+08:00
#>*******************************************************
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://192.168.99.114:3306/bitbucket?characterEncoding=utf8&useUnicode=true
jdbc.user=bitbucket
jdbc.password=bitbucket

# 增加以下项目
plugin.auth-crowd.sso.enabled=true
```

#### 2. 重启

```bash
cd /opt/atlassian/bitbucket/7.0.0/bin
./stop-bitbucket.sh  
./start-bitbucket.sh  
```

## 6. 验证SSO

* 分别使用具有admin权限的用户和普通用户登录

### 1. 测试具有admin权限的用户

1. 退出已经登录的所有账号

![image-20250722134654584](../images/Linux/sso/image-20250722134654584.png)

2. 登录具有admin权限的用户

![image-20250722134734142](../images/Linux/sso/image-20250722134734142.png)

3. 测试confluence、bitbucket

![image-20250722134814486](../images/Linux/sso/image-20250722134814486.png)![image-20250722135011804](../images/Linux/sso/image-20250722135011804.png)

至此已实现了基于Crowd的jira、confluence、bitbucket三平台SSO。





## 问题解决

见[jira、confluence、bitbucket、基于crowd实现sso | Regen](https://regenm.github.io/2025/07/18/jira、confluence、bitbucket、基于crowd实现sso/)
