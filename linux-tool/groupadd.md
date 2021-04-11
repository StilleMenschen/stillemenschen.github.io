# groupadd

## 简介

创建新用户组。`groupadd`命令使用命令行上指定的值以及系统的默认值创建一个新的组帐户。新组将根据需要输入到系统文件中
```
groupadd [options] group
```

## 选项

<style>
table th:first-of-type {
    width: 16%;
}
</style>

选项 | 说明
:- | :-
−f, −−force           | 如果指定的组已经存在，则此选项使命令以成功状态简单地退出。当与`-g`一起使用时，并且指定的`GID`已经存在，则选择另一个（唯一的）GID（即`-g`已关闭）
−g, −−gid GID         | 组ID的数值。除非使用`-o`选项，否则该值必须唯一。该值必须为非负值。默认值为使用大于或等于`GID_MIN`且大于其他所有组的最小ID值
−K, −−key KEY=VALUE   | 覆盖`/etc/login.defs`的默认值（`GID_MIN`，`GID_MAX`等）。可以指定多个-K选项。示例：`−K GID_MIN = 100 −K GID_MAX = 499`。注意：`−K GID_MIN = 10，GID_MAX = 499`尚不可用
−o, −−non−unique      | 此选项允许添加具有非唯一`GID`的组
−r, −−system          | 创建一个系统组。在`login.defs`中定义的`SYS_GID_MIN`到`SYS_GID_MAX`范围中选择新系统组的数字标识符，而不是`GID_MIN`到`GID_MAX`
−R, −−root CHROOT_DIR | 在`CHROOT_DIR`目录中应用更改，并使用`CHROOT_DIR`目录中的配置文件
-h, --help            | 显示帮助信息并退出

## 相关文件

- `/etc/group` 组帐户信息
- `/etc/gshadow` 安全的组帐户信息
- `/etc/login.defs` 影子密码套件配置

## 命令示例

1. 使用`-g`选项新建test工作组名，`1005`是工作组id
    ```
    groupadd -g 1005 test
    ```

2. 使用-r创建系统工作组
    ```
    groupadd -r -g 368 test
    ```

Last Modified 2021-04-11
