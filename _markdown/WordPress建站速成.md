---
title: WordPress建站速成
date: 2024-11-04 23:00:58
tags: [笔记, linux,wordpress,web,docker]
categories: [软件技术]
---

# docker搭建wordpress

```bash
mkdir wordpress
```

```bash
cd wordpress
```

```bash
vim docker-compose.yml
```

```yaml
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress_app
    depends_on:
      - db
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress_db
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: strong_password
    volumes:
      - wordpress_data:/var/www/html

  db:
    image: mysql:5.7
    container_name: wordpress_db
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: wordpress_db
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: strong_password
    volumes:
      - db_data:/var/lib/mysql

volumes:
  wordpress_data:
  db_data:

```

