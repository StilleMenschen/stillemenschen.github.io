# 删除远程桌面历史

1. 打开注册表，按下`Win + R`组合键，输入`regedit`

    ```
    HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Default
    ```


2. 删除不需要的记录，重新打开远程桌面连接

>部分Windows版本可能不支持修改注册表，如家庭版

Last Modified 2021-05-31
