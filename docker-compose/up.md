# up

## 简介

构建，（重新）创建，启动多个容器

## 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

选项 | 说明
:- | :-
-d, --detach              | 分离模式：在后台运行容器，打印新的容器名称，与`--abort-on-container-exit`选项冲突
--no-color                | 单色输出
--quiet-pull              | 拉取镜像时不输出进度
--no-deps                 | 不启动链接的服务
--force-recreate          | 即使配置和镜像没有改变也重新创建容器
--always-recreate-deps    | 重新创建依赖容器，与`--no-recreate`选项冲突
--no-recreate             | 不重新创建已存在的容器，与`--force-recreate`和`--renew-anon-volumes`选项冲突
--no-build                | 即使没有生成镜像也不要构建镜像
--no-start                | 创建服务完成后不启动服务
--build                   | 启动前先构建镜像
--abort-on-container-exit | 若某个容器停止了则自动停止所有的容器，与`--detach`选项冲突
--attach-dependencies     | 连接到依赖的容器
-t, --timeout TIMEOUT     | 设置容器运行或连接关闭的超时时间（单位秒，默认值：10）
-V, --renew-anon-volumes  | 重新创建匿名卷，而不是检查先前已有的容器数据
--remove-orphans          | 删除未在Compose文件中定义的服务的容器
--exit-code-from SERVICE  | 返回所选服务容器的退出代码。意味着`--abort-on-container-exit`

Last Modified 2021-04-17