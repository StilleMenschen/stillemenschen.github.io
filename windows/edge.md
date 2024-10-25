# Edge 浏览器

关闭侧边栏和 Edge 栏

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge]
"SuppressUnsupportedOSWarning"=dword:00000001
"HubsSidebarEnabled"=dword:00000000
"StandaloneHubsSidebarEnabled"=dword:00000000
"CopilotPageContextEnabled"=dword:00000000
"WebWidgetAllowed"=dword:00000000

[HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Edge]
"SuppressUnsupportedOSWarning"=dword:00000001
"HubsSidebarEnabled"=dword:00000000
"StandaloneHubsSidebarEnabled"=dword:00000000
"CopilotPageContextEnabled"=dword:00000000
"WebWidgetAllowed"=dword:00000000
```

可以参考地址`edge://policy/`中的定义

Last Modified 2024-10-25
