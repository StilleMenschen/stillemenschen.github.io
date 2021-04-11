# groupmod

## 简介

修改系统上的组定义。`groupmod`命令通过修改组数据库中的适当条目来修改指定`GROUP`的定义
```
groupmod [options] GROUP
```

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

选项 | 说明
:- | :-
−g, −−gid GID            | 给定`GROUP`的组ID将更改为`GID`。`GID`的值必须是非负十进制整数。除非使用`-o`选项，否则该值必须唯一。使用该组作为主要组的用户将被更新，以将该组保留为其主要组。具有旧组ID且必须继续属于`GROUP`的任何文件都必须手动更改其组ID。不会对`/etc/login.defs`中的`GID_MIN`，`GID_MAX`，`SYS_GID_MIN`或`SYS_GID_MAX`进行检查
−n, −−new−name NEW_GROUP | 该组的名称将从`GROUP`更改为`NEW_GROUP`名称
−o, −−non−unique         | 当与`-g`选项一起使用时，允许将组`GID`更改为非唯一值
−R, −−root CHROOT_DIR    | 在`CHROOT_DIR`目录中应用更改，并使用`CHROOT_DIR`目录中的配置文件
-h, --help               | 显示帮助信息并退出

## 相关文件

- `/etc/group` 组帐户信息
- `/etc/gshadow` 安全的组帐户信息
- `/etc/login.defs` 影子密码套件配置
- `/etc/passwd` 用户帐户信息

## 命令示例

1. 更改test用户组为root：
    ```
    groupmod -n root test
    ```

Last Modified 2021-04-11
