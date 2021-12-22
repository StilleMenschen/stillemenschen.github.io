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

## 速度差异总和

```python
# O(n * log(n)) time | O(1) space
def tandem_bicycle(red_shirt_speeds, blue_shirt_speeds, fastest):
    sum_of_speeds = 0
    if fastest:
        red_shirt_speeds.sort()
    else:
        red_shirt_speeds.sort(reverse=True)
    blue_shirt_speeds.sort(reverse=True)

    min_length = min(len(red_shirt_speeds), len(blue_shirt_speeds))

    for i in range(min_length):
        red_shirt = red_shirt_speeds[i]
        blue_shirt = blue_shirt_speeds[i]
        sum_of_speeds += max(red_shirt, blue_shirt)

    return sum_of_speeds


if __name__ == '__main__':
    blue = [3, 3, 4, 6, 1, 2]
    red = [1, 2, 1, 9, 12, 3]
    print(tandem_bicycle(red, blue, False))
```

## 任务分配

```python
def get_task_durations_to_indices(tasks):
    task_durations_to_indices = {}

    for idx, task_duration in enumerate(tasks):
        if task_duration in task_durations_to_indices:
            task_durations_to_indices[task_duration].append(idx)
        else:
            task_durations_to_indices[task_duration] = [idx]

    return task_durations_to_indices


def get_task_index_from_indices(idx, sorted_tasks, task_durations_to_indices):
    task_duration = sorted_tasks[idx]
    indices_with_task_duration = task_durations_to_indices[task_duration]
    return indices_with_task_duration.pop()


# O(n * log(n)) time | O(n) space - where n is the number of tasks
def task_assignment1(k, tasks):
    paired_tasks = []
    task_durations_to_indices = get_task_durations_to_indices(tasks)

    last_task_index_from_tasks = len(tasks) - 1
    sorted_tasks = sorted(tasks)
    for idx in range(k):
        task_1_index = get_task_index_from_indices(idx, sorted_tasks, task_durations_to_indices)
        task_2_duration_index = last_task_index_from_tasks - idx
        task_2_index = get_task_index_from_indices(task_2_duration_index, sorted_tasks,
                                                   task_durations_to_indices)
        paired_tasks.append([task_1_index, task_2_index])

    return paired_tasks


# O(n * log(n)) time | O(n) space - where n is the number of tasks
def task_assignment2(k, tasks):
    paired_tasks = []
    sorted_tasks_with_indices = [(duration, idx,) for idx, duration in enumerate(tasks)]
    sorted_tasks_with_indices.sort(key=lambda x: x[0])

    last_task_index_from_tasks = len(tasks) - 1
    for idx in range(k):
        task_1_index = sorted_tasks_with_indices[idx][1]
        task_2_index = sorted_tasks_with_indices[last_task_index_from_tasks - idx][1]
        paired_tasks.append([task_1_index, task_2_index])

    return paired_tasks


if __name__ == '__main__':
    source = [2, 1, 3, 4, 5, 13, 12, 9, 11, 10, 6, 7, 14, 8]
    print(task_assignment1(7, source))
    print(task_assignment2(7, source))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

unordered_map<int, vector<int>> getTaskDurationsToIndices(vector<int> tasks);

// O(n * log(n)) time | O(n) space - where n is the number of tasks
vector<vector<int>> taskAssignment(int k, vector<int> tasks)
{
    vector<vector<int>> pairedTasks;
    unordered_map<int, vector<int>> taskDurationToIndices = getTaskDurationsToIndices(tasks);

    vector<int> sortedTasks(tasks);
    sort(sortedTasks.begin(), sortedTasks.end());

    for (int idx = 0; idx < k; idx++)
    {
        int task1Duration = sortedTasks[idx];
        vector<int> *indicesWithTask1Duration = &taskDurationToIndices[task1Duration];
        int task1Index = indicesWithTask1Duration->back();
        indicesWithTask1Duration->pop_back();

        int task2SortedIndex = tasks.size() - 1 - idx;
        int task2Duration = sortedTasks[task2SortedIndex];
        vector<int> *indicesWithTask2Duration = &taskDurationToIndices[task2Duration];
        int task2Index = indicesWithTask2Duration->back();
        indicesWithTask2Duration->pop_back();

        pairedTasks.push_back(vector<int>{task1Index, task2Index});
    }

    return pairedTasks;
}

unordered_map<int, vector<int>> getTaskDurationsToIndices(vector<int> tasks)
{
    unordered_map<int, vector<int>> taskDurationToIndices;

    const int tasksSize = tasks.size();
    for (int idx = 0; idx < tasksSize; idx++)
    {
        auto taskDuration = tasks[idx];
        if (taskDurationToIndices.find(taskDuration) !=
            taskDurationToIndices.end())
        {
            taskDurationToIndices[taskDuration].push_back(idx);
        }
        else
        {
            taskDurationToIndices[taskDuration] = vector<int>{idx};
        }
    }

    return taskDurationToIndices;
}

int main()
{
    vector<vector<int>> result = taskAssignment(7, {2, 1, 3, 4, 5, 13, 12, 9, 11, 10, 6, 7, 14, 8});
    for (vector<int> pair : result)
    {
        cout << pair[0] << ", " << pair[1] << endl;
    }
    return 0;
}
```

Last Modified 2021-12-22
