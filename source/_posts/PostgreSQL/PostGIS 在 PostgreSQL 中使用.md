---
title: PgSql - PostGIS 在 PostgreSQL 中使用
tags:
  - PostgreSQL
categories: []
date: 2022-09-13 09:48:00
updated: 2022-09-13 09:48:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1663050229/pg/1_7AOhGDnRL2eyJMUidCHZEA_olmcfy.jpg
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1663050229/pg/1_7AOhGDnRL2eyJMUidCHZEA_olmcfy.jpg

---



### 介绍

------

首先来介绍一下 PostGIS 是什么？
PostGIS 是 PostgreSQL 对象关系数据库的一个空间数据库扩展。它增加了对地理对象的支持，允许在SQL中运行位置查询。

官方介绍：

> PostGIS：Spatial and Geographic objects for PostgreSQL

> PostGIS is a spatial database extender for PostgreSQL object-relational database. It adds support for geographic objects allowing location queries to be run in SQL.

```postgresql
SELECT superhero.name
FROM city, superhero
WHERE ST_Contains(city.geom, superhero.geom)
AND city.name = 'Gotham';
```

> In addition to basic location awareness, PostGIS offers many features rarely found in other competing spatial databases such as Oracle Locator/Spatial and SQL Server.

### 安装

***不同系统不同安装方式 不再此处展开讲***

---



### 背景

> 公司新项目需要在系统中整合gis图，之前没有接触过，特此开一篇文章来讲解学习，后续持续更新。

---



### 启用

PostGIS 是一个可选扩展，在你想使用它之前必须在每个数据库中启用它，然后才能使用它。

通过 psql 或 PgAdmin 连接你的数据库，然后运行下面SQL

```postgresql
-- Enable PostGIS (as of 3.0 contains just geometry/geography)
CREATE EXTENSION postgis;
-- Enable Topology
CREATE EXTENSION postgis_topology;
```

*** 注意：不要在 `postgres` 数据库中启用

---



### 升级

升级到最新版本：

```
ALTER EXTENSION postgis UPDATE;
ALTER EXTENSION postgis_topology UPDATE;
```



或指定版本升级：

```
ALTER EXTENSION postgis
 UPDATE TO "3.1.0";
-- Upgrade Topology
ALTER EXTENSION postgis_topology
 UPDATE TO "3.1.0";
```

---



### 使用

首先，创建一个带有空间列的表

```postgresql
CREATE TABLE mytable (
  id SERIAL PRIMARY KEY,
  geom GEOMETRY(Point, 26910),
  name VARCHAR(128)
);
```

解释 ：个人理解 安装完PostGis扩展后 pg库支持了 `GEOMETRY` 类型  特殊类型。

---



然后添加空间索引

```postgresql
CREATE INDEX mytable_gix
  ON mytable
  USING GIST (geom);
```

---



插入测试数据

```postgresql
INSERT INTO mytable (geom) VALUES (
  ST_GeomFromText('POINT(0 0)', 26910)
);
```

解释：通过`ST_GeomFromText`存储 特殊的几何数据/点位数据。

---



查询附近的点

```postgresql
SELECT id, name
FROM mytable
WHERE ST_DWithin(
  geom,
  ST_GeomFromText('POINT(0 0)', 26910),
  1000
);
```

---

