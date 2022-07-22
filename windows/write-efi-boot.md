# 写入新 EFI 引导

>如果对 Windows 的分区和对命令行操作不了解的话不建议进行下面的操作，麻烦一点，重装系统吧

1. 准备一个 Windows 启动盘
2. 重启电脑在 BIOS 中选择启动盘进入
3. 进入安装界面后，按下`Shift + F10`
4. 打开 CMD 后，进入磁盘管理`diskpart`
5. 列出磁盘`list disk`
6. 选中你需要建立引导的磁盘`select disk 0`（`0`表示磁盘编号，根据实际情况输入）
7. 列出所需磁盘分区`list partition`
8. 压缩 100MB 的空间出来，用以建立 EFI 分区`shrink desired=100`
9. 创建新 EFI 分区`create partition efi size=100`
10. 快速格式化分区`format quick fs=fat32`
11. 分配一个盘符给此分区`assign letter=x`（`x`表示盘符命名，可自行修改）
12. 再次列出磁盘分区`list partition`，可以看到刚刚新建的分区了，而且是属于 EFI 分区（有`System`的标记）
13. 列出硬盘的盘符`list volume`（一般情况下都是放在`C:\\Windows`）
14. 退出磁盘管理`exit`（注意不是退出 CMD）
15. 写入一个新的引导到刚刚创建的 EFI 分区中`bcdboot C:\\Windows /s X:`（这里的`X`是刚刚步骤 11 中创建分区时分配的盘符）
16. 重启电脑，进入 BIOS 应该就可以看到新的引导分区了（当然旧的也还是在的）

Last Modified 2021-07-18
