# 延迟和吞吐量

## 延迟

在系统中完成某项操作所需的时间。大多数情况下，此度量是一个持续时间，如毫秒或秒。你应该知道这些数量级：

- 从内存中读取 1 MB 数据: 250 μs (0.25 ms)
- 从 SSD 读取 1 MB 数据: 1,000 μs (1 ms)
- 通过网络传输 1 MB 数据: 10,000 μs (10 ms)
- 从 HDD 读取 1MB 数据: 20,000 μs (20 ms)
- Inter-Continental Round Trip: 150,000 μs (150 ms)

## 吞吐量

系统在每个时间单位可以正确处理的操作数。例如，服务器的吞吐量通常可以用每秒请求数（RPS 或 QPS）来衡量。

Last Modified 2021-12-27
