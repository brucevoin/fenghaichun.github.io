---
layout: post
title: "docker-mysql指定字符编码的两种方式"
subtitle: ""
date: 2020-03-02
author: fenghaichun
category: computer
tags: 计算机
finished: true
---


## 初始化mysql时指定mysql配置文件
例如：mysql的dockerFile：

```
from mysql:5.7

copy ./configdb.sql /docker-entrypoint-initdb.d
copy ./my.cnf /etc/
run chmod a+x /docker-entrypoint-initdb.d/configdb.sql
```
说明：
- 其中的configdb.sql指定了初始化数据库的sql脚本，
- my.cnf是mysql的配置文件，内容如下：

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
```
## 在docker-compose.yml中通过command指定

```
version : '2'
services:
  mysqldbserver:
    container_name: mysqldbserver
    build:
      context: .
      dockerfile: mysqldb-dockerfile
    environment:
      - "MYSQL_ROOT_PASSWORD=root"
      - "MYSQL_DATABASE=testdb"
      - "MYSQL_ROOT_HOST=%"
    ports:
      - "3307:3307"
    networks:
      - net-spring-db
    command: [
          'mysqld',
          '--innodb-buffer-pool-size=20M',
          '--character-set-server=utf8',
          '--collation-server=utf8_general_ci',
          '--default-time-zone=+8:00',
          '--lower-case-table-names=1'
        ]

```


