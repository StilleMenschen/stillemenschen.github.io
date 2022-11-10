# 监控

## 虚拟机监控

### 权限策略文件

- Java 8

```
grant codebase "file:${java.home}/../lib/tools.jar" {
    permission java.security.AllPermission;
};
```

- Java 11

```
grant codebase "jrt:/jdk.jstatd" {
    permission java.security.AllPermission;
};

grant codebase "jrt:/jdk.internal.jvmstat" {
    permission java.security.AllPermission;
};
```

### 启动 jstatd

参数中指定上面的策略文件，启动后通过`Visual VM`的`Visual GC`插件就可以远程监控虚拟机启动的`Java`程序了

```bash
#!/bin/sh

jstatd -J-Djava.rmi.server.hostname=192.168.1.1 -J-Djava.security.policy=./jstatd.policy -p 4416 &
```

Last Modified 2022-10-11
