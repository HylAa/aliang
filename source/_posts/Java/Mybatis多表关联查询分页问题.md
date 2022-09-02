---
title: Mybatis - 多表关联查询分页问题
tags:
  - Mybatis
categories: []
date: 2021-01-22 15:15:00
updated: 2021-01-22 15:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1609389860/wallhaven-wyv566_1920x1080_uc4oad.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1609389860/wallhaven-wyv566_1920x1080_uc4oad.png
---

# 背景
之前给公司做的管理系统，今天被测出来，前台展示的分页数量和实际数量不符合，导致前台分页有误！（这么大的BUG竟然没发现，我也是服了自己了）
本项目数据库使用MySql，插件有如下两个

MyBatis
MyBatisPlus 分页插件

分析
产生BUG的地方
下方是未修改之前的SQL，通过多表连接查询一对多的数据

# 修改前的SQL
```sql
SELECT
	u.*,
	r.role_id,
	r.role_name 
FROM
	sys_user AS u
	LEFT JOIN sys_user_role AS ur ON u.user_id = ur.user_id
	LEFT JOIN sys_role AS r ON ur.role_id = r.role_id 
ORDER BY
	u.user_id
```

该SQL查询出的信息如下图

可以看出，这里查询出了7条数据。对于同一个主表，如果在子表中拥有多个关联时，就会查询出多条。
然后，使用查询出的结果，在后台使用 `MyBatils` 的 `collection` 进行一对多的处理。代码如下
```xml
    <resultMap type="com.xxx.modules.sys.entity.SysUserEntity" id="sysDetailUserMap">
        <id property="userId" column="user_id" />
        <result property="userName" column="user_name" />
        <result property="loginName" column="login_name"/>
        <result property="mobile" column="mobile" />
        <result property="email" column="email" />
        <result property="sts" column="sts" />
        <result property="regOrgId" column="reg_org_id" />
        <result property="regOrgName" column="org_name" />
        <result property="regTenantId" column="reg_tenant_id" />
        <result property="regTenantName" column="tenant_name" />
        <result property="createTime" column="create_time" />
        <collection property="roles" ofType="com.xxx.modules.sys.entity.SysRoleEntity">
            <id  property="roleId" column="role_id" />
            <result property="roleName" column="role_name" />
        </collection >
    </resultMap>
```
经过这样，然后将查询的结果发送到前端，最终获取到的JSON结构如下所示
```json
{
    "msg":"操作成功",
    "code":0,
    "page":{
        "totalCount":7, // 这里总数为SQL中查询的数据
        "pageSize":10,
        "totalPage":1,
        "currPage":1,
        "list":[{},{},{},{},{}] // 查询出的数据又是经过mybatis处理后的数据
    }
}
```

这里就可以看出，导致前台分页总数和实际展示的数量不一致的问题，就是分页总数与MyBatis数据不一致造成的。
似乎在MyBatis处理前，分页插件先查询了count，共查询7条纪录，之后再由MyBatis进行处理，然后处理完毕就剩5条纪录。

产生BUG的原因
既然产生了BUG，那就要理解其产生的原因。既然是分页插件和MyBaits的问题，那么就从这两个地方入手。
我所使用的是MyBatisPlus的分页插件，先了解一下它的分页实现原理吧。通过跟踪分析代码，观察它的实现方法
```java
// SQL拦截的地方
public Object intercept(Invocation invocation) throws Throwable {
        StatementHandler statementHandler = (StatementHandler)PluginUtils.realTarget(invocation.getTarget());
        MetaObject metaObject = SystemMetaObject.forObject(statementHandler);
    	// 解析SQL
        this.sqlParser(metaObject);
        MappedStatement mappedStatement = (MappedStatement)metaObject.getValue("delegate.mappedStatement");
    	// 验证是否为SELECT操作，并且不为存储过程
        if (SqlCommandType.SELECT == mappedStatement.getSqlCommandType() && StatementType.CALLABLE != mappedStatement.getStatementType()) {
            BoundSql boundSql = (BoundSql)metaObject.getValue("delegate.boundSql");
            Object paramObj = boundSql.getParameterObject();
            IPage<?> page = null;
            // 验证参数值是否为page对象，如果参数值为Map，则从Map中读取page对象
            if (paramObj instanceof IPage) {
                page = (IPage)paramObj;
            } else if (paramObj instanceof Map) {
                Iterator var8 = ((Map)paramObj).values().iterator();
 
                while(var8.hasNext()) {
                    Object arg = var8.next();
                    if (arg instanceof IPage) {
                        page = (IPage)arg;
                        break;
                    }
                }
            }
			// 当page不存在或者size小于0的情况下，不进行分页
            if (null != page && page.getSize() >= 0L) {
                if (this.limit > 0L && this.limit <= page.getSize()) {
                    page.setSize(this.limit);
                }
 
                String originalSql = boundSql.getSql();
                Connection connection = (Connection)invocation.getArgs()[0];
                DbType dbType = StringUtils.isNotEmpty(this.dialectType) ? DbType.getDbType(this.dialectType) : JdbcUtils.getDbType(connection.getMetaData().getURL());
                if (page.isSearchCount()) {
                    SqlInfo sqlInfo = SqlParserUtils.getOptimizeCountSql(page.optimizeCountSql(), this.countSqlParser, originalSql);
                    // 使用sql查询结果总数
                    this.queryTotal(this.overflow, sqlInfo.getSql(), mappedStatement, boundSql, page, connection);
                    if (page.getTotal() <= 0L) {
                        return null;
                    }
                }
				// 将原始SQL和page拼接起来
                String buildSql = concatOrderBy(originalSql, page);
                DialectModel model = DialectFactory.buildPaginationSql(page, buildSql, dbType, this.dialectClazz);
                Configuration configuration = mappedStatement.getConfiguration();
                List<ParameterMapping> mappings = new ArrayList(boundSql.getParameterMappings());
                Map<String, Object> additionalParameters = (Map)metaObject.getValue("delegate.boundSql.additionalParameters");
                model.consumers(mappings, configuration, additionalParameters);
                metaObject.setValue("delegate.boundSql.sql", model.getDialectSql());
                metaObject.setValue("delegate.boundSql.parameterMappings", mappings);
                return invocation.proceed();
            } else {
                return invocation.proceed();
            }
        } else {
            return invocation.proceed();
        }
    }
```

由源码可知，分页插件在执行的时候，会先去查询所属SQL的总数，然后使用查到的总数进行分页，再与原始SQL拼接。
因此显而易见，问题出现在这里。

如何解决BUG
BUG的解决办法有很多，我采用的是**先查询出主表中的数据总数，在执行collection时，再去使用子查询去查询一对多的关系。**此方法可能不适用与多对多查询

解决问题
修改SQL
在mybatis中，修改查询SQL，使第一次查询只查询主表（不要查询一对多的关系）
```xml
<!-- 修改查询SQL，改为只查询主表 -->
<select id="selectUserPageList" resultMap="sysBaseUserMap">
  SELECT
    u.*
  FROM
    sys_user AS u
  ORDER BY
    u.user_id
</select>
 
<!-- 额外增加一个查询关联表的sql，其中user_id为子查询需要的条件，在collection中传入 -->
<select id="getUserRole" resultType="com.xxx.modules.sys.entity.SysRoleEntity">
	SELECT
    r.*
	FROM
    sys_role as r
	LEFT JOIN
    sys_user_role as ur
	ON
    r.role_id = ur.role_id
	WHERE
    ur.user_id = #{user_id}
</select>
```

修改collection
由于用到了子查询，因此需要修改原先的一对多处理方式，将其改为调用子查询
```xml
<resultMap type="com.xxx.modules.sys.entity.SysUserEntity" id="sysDetailUserMap">
	<id property="userId" column="user_id" />
	<result property="userName" column="user_name" />
	<result property="loginName" column="login_name"/>
	<result property="mobile" column="mobile" />
	<result property="email" column="email" />
	<result property="sts" column="sts" />
	<result property="regOrgId" column="reg_org_id" />
	<result property="regOrgName" column="org_name" />
	<result property="regTenantId" column="reg_tenant_id" />
	<result property="regTenantName" column="tenant_name" />
	<result property="createTime" column="create_time" />
	<!-- column指向一个主表和子表的共有字段 -->
  <collection property="roles" ofType="com.xxx.modules.sys.entity.SysRoleEntity" column="user_id" select="getUserRole">
		<id  property="roleId" column="role_id" />
		<result property="roleName" column="role_name" />
	</collection >
</resultMap>
```
查询结果
经过如上修改后，再次查询结果如下所示
```json
{
    "msg":"操作成功",
    "code":0,
    "page":{
        "totalCount":5,
        "pageSize":10,
        "totalPage":1,
        "currPage":1,
        "list":[{},{},{},{},{}]
    }
}
```
总结
这种方式可以解决一对多的问题。但针对于多对多问题还没有考虑过。做个记录

Q.E.D. 