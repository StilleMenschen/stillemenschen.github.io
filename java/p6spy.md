# P6Spy 分析

## pom.xml

```xml
<dependency>
    <groupId>p6spy</groupId>
    <artifactId>p6spy</artifactId>
    <version>3.9.1</version>
</dependency>
```

## 自定义日志格式

```java
package com.example.config.p6spy;

import com.p6spy.engine.spy.appender.MessageFormattingStrategy;
import org.apache.commons.lang3.StringUtils;

import java.util.regex.Pattern;

public class P6SpyLogger implements MessageFormattingStrategy {
    private static final Pattern lineBreakAndTwoSpacePattern = Pattern.compile("((\\r?\\n)+|(\\s{2,}))");

    /**
     * 重写日志格式方法
     * <p>
     * now: 当前时间
     * elapsed: 执行耗时
     * category：执行分组
     * prepared：预编译sql语句
     * sql: 执行的真实SQL语句，已替换占位
     */
    @Override
    public String formatMessage(int connectionId, String now, long elapsed, String category, String prepared, String sql, String url) {
        if (StringUtils.isNotBlank(sql)) {
            return  now + " | elapsed "
                    + elapsed + " ms | " + category + "-" + connectionId + "\n "
                    + lineBreakAndTwoSpacePattern.matcher(sql).replaceAll(" ") + ";";
        }
        return "";
    }
}
```

## spy.properties

```properties
module.log=com.p6spy.engine.logging.P6LogFactory,com.p6spy.engine.outage.P6OutageFactory
# 简单的输出格式
#logMessageFormat=com.p6spy.engine.spy.appender.SingleLineFormat
# 自定义的类继承 MessageFormattingStrategy 定义输出格式
logMessageFormat=com.example.config.p6spy.P6SpyLogger
# 自定义输出格式
#logMessageFormat=com.p6spy.engine.spy.appender.CustomLineFormat
#customLogMessageFormat=%(currentTime) | took: %(executionTime) ms | connection: %(category)-%(connectionId) | sql: %(sql)
# 使用控制台记录 SQL
#appender=com.p6spy.engine.spy.appender.StdoutLogger
# 使用文件记录 SQL
appender=com.p6spy.engine.spy.appender.Slf4JLogger
## 配置记录 Log 例外
excludecategories=info,debug,result,batc,resultset
# 设置使用 p6spy driver 来做代理
deregisterdrivers=true
# 日期格式
dateformat=yyyy-MM-dd HH:mm:ss
# 实际驱动
driverlist=com.mysql.jdbc.Driver
# 是否开启慢 SQL 记录
outagedetection=true
# 慢 SQL 记录标准（秒）
outagedetectioninterval=2
```

## application.yml

需要替换部分配置

```yaml
url: jdbc:mysql:/127.0.0.1:3306/mock?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&tinyInt1isBit=false
```

在 `jdbc:` 后增加 `p6spy:`

```yaml
url: jdbc:p6spy:mysql:/127.0.0.1:3306/mock?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&tinyInt1isBit=false
```

修改驱动

```yaml
driver-class-name: com.mysql.jdbc.Driver
```

替换为 P6Spy 的驱动

```yaml
driver-class-name: com.p6spy.engine.spy.P6SpyDriver
```

> 比较新的 MySQL Driver 应该是 `com.mysql.cj.jdbc.Driver`，根据实际情况修改

## 参考文档

- https://p6spy.readthedocs.io/en/latest/install.html
- https://p6spy.readthedocs.io/en/latest/configandusage.html

> 增加了这个分析之后，原来框架的 SQL 输出其实就可以关掉了

Last Modified 2024-02-20
