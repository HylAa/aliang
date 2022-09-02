---
title: Mybatis - 批量操作总结
tags:
  - Mybatis
categories: []
date: 2021-01-22 15:15:00
updated: 2021-01-22 15:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1609389860/wallhaven-wyv566_1920x1080_uc4oad.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1609389860/wallhaven-wyv566_1920x1080_uc4oad.png
---

# 背景
记录自己在项目中总结的mybatis操作和用法

### `mybatis` 操作(批量插入,批量修改,批量删除,分页查询)
- 环境描述 Java\Mysql\Mybatis
  
1.批量插入
mapper 类方法
```java
int insertBatch(List<ShoppingCartBean> goodsList);
```
XML 配置

```xml
<insert id="insertBatch" parameterType="java.util.List">

   insert into th_shopping_Cart 

    (itemCode, userId, number)

   values

   <foreach collection="list" item="item" index="index" separator=",">

    (#{item.itemCode},#{item.userId},#{item.number})

   </foreach>

</insert>
```
1. 分页查询
页面请求参数
- 页面显示条数 size
- 第page页面

返回结果
- 所有记录条目总数:totalNum
- 当前page页
- 每页记录数pageSize
- 记录列表list
mapper类方法

```java
// 查询第page页的所有记录
List<ShoppingCartBean> selectByUserId(String userId, Integer start, Integer size);
// 查询所有记录数量
int selectCount(String userId);
```

  xml配置 
  ```xml
  <select id="selectByUserId" resultMap="BaseResultMap">
    select 
    <include refid="Base_Column_List" />
    from th_shopping_Cart
    where userId =#{0}
    // start = （page - 1）* size 
    order by createTime DESC limit #{1},#{2}
  </select>
  <select id="selectCount" resultType="java.lang.Integer">
     SELECT COUNT(userId) AS _count 
     FROM `th_shopping_Cart`
     WHERE userId=#{0} 
  </select>
  ```
3.mybatis 批量修改
url配置
在mysql数据库连接上增加 &allowMultiQueries=true

例如:
```yml
jdbc:mysql://*******:3306/tohome?autoReconnect=true&autoReconnectForPools=true&interactiveClient=true&useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true
```
mapper类方法
```java
    //参数为List<T>
int updateBatch(List<ShoppingCartBean> list);
```
XML配置逻辑
```xml
<update id="updateBatch" parameterType="java.util.List">
    <foreach collection="list" item="item" index="index" open="" close="" separator=";">
        update th_shopping_Cart 
        <set>
            number=${item.number}
        </set>
        where userId='${item.userId}' and itemCode='${item.itemCode}'
    </foreach>
  </update>
  ```
4.批量删除
XML配置
```xml
<delete id="deleteBatch" parameterType="com.morning.star.tohome.store.entity.ShoppingItems">
    delete from th_shopping_Cart
    where userId =#{userId}

    <if test="null!=list and list.size > 0">
        and itemCode in

         <foreach collection="list" item="item" index="index" open="(" close=")" separator=",">
            #{item.itemCode}
    </foreach>
    </if>
  </delete>
  ```
## 即使再小的帆也能远航（前提得一直坚持驶到彼岸！） --Aliang