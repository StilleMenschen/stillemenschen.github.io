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

function prompt { "$PWD`n> " }

function time {
    param (
        [Parameter(Position = 0, Mandatory = $true, ValueFromRemainingArguments =$true)]
        [string[]]$Cmds
    )

    $cmd = $Cmds -join " "
    $t = $(Measure-Command { Invoke-Expression $cmd | Out-Default }).TotalSeconds
    Write-Host "TotalSeconds $t"
}

Set-PSReadLineKeyHandler -Key Ctrl+w -Function BackwardDeleteWord
Set-PSReadLineKeyHandler -Key Ctrl+u -Function BackwardDeleteLine
Set-PSReadLineKeyHandler -Key Ctrl+k -Function DeleteToEnd
```

如果希望 CMD 的命令提示有一个换行，可以添加一个环境变量`PROMPT=$P$_$G$S`

> 中文为`936`，西文为`437`，Unicode 为`65001`，但由于历史原因，Windows 的 UTF-8 编码会带有 BOM，复制粘贴终端里的字符时要留意

## 增强

安装 PSReadLine

```powershell
Install-Module -Name PSReadLine -AllowClobber -Force
```

配置 PSReadLine

```powershell
Set-PSReadLineOption -PredictionViewStyle ListView -PredictionSource History
```

> 可以加入到配置文件中永久生效

查看 PSReadLine 的版本

```powershell
Get-Module PSReadLine | Select-Object Version
```

## 参考阅读

- https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/prompt
- https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_prompts?view=powershell-7.4

Last Modified 2024-11-14
