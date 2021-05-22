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
from logging import DEBUG
from logging import getLogger
from logging import StreamHandler
from logging import FileHandler
from logging import Formatter

# Multiple calls to getLogger() with the same name will return a reference to the same logger object.
logger = getLogger(__name__)
logger.setLevel(DEBUG)
log_format = Formatter(fmt='%(asctime)s %(levelname)s %(name)s %(message)s', datefmt='%Y-%m-%d %H:%M:%S')
console_handler = StreamHandler()
console_handler.setFormatter(log_format)

file_handler = FileHandler(filename='example.log')
file_handler.setFormatter(log_format)

logger.addHandler(console_handler)
logger.addHandler(file_handler)

logger.debug('debug message')
logger.info('info message')
logger.warning('warning message')
logger.error('error message')
logger.critical('critical message')
```

## 参考文档

- 日志 HOW TO https://docs.python.org/zh-cn/2.7/howto/logging.html
- 日志 HOW TO https://docs.python.org/zh-cn/3.7/howto/logging.html

Last Modified 2021-05-22
