# Mybatis

- 使用生成器生成动态 SQL 所需的实体类、Mapper 操作接口

## pom.xml

Maven 依赖文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.10.RELEASE</version>
        <relativePath/>
        <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>springbootdemo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringBootDemo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>
        <!-- 集成Mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
        <!-- 动态SQL依赖 -->
        <dependency>
            <groupId>org.mybatis.dynamic-sql</groupId>
            <artifactId>mybatis-dynamic-sql</artifactId>
            <version>1.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <dependencies>
                    <!-- 生成器依赖的数据库驱动 -->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.23</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <!-- 如果文件存在则覆盖，方便重新生成，实体类和Mapper-->
                    <overwrite>true</overwrite>
                    <!-- 生成器配置文件位置 -->
                    <configurationFile>${basedir}/src/main/resources/mybatis-generator.xml</configurationFile>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

## application.yml

Spring 配置文件

```yml
spring:
  datasource:
    hikari:
      driver-class-name: com.mysql.cj.jdbc.Driver # 数据库连接池
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
```

## mybatis-generator.xml

Mybatis 生成器配置文件

```xml
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="dsql" defaultModelType="flat" targetRuntime="MyBatis3DynamicSql">
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai&amp;useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false"
                        userId="root"
                        password="123456"
        >
            <!-- 如果有多个数据库存在相同名称的表 需要指定此选项 -->
            <!-- 参考 http://mybatis.org/generator/usage/mysql.html -->
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>

        <javaModelGenerator targetPackage="com.example.springbootdemo.model" targetProject="src/main/java"/>

        <javaClientGenerator targetPackage="com.example.springbootdemo.mapper" targetProject="src/main/java"/>

        <table tableName="t_company"/>
        <table tableName="t_job"/>
        <table tableName="t_college"/>
        <table tableName="t_major"/>
        <table tableName="t_grade"/>
        <table tableName="t_class"/>
    </context>
</generatorConfiguration>
```

## 简单示例

Mybatis 会根据生成器配置文件中指定的表名来创建实体类和 Mapper，如果不做特殊配置，一般按驼峰式命名类和接口

由于下面的示例中使用了表连接，首先来创建用来接收表连接查询结果的实体类和 Mapper 操作接口，因为 Mybatis 无法预知你需要如
何连接表，所以需要自己定义

### 实体类

```java
package com.example.springbootdemo.model;

public class TCollegeMajorClass {
    private String collegeId;
    private String collegeName;
    private String majorId;
    private String majorName;
    private String classId;
    private String className;
    // 省略 Getter Setter toString
}
```

### Mapper 接口

这里要注意`@Results`注解中指定的实体类字段名和数据库表字段名（或者连接查询自定义的别名）对应，实体类的数据类型和数据库表
字段类型对应

```java
package com.example.springbootdemo.mapper;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.SelectProvider;
import org.apache.ibatis.type.JdbcType;
import org.mybatis.dynamic.sql.select.render.SelectStatementProvider;
import org.mybatis.dynamic.sql.util.SqlProviderAdapter;
import com.example.springbootdemo.model.TCollegeMajorClass;

import java.util.List;

@Mapper
public interface TCollegeMajorClassMapper {
    @SelectProvider(type = SqlProviderAdapter.class, method = "select")
    @Results(id = "TCollegeMajorClassResult", value = {
            @Result(column = "college_id", property = "collegeId", jdbcType = JdbcType.CHAR, id = true),
            @Result(column = "college_name", property = "collegeName", jdbcType = JdbcType.VARCHAR),
            @Result(column = "major_id", property = "majorId", jdbcType = JdbcType.CHAR),
            @Result(column = "major_name", property = "majorName", jdbcType = JdbcType.VARCHAR),
            @Result(column = "class_id", property = "classId", jdbcType = JdbcType.CHAR),
            @Result(column = "class_name", property = "className", jdbcType = JdbcType.VARCHAR)
    })
    List<TCollegeMajorClass> selectMany(SelectStatementProvider selectStatement);
}
```

### JUnit Test

Mybatis 生成的 Mapper 文件有两个，以`DynamicSqlSupport`结尾的类是用以支持动态 SQL 的，而以`Mapper`结尾的接口则是调用动态
SQL 类来操作数据库的

```java
package com.example.springbootdemo;

import org.junit.jupiter.api.Test;
import org.mybatis.dynamic.sql.render.RenderingStrategy;
import org.mybatis.dynamic.sql.select.render.SelectStatementProvider;
import org.springframework.boot.test.context.SpringBootTest;
import com.example.springbootdemo.mapper.*;
import com.example.springbootdemo.model.TCollegeMajorClass;
import com.example.springbootdemo.model.TJob;

import javax.annotation.Resource;
import java.util.List;
import java.util.Objects;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mybatis.dynamic.sql.SqlBuilder.*;


@SpringBootTest
class SpringBootDemoApplicationTests {

    @Resource
    public TJobMapper jobMapper;

    @Resource
    public TSysCompanyMapper companyMapper;

    @Resource
    public TCollegeMajorClassMapper collegeMajorClassMapper; // 注入自定义的Mapper


    @Test
    void testMybatis() {
        // 基本的单表查询
        final List<TJob> jobList = jobMapper.select(c -> c.where(TJobDynamicSqlSupport.jobName, isLike("岗位00%")).limit(5));
        Optional.of(jobList).ifPresent(list -> list.forEach(e -> System.out.println(e.getJobName())));
        assertTrue(jobList.size() > 0);
        // 单表统计数据数量
        final String findJobName = "岗位0422";
        final SelectStatementProvider provider = select(count(TJobDynamicSqlSupport.tid)) /* 这里的count与数据库查询类似 */
                .from(TJobDynamicSqlSupport.TJob)
                .where(TJobDynamicSqlSupport.jobName, isLike(findJobName)
                        .when(Objects::nonNull).then(s -> s.concat("%")))
                .build().render(RenderingStrategies.MYBATIS3);
        // 通过Mapper操作接口统计数据数量
        final long size = jobMapper.count(provider);
        assertEquals(size, 10);
    }

    @Test
    public void testMybatisJoin() {
        // 由于数据库设计了多公司的概念，此处需要先查出公司ID
        companyMapper.selectOne(c -> c.where(TSysCompanyDynamicSqlSupport.companyName, isEqualTo("公司001"))).ifPresent(company -> {
            final String companyId = company.getTid();
            assertNotNull(companyId);
            System.out.println(companyId);
            // 多表连接查询
            SelectStatementProvider provider = select(
                    /* 这里使用了字段别名，与实体类和Mapper中对应 */
                    TCollegeDynamicSqlSupport.tid.as("college_id"), TCollegeDynamicSqlSupport.collegeName,
                    TMajorDynamicSqlSupport.tid.as("major_id"), TMajorDynamicSqlSupport.majorName,
                    TClassDynamicSqlSupport.tid.as("class_id"), TClassDynamicSqlSupport.className)
                    /* 左连接为表取别名 */
                    .from(TClassDynamicSqlSupport.TClass, "cl")
                    .leftJoin(TMajorDynamicSqlSupport.TMajor, "mj")
                    .on(TClassDynamicSqlSupport.majorId, equalTo(TMajorDynamicSqlSupport.tid))
                    .leftJoin(TCollegeDynamicSqlSupport.TCollege, "co")
                    .on(TMajorDynamicSqlSupport.collegesId, equalTo(TCollegeDynamicSqlSupport.tid))
                    /* 加上公司ID的约束条件 */
                    .where(TClassDynamicSqlSupport.companyId, isEqualTo(companyId))
                    .and(TMajorDynamicSqlSupport.companyId, isEqualTo(companyId))
                    .and(TCollegeDynamicSqlSupport.companyId, isEqualTo(companyId))
                    /* 按名称找出某个班级 */
                    .and(TClassDynamicSqlSupport.className, isEqualTo("班级001"))
                    .build().render(RenderingStrategies.MYBATIS3);
            // 通过自定义Mapper操作接口执行多表查询，并通过自定义的实体类来接收数据
            final List<TCollegeMajorClass> collegeMajorClassList = collegeMajorClassMapper.selectMany(provider);
            assertTrue(collegeMajorClassList.size() > 0);
            Optional.of(collegeMajorClassList).ifPresent(list -> list.forEach(e -> System.out.println(e.toString())));
        });
    }
}
```

## 参考文档

- Mybatis3 基础配置 https://mybatis.org/mybatis-3/
- Mybatis 生成器 http://mybatis.org/generator/
- Mybatis 动态 SQL https://mybatis.org/mybatis-dynamic-sql/docs/introduction.html
- Spring http://mybatis.org/spring/
- Spring Boot Starter http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/
- Mybatis 博客的产品列表 https://blog.mybatis.org/p/products.html

Last Modified 2021-05-02
