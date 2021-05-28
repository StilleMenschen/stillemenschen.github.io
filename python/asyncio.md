# 异步IO

## 协程与任务

```python
import asyncio
import time
import datetime
import concurrent.futures


def blocking_io():
    # File operations (such as logging) can block the
    # event loop: run them in a thread pool.
    # /dev/urandom is a linux device file
    with open('/dev/urandom', 'rb') as f:
        return f.read(100)


def cpu_bound():
    # CPU-bound operations will block the event loop:
    # in general it is preferable to run them in a
    # process pool.
    return sum(i * i for i in range(10**7))


async def display_date():
    loop = asyncio.get_running_loop()
    end_time = loop.time() + 5.0
    while True:
        print(datetime.datetime.now())
        if (loop.time() + 1.0) >= end_time:
            break
        await asyncio.sleep(1)


async def say_after(delay, what):
    await asyncio.sleep(delay)
    print(what)


async def main1():
    task1 = asyncio.create_task(say_after(1, 'hello'))

    task2 = asyncio.create_task(say_after(2, 'world'))

    print(f"started at {time.strftime('%X')}")

    # Wait until both tasks are completed (should take
    # around 2 seconds.)
    await task1
    await task2

    print(f"finished at {time.strftime('%X')}")


async def main2():
    loop = asyncio.get_running_loop()

    ## Options:

    # 1. Run in the default loop's executor:
    result = await loop.run_in_executor(None, blocking_io)
    print('default thread pool', result)

    # 2. Run in a custom thread pool:
    with concurrent.futures.ThreadPoolExecutor() as pool:
        result = await loop.run_in_executor(pool, cpu_bound)
        print('custom thread pool', result)

    # 3. Run in a custom process pool:
    with concurrent.futures.ProcessPoolExecutor() as pool:
        result = await loop.run_in_executor(pool, blocking_io)
        print('custom process pool', result)


if __name__ == '__main__':
    asyncio.run(display_date())
    asyncio.run(main1())
    asyncio.run(main2())
```

## 网络IO

### TCP服务端

```python
import asyncio


async def handle_echo(reader, writer):
    data = await reader.read(100)
    message = data.decode()
    address = writer.get_extra_info('peername')

    print(f"Received {message!r} from {address!r}")

    print(f"Send: {message!r}")
    writer.write(data)
    await writer.drain()

    print("Close the connection")
    writer.close()


async def main():
    server = await asyncio.start_server(
        handle_echo, '127.0.0.1', 8888)

    address = server.sockets[0].getsockname()
    print(f'Serving on {address}')

    async with server:
        await server.serve_forever()


asyncio.run(main())
```

### TCP客户端

```python
import asyncio


async def tcp_echo_client(message):
    reader, writer = await asyncio.open_connection(
        '127.0.0.1', 8888)

    print(f'Send: {message!r}')
    writer.write(message.encode())

    data = await reader.read(100)
    print(f'Received: {data.decode()!r}')

    print('Close the connection')
    writer.close()


asyncio.run(tcp_echo_client('Hello World!'))
```

### 获取响应头

```python
import asyncio
import urllib.parse


async def print_http_headers(request_url):
    request_url = urllib.parse.urlsplit(request_url)
    if request_url.scheme == 'https':
        reader, writer = await asyncio.open_connection(
            request_url.hostname, 443, ssl=True)
    else:
        reader, writer = await asyncio.open_connection(
            request_url.hostname, 80)

    query = (
        f"HEAD {request_url.path or '/'} HTTP/1.0\r\n"
        f"Host: {request_url.hostname}\r\n"
        f"User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:88.0) Gecko/20100101 Firefox/88.0\r\n"
        f"\r\n"
    )

    writer.write(query.encode('latin-1'))
    print(f'get url {request_url}')
    while True:
        line = await reader.readline()
        if not line:
            break

        line = line.decode('latin1').rstrip()
        if line:
            print(f'HTTP header> {line}')

    # Ignore the body, close the socket
    writer.close()


async def main():
    urls = ['https://www.bing.com', 'https://www.baidu.com', 'https://www.w3school.com.cn', 'https://www.runoob.com']
    tasks = [asyncio.create_task(print_http_headers(url)) for url in urls]
    for task in tasks:
        await task


if __name__ == '__main__':
    asyncio.run(main())
```

Last Modified 2021-05-28
