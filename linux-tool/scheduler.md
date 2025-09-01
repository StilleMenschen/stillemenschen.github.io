# at & crontab

## 简介

Linux 系统提供了两种主要的定时任务管理工具：`at`和`crontab`。`at`用于安排一次性任务在特定时间执行，而`crontab`用于设置周期性重复执行的任务。

## at 命令

### 简介

`at`命令用于在指定时间执行一次性任务。它从标准输入读取命令并在以后的时间执行它们。

```
at [OPTION]... TIME
```

### 选项

<style>
table th:first-of-type {
    width: 18%;
}
</style>

| 选项     | 说明                                                     |
| :------- | :------------------------------------------------------- |
| -f FILE  | 从指定文件而不是标准输入读取命令                         |
| -m       | 在作业完成后发送邮件给用户（即使没有输出）               |
| -M       | 从不发送邮件给用户                                       |
| -l       | 列出用户的待处理作业（等同于`atq`）                      |
| -r JOB   | 删除指定的作业（等同于`atrm`）                           |
| -t TIME  | 以`[[CC]YY]MMDDhhmm[.ss]`格式指定作业运行时间            |
| -q QUEUE | 使用指定的队列（队列由单个字母表示，字母越高优先级越低） |
| -b       | 在系统负载较低时执行作业（等同于`batch`）                |
| -v       | 显示作业将被执行的时间                                   |

### TIME 格式说明

at 命令支持多种时间格式：

- HH:MM - 指定当天的小时和分钟（如果时间已过，则第二天执行）
- midnight - 午夜（00:00）
- noon - 中午（12:00）
- teatime - 下午茶时间（16:00）
- AM/PM - 上午/下午指示符
- MMDDYY, MM/DD/YY, DD.MM.YY - 日期格式
- now + count time-units - 相对时间（如 now + 5 minutes）
- today - 今天
- tomorrow - 明天

### 示例

1. 创建一个在指定时间执行的作业

   ```bash
   at 14:30
   at> echo "任务执行于 $(date)" >> /tmp/at_job.log
   at> <EOT> # 按Ctrl+D结束输入
   ```

2. 从文件读取命令创建作业

   ```bash
   at -f /path/to/script.sh 10:00
   ```

3. 列出所有待处理的 at 作业

   ```bash
   at -l
   atq # 等效命令
   ```

4. 删除指定的 at 作业

   ```bash
   at -r 1
   atrm 1 # 等效命令
   ```

5. 在 5 分钟后执行命令
   ```bash
   at now + 5 minutes
   at> your_command
   at> <EOT>
   ```

## crontab 命令

### 简介

`crontab`用于安装、卸载或列出用于驱动 cron 守护进程的表格。用户可以使用 crontab 定义周期性执行的任务。

```
crontab [-u user] file
crontab [-u user] [-l | -r | -e] [-i]
```

### 选项

| 选项    | 说明                                 |
| :------ | :----------------------------------- |
| -u USER | 指定用户（只有 root 可以使用此选项） |
| -l      | 列出当前用户的 cron 作业             |
| -r      | 删除当前用户的所有 cron 作业         |
| -e      | 编辑当前用户的 cron 作业             |
| -i      | 与-r 一起使用时，在删除前提示确认    |

### crontab 时间格式

crontab 文件包含每行一个作业，格式如下：

```
分钟 小时 日 月 星期 命令
```

时间字段的取值范围：

- 分钟：0-59
- 小时：0-23
- 日：1-31
- 月：1-12
- 星期：0-7（0 和 7 都代表星期日）

特殊字符：

- `*` - 任意值
- `,` - 值列表分隔符（如 1,3,5）
- `-` - 范围（如 1-5）
- `/` - 步长值（如\*/2 表示每 2 个单位）

### 系统 crontab 文件

除了用户 crontab，系统还提供以下配置文件：

- `/etc/crontab` - 系统 crontab 文件
- `/etc/cron.d/` - 包含额外 crontab 文件的目录
- `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` - 按周期执行的脚本目录

### 示例

1. 编辑当前用户的 cron 作业

   ```bash
   crontab -e
   ```

2. 列出当前用户的 cron 作业

   ```bash
   crontab -l
   ```

3. 删除当前用户的所有 cron 作业

   ```bash
   crontab -r
   ```

4. 从文件加载 cron 作业

   ```bash
   crontab /path/to/crontab-file
   ```

5. crontab 条目示例：

   ```bash
   # 每天凌晨2点执行备份脚本
   0 2 * * * /home/user/backup.sh

   # 每周末凌晨3点清理临时文件
   0 3 * * 0 /home/user/cleanup.sh

   # 工作日每30分钟检查一次系统状态
   */30 * * * 1-5 /home/user/check_status.sh

   # 每月1号中午12点发送月度报告
   0 12 1 * * /home/user/send_report.sh
   ```

## 注意事项

1. 环境变量：cron 作业执行时环境变量可能与交互式 shell 不同，建议在脚本中设置所需环境变量

2. 输出处理：cron 会将命令输出通过邮件发送给用户，若要禁止输出，可以使用重定向：

   ```bash
   * * * * * /path/to/command >/dev/null 2>&1
   ```

3. 路径问题：cron 作业中的命令最好使用绝对路径

4. 权限管理：普通用户只能管理自己的 cron 作业，root 用户可以管理所有用户的作业

5. 日志查看：系统 cron 日志通常位于`/var/log/cron`或`/var/log/syslog`

## 常见问题排查

1. 检查 cron 服务是否运行：

   ```bash
   systemctl status cron
   # 或
   systemctl status crond
   ```

2. 查看 cron 日志以排查问题：

   ```bash
   tail -f /var/log/cron
   # 或
   grep CRON /var/log/syslog
   ```

3. 测试 cron 环境：
   ```bash
   # 在crontab中添加测试作业
   * * * * * env > /tmp/cron_env.txt
   ```

Last Modified 2023-09-01
