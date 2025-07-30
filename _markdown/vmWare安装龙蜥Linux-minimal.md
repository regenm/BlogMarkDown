---
title: vmWare安装龙蜥Linux-minimal
date: 2025-06-26 06:23:20
tags: [笔记, linux, system,command,运维,Anolis]
categories: [软件技术]
---

1. 龙蜥8.9
2. shell安装mysql5.7
3. docker 安装mysql5.7

---



# 一、vmWare龙蜥8.9系统安装过程

1. **安装最小镜像**

    - 使用龙蜥8.9的最小安装镜像，保证系统精简、基础环境可控。

    ![](../images/Linux/运维/day01/2.png)

2. **确认内核版本**

    - 根据内核版本选择Linux5.x进行客制化安装。
    - ![](../images/Linux/运维/day01/5.png)

3. **进入安装环境**

    - 选择现在的iso文件，启动安装程序。
    - 选择初始软件包（基础软件，网络工具，桌面环境）。
    - 进行硬盘分区：
        - 确定分区大小和文件系统格式，保证系统运行稳定。
        - ![](../images/Linux/运维/day01/1.png)

4. 设置root密码后进行安装

------

# 二、系统配置

1. **网络配置**

    - 配置静态IP，避免使用DHCP带来的不稳定。
        -  `vim /etc/sysconfig/network-scripts/ifcfg-ens33`
        -  ![](../images/Linux/运维/day01/7.png)
    - 遇到网络配置过程卡住的问题，排查发现与NAT模式子网网段不一致有关，调整后恢复正常。
        - 这主要与vmware的设置有关，虚拟机的子网网段需要和Vmware设置中的网段一致，都则无法互联以及访问互联网(可以发现网段是72，所以在设置静态IP的时候网段也应该是72)
            - ![](../images/Linux/运维/day01/6.png)
    - 注意网络配置时需保证网段一致性，尤其虚拟机或云服务器环境中。

2. **数据库MySQL安装**

    - **Shell方式安装MySQL 5.7**

        - 下载tar包进行安装，手动解压

            - `tar -zxvf mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz`

        - 初始化数据库。

            * ```bash
                  
                // 重命名为mysql目录
                [root@xxxx local]# mv mysql-5.7.35-linux-glibc2.12-x86_64/ mysql
                // 创建mysql用户组和用户
                [root@xxxx local]# groupadd mysql
                [root@xxxx local]# useradd -r -g mysql mysql
                [root@xxxx local]# groups mysql
                mysql : mysql
                [root@xxxx local]# cd mysql/
                // 创建目录
                [root@xxxx mysql]# mkdir data
                // 赋权
                [root@xxxx mysql]# chown -R mysql:mysql ./
                // 配置my.cnf
                [root@xxxx mysql]# vim /etc/my.cnf
                
                ```

            * my.cnf

                ```bash
                [mysqld]
                bind-address=0.0.0.0
                port=3306
                user=mysql
                # 下载的目录
                basedir=/usr/local/mysql
                datadir=/usr/local/mysql/data
                socket=/tmp/mysql.sock
                log-error=/usr/local/mysql/data/mysql.err
                pid-file=/usr/local/mysql/data/mysql.pid
                #character config
                character_set_server=utf8mb4
                symbolic-links=0
                explicit_defaults_for_timestamp=true
                max_connections=512
                lower_case_table_names=1
                default-time-zone=timezone
                default-time-zone = '+8:00'
                sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
                log-bin=mysql-bin
                binlog-format=ROW
                server_id=1
                
                ```

            * 初始化并查看密码

                * ```bash
                    // 初始化数据库
                    [root@xxxx bin]# ./mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/ --user=mysql --initialize
                    [root@xxxx bin]# cd ../
                    // 查看初始密码
                    [root@xxxx bin]# cat data/mysql.err
                    
                    ```

                * 

        - 遇到宿主机访问权限问题，调整MySQL用户权限及防火墙策略解决。

            - ![](C:\Users\Regen\Desktop\工作笔记\上海城建信息科技有限公司\img\3.png)

            - 这和mysql本体的设置有关

            - 首先查看root用户权限

                - `SELECT user, host FROM mysql.user WHERE user='你的用户名';`

                - 这里已经有了权限，如果没有则需要执行

                    - ```bash
                        ALTER USER 'root'@'%' IDENTIFIED BY '密码';
                        GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
                        FLUSH PRIVILEGES;
                        
                        ```

                    - 

                - ![](../images/Linux/运维/day02/4.png)

    - **Docker方式安装MySQL 5.7**

        - 使用docker或podman拉取MySQL 5.7镜像。

            - `docker pull mysql:5.7`

        - 发现podman和docker部分命令或配置差异，注意兼容性问题。

            - 选择了删除podman

            - ```
                sudo dnf remove podman buildah
                sudo dnf install -y yum-utils device-mapper-persistent-data lvm2
                
                sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
                
                sudo dnf install -y docker-ce docker-ce-cli containerd.io
                
                ```

        - 遇到拉取镜像慢或失败，尝试配置国内镜像加速器，解决网络瓶颈。


```bash
vim /etc/docker/daemon.json
```

- 案例daemon.json



```json
{
   
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://ccr.ccs.tencentyun.com"
    ]
}
```



- **需要可靠的源，否则最后就会走官网的镜像。**

------

# 三、遇到的主要问题及解决方案

| 问题                                 | 原因及分析                          | 解决方案                                       |
| ------------------------------------ | ----------------------------------- | ---------------------------------------------- |
| 网络配置过程卡住                     | 静态IP配置错误；NAT子网网段不一致   | 调整静态IP设置，确保子网网段匹配NAT配置        |
| 宿主机无法访问MySQL容器              | MySQL权限配置不正确；防火墙阻止访问 | 修改MySQL用户权限；开放防火墙相关端口          |
| docker拉取镜像失败或速度慢           | 镜像源不稳定或权限不足              | 使用国内镜像加速器；选择官方或公开镜像源       |
| podman与docker命令不兼容             | 两者实现有差异                      | 针对podman调整命令；删除podman，优先使用docker |
| 容器名称冲突（启动已有容器名时报错） | 容器名称重复                        | 停止并删除冲突容器，或使用新的容器名称         |

# 使用到的一些命令

## 有关系统

```bash
# 查看网络配置、路由信息
ip a
ip route
cat /etc/sysconfig/network-scripts/ifcfg-ens33
cat /etc/resolv.conf
ping baidu.com
ping <域名>
nslookup <域名>
# 查看系统信息
cat /etc/os-release
cat /etc/redhat-release
uname -a
# 查看硬件信息
cat /proc/cpuinfo
free -h
lsblk
fdisk -l

# 启动防火墙
sudo firewall-cmd --reload
sudo systemctl start firewalld

```

## 有关MySql

```bash
# 解压 tar 包
tar -xvf mysql-5.7.xx-linux-glibc2.12-x86_64.tar.gz -C /opt/mysql/
mv /opt/mysql/mysql-5.7.xx /opt/mysql/mysql

# 创建数据目录和用户
mkdir -p /opt/mysql/mysql/data
useradd -r mysql
chown -R mysql:mysql /opt/mysql/mysql

# 初始化数据库
/opt/mysql/mysql/bin/mysqld --defaults-file=/opt/mysql/my.cnf --initialize --user=mysql

# 启动 MySQL
/opt/mysql/mysql/bin/mysqld_safe --defaults-file=/opt/mysql/my.cnf &
# 或
/opt/mysql/mysql/bin/mysqld --defaults-file=/opt/mysql/my.cnf --user=mysql &

# 登录 MySQL
/opt/mysql/mysql/bin/mysql -u root -p --socket=/opt/mysql/mysql/mysql.sock

# 设置 root 密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'yourpassword';

# 授权远程登录
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;
FLUSH PRIVILEGES;

```



## 有关Docker/podman

```bash
# 启动 Podman 测试
podman run hello-world

# 构建 Dockerfile 镜像（podman 兼容）
podman build -t my-mysql:5.7 .

# 运行容器（MySQL 5.7）
podman run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3305:3306 -d docker.io/mysql:5.7

# 查看容器状态
podman ps -a

# Docker 方式拉取镜像
docker pull mysql:5.7
docker run --name my-mysql57 -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3306:3306 -d mysql:5.7

# Docker 相关错误排查
systemctl start docker
docker info
# 镜像加速文件修改
vim /etc/containers/registries.conf
```



