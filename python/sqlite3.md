# SQLite3

## Python 3

```python
import sqlite3

conn = sqlite3.connect('example.db')

c = conn.cursor()

# Create table
c.execute('DROP TABLE IF EXISTS stocks')
c.execute('''CREATE TABLE IF NOT EXISTS stocks
             (id INTEGER PRIMARY KEY ASC, date text, trans text, symbol text, qty real, price real)''')

# Insert a row of data
c.execute("INSERT INTO stocks (date, trans, symbol, qty, price) VALUES ('2006-01-05','BUY','RHAT',100,35.14)")

# Save (commit) the changes
conn.commit()

# We can also close the connection if we are done with it.
# Just be sure any changes have been committed or they will be lost.
conn.close()

conn = sqlite3.connect('example.db')
c = conn.cursor()

# Do this instead
t = ('RHAT',)
c.execute('SELECT * FROM stocks WHERE symbol=?', t)
print(c.fetchone())

# Larger example that inserts many records at a time
purchases = [('2006-03-28', 'BUY', 'IBM', 1000, 45.00),
             ('2006-04-05', 'BUY', 'MSFT', 1000, 72.00),
             ('2006-04-06', 'SELL', 'IBM', 500, 53.00),
             ]
c.executemany('INSERT INTO stocks (date, trans, symbol, qty, price) VALUES (?,?,?,?,?)', purchases)

for row in c.execute('SELECT * FROM stocks ORDER BY price'):
    print(row)
```

> Python 2 的语法类似

## 参考文档

- SQLite 数据库 DB-API 2.0 接口模块 https://docs.python.org/zh-cn/3.7/library/sqlite3.html
- SQLite 数据库 DB-API 2.0 接口模块 https://docs.python.org/zh-cn/2.7/library/sqlite3.html

Last Modified 2021-05-22
