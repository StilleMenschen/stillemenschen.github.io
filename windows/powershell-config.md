# PowerShell 配置

关闭脚本执行限制（需要管理员权限）

```powershell
Set-ExecutionPolicy Unrestricted
```

全局配置文件路径

```powershell
$PSHOME\Profile.ps1
C:\Windows\System32\WindowsPowerShell\v1.0\Profile.ps1
```

配置文件内容

```powershell
$PSDefaultParameterValues['*:Encoding'] = 'utf8'
[System.Console]::OutputEncoding = [System.Text.Encoding]::GetEncoding(65001)
```

> 中文为`936`，西文为`437`

Last Modified 2022-03-19
