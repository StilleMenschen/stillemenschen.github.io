# 创建 Git 仓库

## init

初始化仓库

### 选项

<style>
table th:first-of-type {
    width: 20%;
}
</style>

| 选项                                                 | 说明                     |
| :--------------------------------------------------- | :----------------------- |
| -q, --quiet                                          | 安静模式，仅输出错误信息 |
| -b \<branch-name\>, --initial-branch=\<branch-name\> | 指定初始化时的主分支名称 |

### 例子

1. 创建一个 /path/to/my/codebase/.git 目录
2. 将所有现有文件添加到索引中
3. 将原始状态记录为历史上的第一次提交

```bash
cd /path/to/my/codebase
git init      # (1)
git add .     # (2)
git commit    # (3)
```

## clone

克隆仓库

## status

仓库状态

Last Modified 2021-08-24
