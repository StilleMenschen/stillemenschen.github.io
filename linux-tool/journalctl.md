# journalctl

## 简介

journalctl 是一个用于查看、过滤和分析 Linux 系统日志的命令行工具。它与 systemd 集成，可以读取 journald 日志，这些日志以二进制格式存储在“日志”中。

journalctl 是 systemd 系统的一部分，用于与日志交互。它可以按照时间顺序显示日志。支持类似 less 命令的键盘快捷键，例如箭头键、空格、搜索等。

## 选项

| 选项           | 说明                                                                                                                      |
| :------------- | :------------------------------------------------------------------------------------------------------------------------ |
| -r, --reverse  | 反向查看，最新的日志先展示                                                                                                |
| -S, --since    | 指定起始时间，只显示此时间之后的日志，支持字符串 yesterday, today, tomorrow, now 或 yyyy-MM-dd HH:mm:SS 格式的日期时间    |
| -U, --until    | 指定结束时间，只显示此时间之前的日志，支持字符串 yesterday, today, tomorrow, now 或 yyyy-MM-dd HH:mm:SS 格式的日期时间    |
| -u, --unit     | 按单元（服务）过滤日志，如 sshd, crond, containerd                                                                        |
| -p, --priority | 按日志级别过滤日志："emerg" (0), "alert" (1), "crit" (2), "err" (3), "warning" (4), "notice" (5), "info" (6), "debug" (7) |
| -k, --dmesg    | 只显示内核相关的日志                                                                                                      |
| -n, --lines=N  | 显示最近的 N 行日志，N 的默认值为 10                                                                                      |
| -f, --follow   | 追踪日志输出，类似 tail -f                                                                                                |

## 示例

1. 查看日志，按照逆时间顺序显示警告级别的日志

   ```bash
   journalctl -r -p 4
   ```

2. 按单元过滤日志，只显示 sshd 服务的日志

   ```bash
   journalctl --unit sshd
   ```

3. 按时间范围过滤日志

   ```bash
   journalctl --since "2023-12-01" --until "2023-12-10"
   journalctl --since "2023-12-01 12:05" --until "2023-12-10 21:35:10"
   journalctl --since yesterday --until now
   ```

4. 展示最近的 20 条日志并跟踪日志输出

   ```bash
   journalctl -n 20 -f
   ```

Last Modified 2024-04-13
