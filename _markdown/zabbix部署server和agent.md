---
title: zabbix部署server和agent
date: 2025-07-29 14:11:38
tags: [笔记, linux,运维,zabbix,docker,硬件检测]
---

# zabbix部署server和agent

> Zabbix 是一个开源的企业级分布式监控解决方案，广泛应用于监控 IT 基础设施（如服务器、网络设备、虚拟机等）和应用程序。它支持多种监控方式（如 agent、SNMP、IPMI、SSH 等），提供实时数据采集、告警通知、趋势分析和历史存储。Zabbix 拥有强大的可扩展性和灵活的图形化 Web 界面，能够通过 Zabbix Server、Proxy 和 Agent 实现分布式部署，适用于大规模环境。其开源特性和丰富的模板、API 使其在中小企业到超大规模企业中都能得到广泛应用。

> * 部署环境
>
> 系统：Anolis 8.10
>
> 软件：
>
> 1. Docker 28.3
> 2. Docker镜像：
>     1. mysql                           8.0                 304929b30183   5 days ago    781MB
>     2. zabbix/zabbix-web-nginx-mysql   alpine-6.2-latest   09a4eb616c94   2 years ago   237MB
>     3. zabbix/zabbix-server-mysql      6.2-alpine-latest   69c2e207de22   2 years ago   86.5MB
>     4. zabbix/zabbix-java-gateway      alpine-6.2-latest   8f70989cf9b1   2 years ago   91.1MB
>
> ![image-20250729163727520](../images/linux/运维/zabbix/image-20250729163727520.png)





## docker部署server端

### 1. 准备操作



### 2. docker部署mysql

​	选择将数据持久化存储到当前工作目录的`mysql_data`目录下

```
docker run --name mysql-server -t \
>   -v $(pwd)/mysql_data:/var/lib/mysql \
>   -e MYSQL_DATABASE="zabbix" \
>   -e MYSQL_USER="zabbix" \
>   -e MYSQL_PASSWORD="zabbix_pwd" \
>   -e MYSQL_ROOT_PASSWORD="123456" \
>   --restart=unless-stopped \
>   -p 3306:3306 \
>   -d mysql:8.0 \
>   --character-set-server=utf8 --collation-server=utf8_bin \
>   --default-authentication-plugin=mysql_native_password
```





### 3. docker部署zabbix-java-gateway

> 用于监控Java程序的JVM状态

```bash
docker run --name zabbix-java-gateway -t \
   --restart=unless-stopped \
   -d zabbix/zabbix-java-gateway:alpine-6.2-latest
```

### 4. docker部署zabbix-server

准备好挂载配置文件的目录和配置文件。

![image-20250730092559134](../images/linux/运维/zabbix/image-20250730092559134.png)

```bash
docker run --name zabbix-server-mysql -t \
    -v /root/zabbix-server/zabbix_server:/etc/zabbix \  
    -e DB_SERVER_HOST="mysql-server"  \ 
    -e MYSQL_DATABASE="zabbix"  \
    -e MYSQL_USER="zabbix"   \
    -e MYSQL_PASSWORD="zabbix_pwd"   \
    -e MYSQL_ROOT_PASSWORD="123456"   \
    -e ZBX_JAVAGATEWAY="zabbix-java-gateway"   \
    --link mysql-server:mysql   \
    --link zabbix-java-gateway:zabbix-java-gateway  \
    --restart=unless-stopped   \
    -p 10051:10051   \
    -d zabbix/zabbix-server-mysql:6.2-alpine-latest
```



### 5.docker部署zabbix-web界面



```bash
docker run --name zabbix-web-nginx-mysql -t \
   -e PHP_TZ="Asia/Shanghai" \
   -e ZBX_SERVER_HOST="zabbix-server-mysql" \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="123456" \
      --link mysql-server:mysql \
      --link zabbix-server-mysql:zabbix-server \
      -p 80:8080 \
      --restart unless-stopped \
      -d zabbix/zabbix-web-nginx-mysql:alpine-6.2-latest
```

至此server端的组件均已安装

访问 `IP:80`即可访问到zabbix服务端，账号密码为`Admin/Zabbix`

## 服务器部署agent

使用docker部署agent在不做其他配置的情况下只会监测容器本身。因此这里使用直接安装的方式部署zabbix-agent

### 使用包管理器安装

#### 1. 添加与系统版本一致的仓库

```bash
dnf install -y https://repo.zabbix.com/zabbix/7.0/rhel/8/x86_64/zabbix-release-7.0-1.el8.noarch.rpm
```

#### 2. 清理并刷新缓存

```bash
dnf clean all
dnf makecache

```

#### 3. 安装

```bash
dnf install -y zabbix-agent
```







### 离线包安装

#### 1. 准备离线包

以下是安装`zabbix7.0`所需要的所有包。主包为`zabbix7.0-7.0.16-1.el8.x86_64.rpm`

> 仅仅适用于Anolis8.10环境下。

> ![image-20250730143332854](../images/linux/运维/zabbix/image-20250730143332854.png)

#### 2. 安装

```bash
yum localinstall *.rpm
```



#### 3. 配置agent并启用

```bash
vim /etc/zabbix/zabbix_agentd.conf
```

两处地方修改为zabbix-server的IP，Hostname改为合适的名字

![image-20250730111245757](../images/linux/运维/zabbix/image-20250730111245757.png)

![image-20250730111256706](../images/linux/运维/zabbix/image-20250730111256706.png)



保存退出后重启服务

```
systemctl restart zabbix-agent.service
```

确认正常运行中

![image-20250730111426747](../images/linux/运维/zabbix/image-20250730111426747.png)

#### 4. server端连接agent

登录server端后点击：

![image-20250730110022568](../images/linux/运维/zabbix/image-20250730110022568.png)

![image-20250730110158704](../images/linux/运维/zabbix/image-20250730110158704.png)

![image-20250730111457525](../images/linux/运维/zabbix/image-20250730111457525.png)

等待连接变为绿色即可

![image-20250730111517028](../images/linux/运维/zabbix/image-20250730111517028.png)

## 附：

### 离线包下载方法

#### 导入 Zabbix 官方仓库

```
dnf install -y https://repo.zabbix.com/zabbix/7.0/rhel/8/x86_64/zabbix-release-7.0-1.el8.noarch.rpm
```

![image-20250730143949900](../images/linux/运维/zabbix/image-20250730143949900.png)

#### 下载到本地

```
dnf download --resolve --destdir=./ zabbix-agent
```

![image-20250730143934759](../images/linux/运维/zabbix/image-20250730143934759.png)
