# useradd

## 简介

如果在不使用-D 选项的情况下调用 useradd 命令，则会使用命令行上指定的值以及系统的默认值来创建新的用户帐户。 根据命令行选
项，useradd 命令将更新系统文件，并且还可以创建新用户的主目录并复制初始文件。

```
useradd [options] LOGIN
useradd -D
useradd -D [options]
```

## 选项

<style>
table th:first-of-type {
    width: 18%;
}
</style>

| 选项                         | 说明                                       |
| :--------------------------- | :----------------------------------------- |
| -b, --base-dir BASE_DIR      | 新账户的主目录的基目录                     |
| -c, --comment COMMENT        | 新账户的`GECOS`字段                        |
| -d, --home-dir HOME_DIR      | 新账户的主目录                             |
| -D, --defaults               | 显示或更改默认的`useradd`配置              |
| -e, --expiredate EXPIRE_DATE | 新账户的过期日期                           |
| -f, --inactive INACTIVE      | 新账户的密码不活动期                       |
| -g, --gid GROUP              | 新账户主组的名称或`ID`                     |
| -G, --groups GROUPS          | 新账户的附加组列表                         |
| -h, --help                   | 显示此帮助信息并推出                       |
| -k, --skel SKEL_DIR          | 使用此目录作为骨架目录                     |
| -K, --key KEY=VALUE          | 不使用`/etc/login.defs`中的默认值          |
| -l, --no-log-init            | 不要将此用户添加到最近登录和登录失败数据库 |
| -m, --create-home            | 创建用户的主目录                           |
| -M, --no-create-home         | 不创建用户的主目录                         |
| -N, --no-user-group          | 不创建同名的组                             |
| -o, --non-unique             | 允许使用重复的`UID`创建用户                |
| -r, --system                 | 创建一个系统账户                           |
| -R, --root CHROOT_DIR        | `chroot`到的目录                           |
| -s, --shell SHELL            | 新账户的登录`shell`                        |
| -u, --uid UID                | 新账户的用户`ID`                           |
| -U, --user-group             | 创建与用户同名的组                         |
| -Z, --selinux-user SEUSER    | 为`SELinux`用户映射使用指定`SEUSER`        |

## 相关文件

- `/etc/passwd` 用户帐户信息（登录名:口令占位符:用户ID:用户组ID:用户私人信息:用户主目录:登录Shell）
- `/etc/shadow` 安全的用户帐户信息（登录名:加密后的口令:上次修改口令的日期:两次修改口令之间的天数（最少）:两次修改口令之间的天数（最多）:提前多少天提示修改口令:在口令过期多少天后禁用:账户过期日期:保留无含义）
- `/etc/group` 组帐户信息（组名:口令占位符:组ID:成员列表）
- `/etc/gshadow` 安全的组帐户信息
- `/etc/default/useradd` 创建帐户的默认值
- `/etc/skel/` 包含默认文件的目录
- `/etc/login.defs` 影子密码套件配置

>`/etc/shadow`文件中的时间是以`1970年1月1日`至今的天数来表示的

## 命令示例

1. 新增一个用户 test

   ```bash
   useradd test
   ```

2. 新增用户但不创建 HOME 目录，并禁止登录

   ```bash
   useradd -M -s /sbin/nologin test
   ```

3. 添加新用户 test，指定 UID 为 888，指定归属用户组为 root，adm 成员，其 shell 类型为/bin/sh

   ```bash
   useradd -u 888 -s /bin/sh -G root,adm test
   ```

4. 添加新用户 test，设置 HOME 目录为/tmp/test，用户过期时间为 2019/05/01.过期后两天停权

   ```bash
   useradd -e "2019/05/01" -f 2 -d /tmp/test test
   ```

5. 添加新用户 test，并同时创建同名组
   ```bash
   useradd -U test
   ```

Last Modified 2021-04-11
