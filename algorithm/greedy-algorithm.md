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
# O(n * log(n)) time | O(1) space - where n is the number of students
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

## 验证有效起始城市

现有几座城市是相互靠近且循环相连的，给定一辆每加仑油耗能行驶指定英里的汽车，汽车每到一个城市都有加油站可以加油，给出两个数组和一个整数，
分别为：表示每座城市距离下一座城市的距离（数组）、每座城市可以提供多少加仑的汽油（数组），汽车每一加仑可以行驶的英里数。
每座城市以数组的下标来表示，找出可以刚刚好走完所有城市的起始城市位置

```python
# O(n^2) time | O(1) space - where n is the number of cities
def valid_starting_city1(distances, fuel, mpg):
    number_of_cities = len(distances)

    for start_city_idx in range(number_of_cities):
        miles_remaining = 0

        for current_city_idx in range(start_city_idx, start_city_idx + number_of_cities):
            if miles_remaining < 0:
                continue

            current_city_idx = current_city_idx % number_of_cities

            fuel_from_current_city = fuel[current_city_idx]
            distance_to_next_city = distances[current_city_idx]
            miles_remaining += fuel_from_current_city * mpg - distance_to_next_city

        if miles_remaining >= 0:
            return start_city_idx

    # This line should never be reached if the inputs are correct.
    return -1


# O(n) time | O(1) space - where n is the number of cities
def valid_starting_city2(distances, fuel, mpg):
    number_of_cities = len(distances)
    miles_remaining = 0

    index_of_starting_city_candidate = 0
    miles_remaining_at_starting_city_candidate = 0

    for city_idx in range(1, number_of_cities):
        distance_from_previous_city = distances[city_idx - 1]
        fuel_from_previous_city = fuel[city_idx - 1]
        miles_remaining += fuel_from_previous_city * mpg - distance_from_previous_city

        if miles_remaining < miles_remaining_at_starting_city_candidate:
            miles_remaining_at_starting_city_candidate = miles_remaining
            index_of_starting_city_candidate = city_idx

    return index_of_starting_city_candidate


if __name__ == '__main__':
    print(valid_starting_city1([5, 25, 15, 10, 15], [1, 2, 1, 0, 3], 10))
    print(valid_starting_city2([5, 25, 15, 10, 15], [1, 2, 1, 0, 3], 10))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(n^2) time | O(1) space - where n is the number of cities
int validStartingCity1(vector<int> distances, vector<int> fuel, int mpg)
{
    int numberOfCities = distances.size();

    for (int startCityIdx = 0; startCityIdx < numberOfCities; startCityIdx++)
    {
        int milesRemaining = 0;

        for (int currentCityIdx = startCityIdx;
             currentCityIdx < startCityIdx + numberOfCities; currentCityIdx++)
        {
            if (milesRemaining < 0)
                continue;

            int currentCityIdxRotated = currentCityIdx % numberOfCities;

            int fuelFromCurrentCity = fuel[currentCityIdxRotated];
            int distanceToNextCity = distances[currentCityIdxRotated];
            milesRemaining += fuelFromCurrentCity * mpg - distanceToNextCity;
        }

        if (milesRemaining >= 0)
            return startCityIdx;
    }

    // This line should never be reached if the inputs are correct.
    return -1;
}

// O(n) time | O(1) space - where n is the number of cities
int validStartingCity2(vector<int> distances, vector<int> fuel, int mpg)
{
    int numberOfCities = distances.size();
    int milesRemaining = 0;

    int indexOfStartingCityCandidate = 0;
    int milesRemainingAtStartingCityCandidate = 0;

    for (int cityIdx = 1; cityIdx < numberOfCities; cityIdx++)
    {
        int distanceFromPreviousCity = distances[cityIdx - 1];
        int fuelFromPreviousCity = fuel[cityIdx - 1];
        milesRemaining += fuelFromPreviousCity * mpg - distanceFromPreviousCity;

        if (milesRemaining < milesRemainingAtStartingCityCandidate)
        {
            milesRemainingAtStartingCityCandidate = milesRemaining;
            indexOfStartingCityCandidate = cityIdx;
        }
    }

    return indexOfStartingCityCandidate;
}

int main()
{
    cout << validStartingCity1({5, 25, 15, 10, 15}, {1, 2, 1, 0, 3}, 10);
    cout << validStartingCity2({5, 25, 15, 10, 15}, {1, 2, 1, 0, 3}, 10);
    return 0;
}
```

Last Modified 2021-12-24
