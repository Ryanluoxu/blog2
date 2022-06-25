---
title: PostgreSQL 10 Windows 使用记录
date: 2018-02-22 21:48:57
categories: 使用记录
tags: [PostgreSQL, 10, Windows, 使用记录]
---
PostgreSQL 10 Windows 使用记录
<!--more-->

# Windows 基本指令
- cd /d
- dir


# 问题

## 重置 postgres 用户的密码
1. 打开 H:\PostgreSQL\10\data\pg_hba.conf
2. 将下面的 md5 都改成 trust，保存。
```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
```
3. 打开 cmd
```
psql -U postgres
```
4. 无需密码，直接进入，然后
```
alter user postgres with password '<password>';
```
5. 修改好密码后，退出
```
\q
```
6. 将 pg_hba.conf 的 trust 改回 md5 即可。

## SQuirrel 连接数据库
- jdbc:postgresql://localhost:5432/dk
- postgres
- xxxxx

## Create database and user
```
psql -U postgres
用户 postgres 的口令：

postgres=# create database logocessor
CREATE DATABASE

postgres=# CREATE USER logocessor WITH PASSWORD 'logocessor';
CREATE ROLE

postgres=# GRANT ALL PRIVILEGES ON database logocessor TO logocessor;
GRANT
```

## SQL 指令

### Create table
```
CREATE TABLE account(
	user_id serial PRIMARY KEY,
	user_name VARCHAR (50) UNIQUE NOT NULL,
	password VARCHAR (50) NOT NULL,
	email VARCHAR (355) UNIQUE NOT NULL,
	created_on TIMESTAMP NOT NULL,
	last_login TIMESTAMP
	role_id integer NOT NULL,
);
```

### Insert
```
INSERT INTO table(column1, column2, …)
VALUES
 (value1, value2, …);
```


------------------------


drop table dream;

CREATE TABLE dream(
	dream_id serial PRIMARY KEY,
	title VARCHAR (100),
	content VARCHAR (50000) NOT NULL,
	user_id integer NOT NULL
);

SELECT * FROM dream;

INSERT INTO dream (content, user_id)
VALUES
 ('Happy New Year everyone and welcome to the first Retro Flame blog post of the year. I’m back again with my annual ‘Dream Year’ post. Each year at this time, I look ahead  and ask myself what my IDEAL year would look like (absolutely zero limits) and then write it all down here on the blog. I started doing this in 2016 and as soon as I saw the difference it made on the way I approached a year, I promised I would do it every year. You also seem to really like it too, so here I am today with my Dream 2018 version.',1);
 