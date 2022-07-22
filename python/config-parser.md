# Config Parser

## Python 3

```python
from configparser import ConfigParser
from configparser import ExtendedInterpolation

config = ConfigParser()
config['DEFAULT'] = {'ServerAliveInterval': '45',
                     'Compression': 'yes',
                     'CompressionLevel': '9'}
config['bitbucket.org'] = {}
config['bitbucket.org']['User'] = 'hg'
config['topsecret.server.com'] = {}
top_secret = config['topsecret.server.com']
top_secret['Port'] = '50022'  # mutates the parser
top_secret['ForwardX11'] = 'no'  # same here
config['DEFAULT']['ForwardX11'] = 'yes'
config['Paths'] = {}
top_secret = config['Paths']
top_secret['home_dir'] = '/Users'
top_secret['my_dir'] = '%(home_dir)s/lumberjack'
top_secret['my_pictures'] = '%(my_dir)s/Pictures'
config['Common'] = {}
top_secret = config['Common']
top_secret['system_dir'] = '/System'
config['Frameworks'] = {}
top_secret = config['Frameworks']
top_secret['path'] = '${Common:system_dir}/Library/Frameworks/'
with open('example.ini', 'w') as configfile:
    config.write(configfile)

config.read('example.ini')
print(config.sections())
print('bitbucket.org' in config)
print('bytebong.com' in config)  # False
print(config['bitbucket.org']['User'])
print(config['DEFAULT']['Compression'])
top_secret = config['topsecret.server.com']
print(top_secret['ForwardX11'])
print(top_secret['Port'])
for key in config['bitbucket.org']:
    print(key)
print(config['bitbucket.org']['ForwardX11'])  # yes
print(top_secret.get('exists', 'None'))  # None
print(top_secret.getint('nums', 99))  # 99
top_secret = config['Paths']
print('my_pictures:', top_secret.get('my_pictures'))

config = ConfigParser(allow_no_value=True,
                      interpolation=ExtendedInterpolation())
config.read('example.ini')
top_secret = config['Frameworks']
print('path:', top_secret.get('path'))
```

>配置文件的单行注释为`#`或`;`

## Python 2

```python
# encoding=UTF-8

import ConfigParser

config = ConfigParser.RawConfigParser()

# When adding sections or items, add them in the reverse order of
# how you want them to be displayed in the actual file.
# In addition, please note that using RawConfigParser's and the raw
# mode of ConfigParser's respective set functions, you can assign
# non-string values to keys internally, but will receive an error
# when attempting to write to a file or when you get it in non-raw
# mode. SafeConfigParser does not allow such assignments to take place.
config.add_section('Section1')
config.set('Section1', 'an_int', '15')
config.set('Section1', 'a_bool', 'true')
config.set('Section1', 'a_float', '3.1415')
config.set('Section1', 'baz', 'fun')
config.set('Section1', 'bar', 'Python')
config.set('Section1', 'foo', '%(bar)s is %(baz)s!')

# Writing our configuration file to 'example.cfg'
with open('example.cfg', 'wb') as configfile:
    config.write(configfile)

config.read('example.cfg')

# getfloat() raises an exception if the value is not a float
# getint() and getboolean() also do this for their respective types
a_float = config.getfloat('Section1', 'a_float')
an_int = config.getint('Section1', 'an_int')
print a_float + an_int

# Notice that the next output does not interpolate '%(bar)s' or '%(baz)s'.
# This is because we are using a RawConfigParser().
if config.getboolean('Section1', 'a_bool'):
    print config.get('Section1', 'foo')

config = ConfigParser.ConfigParser()
config.read('example.cfg')

# Set the third, optional argument of get to 1 if you wish to use raw mode.
print config.get('Section1', 'foo', False)  # -> "Python is fun!"
print config.get('Section1', 'foo', True)  # -> "%(bar)s is %(baz)s!"

# The optional fourth argument is a dict with members that will take
# precedence in interpolation.
print config.get('Section1', 'foo', False, {'bar': 'Documentation',
                                            'baz': 'evil'})

# New instance with 'bar' and 'baz' defaulting to 'Life' and 'hard' each
config = ConfigParser.SafeConfigParser({'bar': 'Life', 'baz': 'hard'})
config.read('example.cfg')

print config.get('Section1', 'foo')  # -> "Python is fun!"
config.remove_option('Section1', 'bar')
config.remove_option('Section1', 'baz')
print config.get('Section1', 'foo')  # -> "Life is hard!"
```

## 参考文档

- Configuration file parser https://docs.python.org/zh-cn/3.7/library/configparser.html
- Configuration file parser https://docs.python.org/zh-cn/2.7/library/configparser.html

Last Modified 2021-07-06
