# 命令行选项

<style>
table th:first-of-type {
    width: 14%;
}
</style>

选项 | 说明
:- | :-
-?, -h        | 显示命令行选项的帮助
-c file       | 使用备用配置文件而不是默认文件
-e file       | 使用备用错误日志文件而不是默认文件（V1.19.5）来存储日志。特殊值`stderr`表示标准错误输出文件
-g directives | 设置全局配置指令，例如，``nginx -g "pid /var/run/nginx.pid; worker_processes `sysctl -n hw.ncpu`;"``
-p prefix     | 设置nginx路径前缀，即将保留服务器文件的目录（默认值为`/usr/local/nginx`）
-q            | 在配置测试期间抑制非错误消息。
-s signal     | 将信号发送到主进程。选项信号可以是以下之一：<br>`stop` 快速关闭<br>`quit` 正常关闭<br>`reload` 重新加载配置，使用新配置启动新的工作进程，并正常关闭旧的工作进程<br>`reopen` 重新打开日志文件
-t            | 测试配置文件：nginx检查配置中的语法是否正确，并尝试打开配置中引用的文件
-T            | 与`-t`相同，但另外将配置文件打印到标准输出（V1.9.2）
-v            | 打印nginx版本
-V            | 打印nginx版本，编译器版本和配置选项

Last Modified 2021-04-11