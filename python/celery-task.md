# Celery Task

## tasks.py

```python
# 导入 Celery
from celery import Celery
from datetime import timedelta
from celery.schedules import crontab

# 配置 Celery
celery_broker = 'amqp://user:pass@127.0.0.1:5672//'  # RabbitMQ 或其他消息中间件的地址
celery_backend = 'redis://localhost:6379/2'  # 存储数据的中间件 RabbitMQ/Redis

# 定义任务模块
app = Celery('celery', broker=celery_broker, backend=celery_backend)

# 配置 Celery
app.conf.update(
    task_acks_late=True,  # 允许重试
    accept_content=['pickle', 'json'],  # 接受的序列化数据格式
    task_serializer='json',  # 客户端与 worker 传输数据序列化
    result_serializer='json',  # 结果数据序列化
    worker_concurrency=4,  # 设置并发 worker 数量
    worker_max_tasks_per_child=100,  # 每个 worker 最多执行 500 个任务被销毁，可以防止内存泄漏
    broker_heartbeat=100,  # 心跳
    task_time_limit=180,  # 超时时间
    celery_timezone='Asia/Shanghai',  # 时区设置
    enable_utc=True,
    result_expires=timedelta(minutes=3)  # 存储结果过期时间
)


# 定义定时任务
app.conf.beat_schedule = {
    'sub': {
        'task': 'tasks.sub',  # 任务名（注意要和模块名/文件名相同）
        'schedule': timedelta(seconds=30),  # 每隔 3 秒执行一次
        'args': (300, 150),  # 任务参数
    },
    'add': {
        'task': 'tasks.add',  # 任务名
        # 使用 crontab 表达式来触发任务定时执行某件事情，比如每周一早八点
        'schedule': crontab(minute='*', hour='*'),
        'args': (235, 26),  # 任务参数
    }
}

# 定义任务

@app.task
def add(x, y):
    return x + y


@app.task
def sub(x, y):
    return x - y
```

## 启动

启动 beat 指定日志级别为 INFO，由 beat 来发送任务给 worker 执行

```bash
celery -A tasks beat -l INFO
```

启动 worker 并指定任务执行使用线程池，接收任务并计算结果

```bash
celery -A tasks worker -P threads -l INFO
```

## 参考文档

- https://docs.celeryq.dev/en/stable/index.html

Last Modified 2024-02-20
