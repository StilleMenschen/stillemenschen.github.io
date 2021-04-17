# down


## 简介

停止容器并删除`up`创建的容器，网络，卷和镜像

默认情况下，删除以下内容

- 通过Compose文件定义的服务的容器
- 通过`networks`定义的网络
- 默认网络（如果已使用）

不会删除Compose中定义的外部网络和卷

<style>
table th:first-of-type {
    width: 20%;
}
</style>

## 选项

选项 | 说明
:- | :-
--rmi type            | 删除镜像。`type`必须是以下之一：`all`：删除任何服务使用的所有镜像。`local`：仅删除没有由` image`字段设置自定义标签的所有镜像
-v, --volumes         | 删除在Compose文件的`volumes`部分中声明的命名卷和附加到容器的匿名卷
--remove-orphans      | 删除未在Compose文件中定义的服务的容器
-t, --timeout TIMEOUT | 指定关闭超时时间（单位秒，默认值：10）



Last Modified 2021-04-17