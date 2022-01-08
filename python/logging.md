# Logging

## 简单方式

```python
import logging

logging.basicConfig(filename='example2.log', level=logging.DEBUG, format='%(asctime)s - %(levelname)s - %(message)s')
logging.debug('This message should go to the log file')
logging.info('So should this')
logging.warning('And this, too')
```

## 高级方式

```python
import logging
from logging.handlers import TimedRotatingFileHandler

# Multiple calls to getLogger() with the same name will return a reference to the same logger object.
log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)
log_format = logging.Formatter(fmt='%(asctime)s %(levelname)s %(name)s %(message)s', datefmt='%Y-%m-%d %H:%M:%S')
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.WARNING)
console_handler.setFormatter(log_format)

file_handler = logging.FileHandler(filename='example.log')
file_handler.setFormatter(log_format)

rotating_file_handler = TimedRotatingFileHandler(filename='rotating-example.log', when='midnight')
rotating_file_handler.setFormatter(log_format)

log.addHandler(console_handler)
log.addHandler(file_handler)
log.addHandler(rotating_file_handler)

log.debug('debug message')
log.info('info message')
log.warning('warning message')
log.error('error message')
log.critical('critical message')
```

## 参考文档

- 日志 HOW TO https://docs.python.org/zh-cn/2.7/howto/logging.html
- 日志 HOW TO https://docs.python.org/zh-cn/3.7/howto/logging.html

Last Modified 2022-01-08
