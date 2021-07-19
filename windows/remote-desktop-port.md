# 远程桌面端口

## 查看当前端口

```powershell
Get-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "PortNumber"
```

## 修改端口

```powershell
Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "PortNumber" -Value 3390
```

## 添加防火墙

```powershell
New-NetFirewallRule -DisplayName 'RDPPORTLatest' -Profile 'Public' -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3390
```

Last Modified 2021-07-19
