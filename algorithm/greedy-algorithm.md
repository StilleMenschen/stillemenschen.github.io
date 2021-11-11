# 贪心算法

## 最小等待时间

```python
# O(n * log(n)) time | O(1) space
def minimum_waiting_time(queries):
    if not queries or not len(queries):
        return 0

    queries.sort()
    total_waiting_time = 0

    for idx, duration in enumerate(queries):
        queries_left = len(queries) - (idx + 1)
        total_waiting_time += duration * queries_left

    return total_waiting_time


if __name__ == '__main__':
    sources = [3, 2, 1, 2, 6]
    print(minimum_waiting_time(sources))
```

```java
import java.util.Arrays;
import java.util.Objects;

public class Algorithm {
    // O(n * log(n)) time | O(1) space
    public int minimumWaitingTime(int[] queries) {
        if (Objects.isNull(queries) || queries.length == 0) {
            return -1;
        }
        Arrays.sort(queries);
        int totalTime = 0, currentTime = 0;
        for (int i = 1; i < queries.length; i++) {
            currentTime += queries[i - 1];
            totalTime += currentTime;
        }
        return totalTime;
    }

    public static void main(String[] args) {
        final int[] source = {3, 2, 1, 2, 6};
        final Algorithm algorithm = new Algorithm();
        System.out.println(algorithm.minimumWaitingTime(source));
    }
}
```

Last Modified 2021-11-11
