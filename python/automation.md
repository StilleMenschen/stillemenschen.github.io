# 自动化

## Windows Notepad

```python
import os
from os import path
from pywinauto.application import Application

filename = path.join(os.environ['USERPROFILE'], 'Desktop', 'aaa.txt')
if os.path.exists(filename):
    print('delete file...')
    os.remove(filename)

app = Application(backend='uia').start('notepad.exe')
notepad = app.window(title='Untitled - Notepad')
notepad.wait('visible')
# if not notepad.is_maximized():
#     notepad.maximize()
notepad.type_keys('Hello world!!!', with_spaces=True)
notepad = app.window(title='*Untitled - Notepad')
notepad.menu_select('File -> Save')
save_as_notepad = notepad.child_window(title_re='Save As')
save_as_notepad.wait('visible')
save_as_notepad.type_keys(filename)
save_as_notepad.type_keys('{ENTER}')
notepad = app.window(title='aaa.txt - Notepad')
notepad.wait('visible')
notepad.type_keys('%FX')
```

Last Modified 2022-07-30
