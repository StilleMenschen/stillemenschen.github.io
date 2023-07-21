# PowerShell 配置

调整脚本执行限制（需要管理员权限）

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
```

> 全部都不限制也可以指定 ExecutionPolicy 为 Unrestricted，[参考说明](https://docs.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.2)

全局配置文件路径

```powershell
$PSHOME\Profile.ps1
C:\Windows\System32\WindowsPowerShell\v1.0\Profile.ps1
```

配置文件内容（修改需要管理员权限）

```powershell
$PSDefaultParameterValues['*:Encoding'] = 'utf8'
$OutputEncoding = [System.Console]::OutputEncoding = [System.Console]::InputEncoding = [System.Text.Encoding]::UTF8

function prompt { "$PWD `n> " }
```

如果希望 CMD 的命令提示有一个换行，可以添加一个环境变量`PROMPT=$P$G$_`

> 中文为`936`，西文为`437`

Last Modified 2023-07-21
