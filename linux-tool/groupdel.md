# groupdel

## 简介

删除用户组。`groupdel`命令修改系统帐户文件，删除所有引用`GROUP`的条目。命名组必须存在

```
groupdel [options] GROUP
```

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项                  | 说明                                                             |
| :-------------------- | :--------------------------------------------------------------- |
| −R, −−root CHROOT_DIR | 在`CHROOT_DIR`目录中应用更改，并使用`CHROOT_DIR`目录中的配置文件 |
| -h, --help            | 显示帮助信息并退出                                               |

Last Modified 2021-04-11
