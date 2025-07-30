---
title: Anolis8和CentOS7的日志对比
date: 2025-07-30 16:40:05
tags: [笔记,运维,日志,centos,anolis]
categories: [软件技术]
---

# Anolis8和CentOS7的日志对比



| 功能/用途            | CentOS 7 | Anolis 8.10                                       | 差异解析                                                     | 常用查看方式（CentOS 7）             | 常用查看方式（Anolis 8）             |
| -------------------- | -------- | ------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------ | ------------------------------------ |
| 包管理               | yum.log  | dnf.log, dnf.rpm.log, dnf.librepo.log, hawkey.log | Anolis 8.10 默认用 dnf；hawkey 是 dnf 的依赖库               | tail -n 50 /var/log/yum.log          | tail -n 50 /var/log/dnf.log          |
| 系统消息总览         | messages | 无 messages                                       | ==Anolis 默认依赖 systemd journal，不再写入 /var/log/messages== | tail -f /var/log/messages            | journalctl -xe                       |
| 认证/安全            | secure   | 无 secure                                         | ==同样默认在 journal 中查看，如 journalctl -u sshd==         | tail -f /var/log/secure              | journalctl -u sshd                   |
| 计划任务             | cron     | 无 cron                                           | 同样转到 journal                                             | tail -f /var/log/cron                | journalctl -u crond                  |
| 系统引导             | boot.log | 无 boot.log                                       | ==用 journalctl -b==                                         | cat /var/log/boot.log                | journalctl -b                        |
| dmesg                | dmesg    | 无                                                | ==dmesg 内容也在 journal 中可查==                            | cat /var/log/dmesg 或 dmesg          | dmesg 或 journalctl -k               |
| rhsm（红帽订阅管理） | rhsm     | 无                                                | ==Anolis 是开源版本，无 RHSM==                               | tail -f /var/log/rhsm/rhsm.log       | 无（默认无此目录）                   |
| spooler, tallylog    | 有       | 无                                                | ==不常用，取决于安装包==                                     | lastlog、faillog 等                  | 无                                   |
| 服务日志             | 有       | 有                                                | 安装的具体服务产生的日志                                     | tail -f /var/log/httpd/access_log 等 | tail -f /var/log/httpd/access_log 等 |
| btmp                 | btmp     | btmp + 历史文件（btmp-20250701）                  | 系统轮转后多了历史文件                                       | lastb                                | lastb                                |
| private              | 无       | private                                           | 可能存放私有敏感日志（取决于服务）                           | 无                                   | 根据服务查看                         |
| sssd                 | 无       | sssd                                              | 如果启用了 SSSD 服务，会有认证相关日志                       | 无                                   | journalctl -u sssd                   |



# 重点关注对象



## **一、系统层面**

| 日志                                         | 说明                               | 用途                       |
| -------------------------------------------- | ---------------------------------- | -------------------------- |
| `/var/log/messages`（或 `journalctl -xe`）   | 系统核心消息、服务异常、内核警告等 | 系统健康、故障排查第一入口 |
| `/var/log/secure`（或 `journalctl -u sshd`） | SSH 登录、sudo、认证失败等         | 审计安全事件、防止暴力破解 |
| `/var/log/boot.log` / `journalctl -b`        | 系统启动过程                       | 排查开机慢、硬件驱动问题   |
| `dmesg` 或 `journalctl -k`                   | 内核级硬件信息                     | 硬件异常、驱动问题排查     |



------

## **二、服务层面**

| 日志                                                  | 说明                       |
| ----------------------------------------------------- | -------------------------- |
| `/var/log/crond` 或 `journalctl -u crond`             | 定时任务执行情况           |
| `/var/log/httpd/`、`/var/log/nginx/`                  | Web 服务访问日志、错误日志 |
| `/var/log/php-fpm/`                                   | PHP 应用错误               |
| `/var/log/mysqld.log`、`/var/log/mariadb/mariadb.log` | 数据库运行情况             |
| `/var/log/sssd/` 或 `journalctl -u sssd`              | 域认证/集中身份管理        |
| `/var/log/samba/`、`/var/log/zabbix/`                 | 特定服务日志               |



------

## **三、安全 & 审计**

| 日志                        | 用途                   |
| --------------------------- | ---------------------- |
| `/var/log/secure`           | 登录、sudo 等认证信息  |
| `/var/log/btmp` （`lastb`） | 登录失败历史           |
| `/var/log/wtmp` （`last`）  | 登录登出历史           |
| `/var/log/audit/audit.log`  | SELinux / 安全审计信息 |



------

## **四、软件包和系统变更**

| 日志                                     | 用途                             |
| ---------------------------------------- | -------------------------------- |
| `/var/log/yum.log` 或 `/var/log/dnf.log` | 软件安装升级记录                 |
| `/var/log/rhsm/`                         | 红帽订阅管理（如适用）           |
| `/var/log/anaconda/`                     | 安装系统时的日志（排查安装问题） |



##  **五、按实际业务重点**

- 有哪些业务服务，就要重点关注它们的日志目录
    - Web： `/var/log/nginx/`、`/var/log/httpd/`
    - DB： `/var/log/mysql/`、`/var/log/mariadb/`
    - 应用： `/var/log/your_app/`
- 定期巡检 + 建议接入**日志平台**（ELK、Loki、Zabbix、Prometheus 等）

