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

## 按身高排列

```python
# O(nlog(n)) time | O(1) space - where n is the number of students
def class_photos(red_shirt_heights, blue_shirt_heights):
    red_shirt_heights.sort(reverse=True)
    blue_shirt_heights.sort(reverse=True)

    shirt_color_in_first_row = "RED" if red_shirt_heights[0] < blue_shirt_heights[0] else "BLUE"
    for idx in range(len(red_shirt_heights)):
        red_shirt_height = red_shirt_heights[idx]
        blue_shirt_height = blue_shirt_heights[idx]

        if shirt_color_in_first_row == "RED":
            if red_shirt_height >= blue_shirt_height:
                return False
        else:
            if blue_shirt_height >= red_shirt_height:
                return False
    return True


if __name__ == '__main__':
    blue = [5, 8, 1, 3, 4]
    red = [6, 9, 2, 4, 5]
    print(class_photos(red, blue))
```

Last Modified 2021-11-14
