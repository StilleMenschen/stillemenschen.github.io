# DateTime

## 时间间隔

```python
from datetime import datetime
from datetime import timedelta
from datetime import date


def main():
    d1 = datetime.now()
    d2 = datetime(year=2021, month=12, day=22, hour=16, minute=30, second=0)
    print(d1 - d2)
    print(d2 - d1)
    delta = timedelta(days=10, hours=6)
    print(d1 - delta)
    print(d2 + delta)
    delta = timedelta(days=-3, hours=7, seconds=-43)
    print(d1 - delta)
    print(d2 + delta)
    delta = timedelta(days=365)
    print(d2 + (10 * delta))
    today = date.today()
    birthday = date(year=today.year, month=2, day=28)
    if birthday < today:
        birthday = birthday.replace(year=today.year + 1)
    delta = abs(birthday - today)
    print('birthday until', delta.days, 'days')


if __name__ == '__main__':
    main()
```

## 格式化和解析

```bash
pip install pytz
```

```python
from datetime import date
from time import time
from datetime import datetime
import pytz


def main():
    today = date.today()
    print(today.isoformat())
    print(today.strftime('%A %m/%d/%Y'))
    now = datetime.now()
    print(now.isoformat(sep=' '))
    print(now.strftime('%A, %m/%d/%Y %I:%S %p'))
    print(datetime.strptime('Wednesday, 03/19/2019 07:23 AM', '%A, %m/%d/%Y %I:%S %p'))
    print(now.timestamp())
    print(datetime.fromtimestamp(time()))
    print(pytz.country_timezones['CN'])
    print(datetime.now(tz=pytz.timezone('Europe/Berlin')).isoformat())


if __name__ == '__main__':
    main()
```

> Python 2 中的 datetime 没有获取时间戳的方法

## 参考文档

- 基本的日期和时间类型 https://docs.python.org/zh-cn/3.7/library/datetime.html
- 基本的日期和时间类型 https://docs.python.org/zh-cn/2.7/library/datetime.html

Last Modified 2021-01-22
