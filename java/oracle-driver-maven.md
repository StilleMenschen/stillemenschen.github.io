# Oracle 连接驱动

访问地址 https://repo1.maven.org/maven2/com/oracle/database/jdbc/

命令行进入到下载文件所在目录，将jar包安装到下载目录

```
mvn install:install-file -Dfile=ojdbc6.jar -DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.2.0.4 -Dpackaging=jar -DgeneratePom=true
```

配置依赖

```xml
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.4</version>
</dependency>
```

Last Modified 2021-12-17
