---
title: SpringBoot+Mybatis实现自定义SQL,可视化SQL进行查询
tags:
  - Mybatis
  - SpringBoot
categories: []
date: 2023-07-04 12:15:00
updated: 2023-07-04 12:15:00
top_img: https://mybatis.org/images/mybatis-logo.png
cover: https://mybatis.org/images/mybatis-logo.png
---

@[toc]
## 概要

>根据后台维护的SQL执行查询，前端同事调用一个通用接口进行执行，最终反回结果给到前端，参考了市场BI工具，自己通过SpringBoot+Mybatis 手动实现了一个，比较粗糙，大家有更好的建议可以提出到评论区！
## 技术细节

>项目采用RuoyiVue版本进行开发，大家可根据自己得框架进行修改，大体逻辑一样

1. SQL配置类
```java
package com.unis.datav.domain;

/**
 * @className: SysSqlConfig
 * @description: SQL配置类
 * @author: 阿良
 * @date: 2023/6/30
 **/
import org.apache.commons.lang3.builder.ToStringBuilder;
import org.apache.commons.lang3.builder.ToStringStyle;
import com.unis.common.annotation.Excel;
import com.unis.common.core.domain.BaseEntity;

/**
 * 【SQL配置类】对象 sys_sql_config
 *
 * @author aliang
 * @date 2023-06-30
 */
public class SysSqlConfig extends BaseEntity
{
    private static final long serialVersionUID = 1L;

    /** $column.columnComment */
    private Long id;

    /** $column.columnComment */
    @Excel(name = "${comment}", readConverterExp = "$column.readConverterExp()")
    private String moduleKey;

    /** sql语句 */
    @Excel(name = "sql语句")
    private String sqlStatement;

    /** 模块名称 */
    @Excel(name = "模块名称")
    private String moduleName;

    public void setId(Long id)
    {
        this.id = id;
    }

    public Long getId()
    {
        return id;
    }
    public void setModuleKey(String moduleKey)
    {
        this.moduleKey = moduleKey;
    }

    public String getModuleKey()
    {
        return moduleKey;
    }
    public void setSqlStatement(String sqlStatement)
    {
        this.sqlStatement = sqlStatement;
    }

    public String getSqlStatement()
    {
        return sqlStatement;
    }
    public void setModuleName(String moduleName)
    {
        this.moduleName = moduleName;
    }

    public String getModuleName()
    {
        return moduleName;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this,ToStringStyle.MULTI_LINE_STYLE)
                .append("id", getId())
                .append("moduleKey", getModuleKey())
                .append("sqlStatement", getSqlStatement())
                .append("createBy", getCreateBy())
                .append("createTime", getCreateTime())
                .append("updateBy", getUpdateBy())
                .append("updateTime", getUpdateTime())
                .append("remark", getRemark())
                .append("moduleName", getModuleName())
                .toString();
    }
}

```
2. Mapper接口

```java
package com.unis.datav.mapper;

/**
 * @className: SysSqlConfigMapper
 * @description: TODO 类描述
 * @author: 阿良
 * @date: 2023/6/30
 **/

import com.unis.datav.domain.SysSqlConfig;

import java.util.List;

/**
 * 【请填写功能名称】Mapper接口
 *
 * @author ruoyi
 * @date 2023-06-30
 */
public interface SysSqlConfigMapper
{
    /**
     * 查询【请填写功能名称】
     *
     * @param id 【请填写功能名称】主键
     * @return 【请填写功能名称】
     */
    public SysSqlConfig selectSysSqlConfigById(Long id);
    public String selectSysSqlConfigByKey(String key);

    /**
     * 查询【请填写功能名称】列表
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 【请填写功能名称】集合
     */
    public List<SysSqlConfig> selectSysSqlConfigList(SysSqlConfig sysSqlConfig);

    /**
     * 新增【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    public int insertSysSqlConfig(SysSqlConfig sysSqlConfig);

    /**
     * 修改【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    public int updateSysSqlConfig(SysSqlConfig sysSqlConfig);

    /**
     * 删除【请填写功能名称】
     *
     * @param id 【请填写功能名称】主键
     * @return 结果
     */
    public int deleteSysSqlConfigById(Long id);

    /**
     * 批量删除【请填写功能名称】
     *
     * @param ids 需要删除的数据主键集合
     * @return 结果
     */
    public int deleteSysSqlConfigByIds(Long[] ids);
}

```
3. Service接口

```java
package com.unis.datav.service;

/**
 * @className: ISysSqlConfigService
 * @description: TODO 类描述
 * @author: 阿良
 * @date: 2023/6/30
 **/

import com.unis.datav.domain.SysSqlConfig;

import java.util.List;

/**
 * 【请填写功能名称】Service接口
 *
 * @author ruoyi
 * @date 2023-06-30
 */
public interface ISysSqlConfigService
{
    /**
     * 查询【请填写功能名称】
     *
     * @param id 【请填写功能名称】主键
     * @return 【请填写功能名称】
     */
    public SysSqlConfig selectSysSqlConfigById(Long id);
    public String selectSysSqlConfigByKey(String key);

    /**
     * 查询【请填写功能名称】列表
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 【请填写功能名称】集合
     */
    public List<SysSqlConfig> selectSysSqlConfigList(SysSqlConfig sysSqlConfig);

    /**
     * 新增【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    public int insertSysSqlConfig(SysSqlConfig sysSqlConfig);

    /**
     * 修改【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    public int updateSysSqlConfig(SysSqlConfig sysSqlConfig);

    /**
     * 批量删除【请填写功能名称】
     *
     * @param ids 需要删除的【请填写功能名称】主键集合
     * @return 结果
     */
    public int deleteSysSqlConfigByIds(Long[] ids);

    /**
     * 删除【请填写功能名称】信息
     *
     * @param id 【请填写功能名称】主键
     * @return 结果
     */
    public int deleteSysSqlConfigById(Long id);
}

```
4. 接口实现

```java
package com.unis.datav.service.impl;

/**
 * @className: SysSqlConfigServiceImpl
 * @description: TODO 类描述
 * @author: 阿良
 * @date: 2023/6/30
 **/

import com.unis.common.utils.DateUtils;
import com.unis.datav.domain.SysSqlConfig;
import com.unis.datav.mapper.SysSqlConfigMapper;
import com.unis.datav.service.ISysSqlConfigService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 【请填写功能名称】Service业务层处理
 *
 * @author ruoyi
 * @date 2023-06-30
 */
@Service
public class SysSqlConfigServiceImpl implements ISysSqlConfigService
{
    @Autowired
    private SysSqlConfigMapper sysSqlConfigMapper;

    /**
     * 查询【请填写功能名称】
     *
     * @param id 【请填写功能名称】主键
     * @return 【请填写功能名称】
     */
    @Override
    public SysSqlConfig selectSysSqlConfigById(Long id)
    {
        return sysSqlConfigMapper.selectSysSqlConfigById(id);
    }

    @Override
    public String selectSysSqlConfigByKey(String key) {
        return sysSqlConfigMapper.selectSysSqlConfigByKey(key);
    }

    /**
     * 查询【请填写功能名称】列表
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 【请填写功能名称】
     */
    @Override
    public List<SysSqlConfig> selectSysSqlConfigList(SysSqlConfig sysSqlConfig)
    {
        return sysSqlConfigMapper.selectSysSqlConfigList(sysSqlConfig);
    }

    /**
     * 新增【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    @Override
    public int insertSysSqlConfig(SysSqlConfig sysSqlConfig)
    {
        sysSqlConfig.setCreateTime(DateUtils.getNowDate());
        return sysSqlConfigMapper.insertSysSqlConfig(sysSqlConfig);
    }

    /**
     * 修改【请填写功能名称】
     *
     * @param sysSqlConfig 【请填写功能名称】
     * @return 结果
     */
    @Override
    public int updateSysSqlConfig(SysSqlConfig sysSqlConfig)
    {
        sysSqlConfig.setUpdateTime(DateUtils.getNowDate());
        return sysSqlConfigMapper.updateSysSqlConfig(sysSqlConfig);
    }

    /**
     * 批量删除【请填写功能名称】
     *
     * @param ids 需要删除的【请填写功能名称】主键
     * @return 结果
     */
    @Override
    public int deleteSysSqlConfigByIds(Long[] ids)
    {
        return sysSqlConfigMapper.deleteSysSqlConfigByIds(ids);
    }

    /**
     * 删除【请填写功能名称】信息
     *
     * @param id 【请填写功能名称】主键
     * @return 结果
     */
    @Override
    public int deleteSysSqlConfigById(Long id)
    {
        return sysSqlConfigMapper.deleteSysSqlConfigById(id);
    }
}

```
5. Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.unis.datav.mapper.SysSqlConfigMapper">

    <resultMap type="SysSqlConfig" id="SysSqlConfigResult">
        <result property="id"    column="id"    />
        <result property="moduleKey"    column="module_key"    />
        <result property="sqlStatement"    column="sql_statement"    />
        <result property="createBy"    column="create_by"    />
        <result property="createTime"    column="create_time"    />
        <result property="updateBy"    column="update_by"    />
        <result property="updateTime"    column="update_time"    />
        <result property="remark"    column="remark"    />
        <result property="moduleName"    column="module_name"    />
    </resultMap>

    <sql id="selectSysSqlConfigVo">
        select id, module_key, sql_statement, create_by, create_time, update_by, update_time, remark, module_name from sys_sql_config
    </sql>

    <select id="selectSysSqlConfigList" parameterType="SysSqlConfig" resultMap="SysSqlConfigResult">
        <include refid="selectSysSqlConfigVo"/>
        <where>
            <if test="moduleKey != null  and moduleKey != ''"> and module_key = #{moduleKey}</if>
            <if test="sqlStatement != null  and sqlStatement != ''"> and sql_statement = #{sqlStatement}</if>
            <if test="moduleName != null  and moduleName != ''"> and module_name like concat('%', #{moduleName}, '%')</if>
        </where>
    </select>

    <select id="selectSysSqlConfigById" parameterType="Long" resultMap="SysSqlConfigResult">
        <include refid="selectSysSqlConfigVo"/>
        where id = #{id}
    </select>
 <select id="selectSysSqlConfigByKey" parameterType="String" resultType="String">
        select sql_statement from sys_sql_config
        where module_key = #{key}
    </select>

    <insert id="insertSysSqlConfig" parameterType="SysSqlConfig">
        insert into sys_sql_config
        <trim prefix="(" suffix=")" suffixOverrides=",">
            <if test="id != null">id,</if>
            <if test="moduleKey != null">module_key,</if>
            <if test="sqlStatement != null">sql_statement,</if>
            <if test="createBy != null">create_by,</if>
            <if test="createTime != null">create_time,</if>
            <if test="updateBy != null">update_by,</if>
            <if test="updateTime != null">update_time,</if>
            <if test="remark != null">remark,</if>
            <if test="moduleName != null">module_name,</if>
        </trim>
        <trim prefix="values (" suffix=")" suffixOverrides=",">
            <if test="id != null">#{id},</if>
            <if test="moduleKey != null">#{moduleKey},</if>
            <if test="sqlStatement != null">#{sqlStatement},</if>
            <if test="createBy != null">#{createBy},</if>
            <if test="createTime != null">#{createTime},</if>
            <if test="updateBy != null">#{updateBy},</if>
            <if test="updateTime != null">#{updateTime},</if>
            <if test="remark != null">#{remark},</if>
            <if test="moduleName != null">#{moduleName},</if>
        </trim>
    </insert>

    <update id="updateSysSqlConfig" parameterType="SysSqlConfig">
        update sys_sql_config
        <trim prefix="SET" suffixOverrides=",">
            <if test="moduleKey != null">module_key = #{moduleKey},</if>
            <if test="sqlStatement != null">sql_statement = #{sqlStatement},</if>
            <if test="createBy != null">create_by = #{createBy},</if>
            <if test="createTime != null">create_time = #{createTime},</if>
            <if test="updateBy != null">update_by = #{updateBy},</if>
            <if test="updateTime != null">update_time = #{updateTime},</if>
            <if test="remark != null">remark = #{remark},</if>
            <if test="moduleName != null">module_name = #{moduleName},</if>
        </trim>
        where id = #{id}
    </update>

    <delete id="deleteSysSqlConfigById" parameterType="Long">
        delete from sys_sql_config where id = #{id}
    </delete>

    <delete id="deleteSysSqlConfigByIds" parameterType="String">
        delete from sys_sql_config where id in
        <foreach item="id" collection="array" open="(" separator="," close=")">
            #{id}
        </foreach>
    </delete>
</mapper>

```
6. 共用查询Mapper

```java
package com.unis.datav.mapper;

import org.apache.ibatis.annotations.Select;

import java.util.List;
import java.util.Map;

/**
 * @className: CustomQueryMapper
 * @description: TODO 类描述
 * @author: 阿良
 * @date: 2023/7/4
 **/
public interface CustomQueryMapper {
    @Select("${query}")
    List<Map<String,Object>> executeQuery(String query);
}

```

7. 查询Service
> **这里面根据SQL里的标签进行匹配和替换，核心方法**

```java
package com.unis.datav.service.impl;

import com.unis.datav.mapper.CustomQueryMapper;
import com.unis.datav.service.ISysSqlConfigService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * @className: QueryService
 * @description: TODO 类描述
 * @author: 阿良
 * @date: 2023/7/4
 **/
@Service
public class QueryService {

    @Autowired
    private  ISysSqlConfigService sqlQueryRepository;

    @Autowired
    private  CustomQueryMapper customQueryMapper;

    // constructor injection for dependencies


    public List<Map<String,Object>> executeQuery(String identifier, Map<String, Object> parameters) {
        // get the query from the database
        String query = sqlQueryRepository.selectSysSqlConfigByKey(identifier);

        // process the query to replace custom tags with actual parameters
        Pattern pattern = Pattern.compile("<(.*?)>(.*?)</\\1>");
        Matcher matcher = pattern.matcher(query);
        StringBuffer sb = new StringBuffer();

        while (matcher.find()) {
            String tag = matcher.group(1);
            Object value = parameters.get(tag);
            if (value != null) {
                // Check if the value is a string
                if (value instanceof String) {
                    value = "'" + value + "'";  // Add single quotes around the string
                }
                // Create the replacement string
                String replacement = matcher.group(2).replace("?{" + tag + "}", value.toString());
                // Replace the tag with the replacement string
                matcher.appendReplacement(sb, replacement);
            } else {
                // If the value is not present in the parameters, remove the tag entirely
                matcher.appendReplacement(sb, "");
            }
        }
        matcher.appendTail(sb);
        // execute the query and return the result
        return customQueryMapper.executeQuery(sb.toString());
    }


    public static void main(String[] args) {
        String sql = "select distinct B.ID 流水号, B.USER_INFO_ID 人员ID, B.SCHOOL_NAME 学院, B.START_DATE 入学日期, B.GRADUATE_DATE 毕业日期, B.MAJOR_NAME 专业名称, B.EDUCATION 学历, B.DEGREE 学位, B.CREATER 创建人, B.CREATE_TIME 创建时间, A.NAME 姓名, B.UPDATER 更新人 from HR_STAFF_EDU_INFO B left join hr_staff_info A ON A.USER_INFO_ID=B.USER_INFO_ID where 1=1 <peopleid>and B.USER_INFO_ID=?{peopleid}</peopleid>;";
        Map<String, Object> parameters = new HashMap<>();  // your parameters
        parameters.put("peopleid", "123456");
        Pattern pattern = Pattern.compile("<(.*?)>(.*?)</\\1>");
        Matcher matcher = pattern.matcher(sql);
        StringBuffer sb = new StringBuffer();

        while (matcher.find()) {
            String tag = matcher.group(1);
            Object value = parameters.get(tag);
            if (value != null) {
                // Create the replacement string
                String replacement = matcher.group(2).replace("?{" + tag + "}", value.toString());
                // Replace the tag with the replacement string
                matcher.appendReplacement(sb, replacement);
            } else {
                // If the value is not present in the parameters, remove the tag entirely
                matcher.appendReplacement(sb, "");
            }
        }
        matcher.appendTail(sb);

        sql = sb.toString();
        System.out.println(sql);
    }
}

```

8. 接口调用 传参
> `identifier` 参数对应sql配置表中的唯一标识
> `parameters` 参数对应要执行的查询参数Map形式 例如`{
    "peopleid": "181722_19633"
}`


```java
 @PostMapping("/query/{identifier}")
    public  List<Map<String,Object>> executeQuery(@PathVariable String  identifier, @RequestBody Map<String, Object> parameters) {
        // execute the query and return the result
        return queryService.executeQuery(identifier, parameters);
    }
```

### 效果图
![调用接口](https://img-blog.csdnimg.cn/160706e9d08c4875b3a1cc05df600344.png)

![界面配置](https://img-blog.csdnimg.cn/3aa197ed11654a478fe138adb9ab4366.png)
![界面配置](https://img-blog.csdnimg.cn/a43d3a36fa894b2c9b0dbf65794ba1fb.png)

## 小结
` 提示：此方式是否存在问题目前还没验证，简单实现，大家有更好的办法或者解决方案欢迎留言评论！`

