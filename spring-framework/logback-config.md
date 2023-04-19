# Logback Config

application.yml

```yml
spring:
  application:
    name: demo
  datasource:
    url: jdbc:mariadb://localhost:3306/db
    username: root
    password: root
    driver-class-name: org.mariadb.jdbc.Driver
  jpa:
    hibernate:
      ddl-auto: create-drop
      show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MariaDBDialect
server:
  error:
    include-message: always
    include-stacktrace: never
logging:
  file:
    path: /opt/logs
  level:
    com:
      example:
        demo: DEBUG
```

文件名为`logback-spring.xml`则可以支持读取spring配置文件中的变量

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 日志目录 -->
    <property scope="context" name="LOG_HOME" value="/opt"/>
    <!-- 读取配置文件中的路径 -->
    <springProperty scope="context" name="LOG_PATH" source="logging.file.path"/>
    <!-- 读取配置文件中的名称 -->
    <springProperty scope="context" name="APP_NAME" source="spring.application.name"/>
    <!-- 日志格式化 -->
    <property name="FILE_LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{39} %msg%n"/>

    <!-- 单个日志文件大小 -->
    <property name="LOG_FILE_MAX_SIZE" value="64MB"/>
    <!-- 日志文件保留天数 -->
    <property name="LOG_FILE_MAX_HISTORY" value="15"/>

    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>

    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${APP_NAME}-info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${APP_NAME}-info-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <maxHistory>${LOG_FILE_MAX_HISTORY}</maxHistory>
            <maxFileSize>${LOG_FILE_MAX_SIZE}</maxFileSize>
            <!-- 根据日志大小调节删除策略，但仍然建议优先使用 maxHistory 来调节日志文件删除策略 -->
            <!-- <totalSizeCap>2000MB</totalSizeCap> -->
        </rollingPolicy>
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %level %logger{80} [%file:%line] - %msg %n</pattern>
        </encoder>
        <!-- 接收级别为 INFO 的，拒绝 INFO 之后的级别 -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>DENY</onMatch>
        </filter>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>DENY</onMatch>
        </filter>
    </appender>
    <!-- 时间滚动输出 level 为 ERROR 日志 -->
    <appender name="WARN_ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/${APP_NAME}-warn-error.log</file>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${APP_NAME}-warn-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--日志文件保留天数 -->
            <maxHistory>${LOG_FILE_MAX_HISTORY}</maxHistory>
            <maxFileSize>${LOG_FILE_MAX_SIZE}</maxFileSize>
        </rollingPolicy>
        <!--日志文件输出格式 -->
        <encoder>
            <pattern>${FILE_LOG_PATTERN}</pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
        <!-- 此日志文件只记录 WARN ERROR 级别的 注意过滤器是 ThresholdFilter -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>WARN</level>
        </filter>
    </appender>
    <appender name="TRACE_DEBUG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${APP_NAME}-trace.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${APP_NAME}-trace-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <maxHistory>${LOG_FILE_MAX_HISTORY}</maxHistory>
            <maxFileSize>${LOG_FILE_MAX_SIZE}</maxFileSize>
        </rollingPolicy>
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %level %logger{80} [%file:%line] - %msg %n</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>TRACE</level>
        </filter>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>DENY</onMatch>
        </filter>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>DENY</onMatch>
        </filter>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>DENY</onMatch>
        </filter>
    </appender>
    <!-- 控制台 -->
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
        </filter>
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}} </pattern>
        </encoder>
    </appender>
    <!-- Hibernate SQL 输出 -->
    <!--<logger name="org.hibernate.SQL" level="trace" additivity="false">
        <appender-ref ref="INFO_FILE" />
    </logger>-->
    <!-- 本地环境:输出到控制台 -->
    <springProfile name="local">
        <root level="warn">
            <appender-ref ref="stdout"/>
        </root>
    </springProfile>

    <!-- 生产环境:输出到文件 -->
    <springProfile name="dev | test | prod">
        <root level="info">
            <appender-ref ref="INFO_FILE"/>
            <appender-ref ref="WARN_ERROR_FILE"/>
            <appender-ref ref="TRACE_DEBUG_FILE"/>
        </root>
    </springProfile>
</configuration>
```

Last Modified 2023-04-19
