# 重置图标缓存

以管理员身份打开 CMD，运行下面的命令

```batch
TASKKILL /F /IM explorer.exe
ATTRIB -H -I %USERPROFILE%\AppData\Local\IconCache.db
DEL /A %USERPROFILE%\AppData\Local\IconCache.db
START explorer
```

Last Modified 2021-12-30
