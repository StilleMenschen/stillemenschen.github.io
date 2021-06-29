# 数组

## 验证子序列

假设有一个主序列存在一个子序列，子序列中的所有元素都存在于主序列中，判断一个序列是否为另一个序列的子序列

```python
# O(n) time | O(1) space
def validate_subsequence1(array, sequence):
    arr_idx = 0
    seq_idx = 0
    while arr_idx < len(array) and seq_idx < len(sequence):
        if array[arr_idx] == sequence[seq_idx]:
            seq_idx += 1
        arr_idx += 1
    return seq_idx == len(sequence)


# O(n) time | O(1) space
def validate_subsequence2(array, sequence):
    seq_idx = 0
    for value in array:
        if seq_idx == len(sequence):
            break
        if sequence[seq_idx] == value:
            seq_idx += 1
    return seq_idx == len(sequence)


if __name__ == '__main__':
    a = [5, 1, 22, 25, 6, -1, 8, 10]
    b = [1, 6, -1, 10]
    print(validate_subsequence1(a, b))
    print(validate_subsequence2(a, b))
```

```cpp
#include<vector>
#include<iostream>

using namespace std;

// O(n) time | O(1) space - where n is the length of the array
bool isValidSubsequence1(vector<int> array, vector<int> sequence)
{
    int arrIdx = 0;
    int seqIdx = 0;
    const int arrSize = array.size();
    const int seqSize = sequence.size();
    while (arrIdx < arrSize && seqIdx < seqSize)
    {
        if (array[arrIdx] == sequence[seqIdx])
        {
            seqIdx++;
        }
        arrIdx++;
    }
    return seqIdx == seqSize;
}


// O(n) time | O(1) space - where n is the length of the array
bool isValidSubsequence2(vector<int> array, vector<int> sequence)
{
    int seqIdx = 0;
    const int seqSize = sequence.size();
    for (auto value : array)
    {
        if (seqIdx == seqSize)
            break;
        if (sequence[seqIdx] == value)
            seqIdx++;
    }
    return seqIdx == seqSize;
}

int main()
{
    const vector<int> array = {5, 1, 22, 25, 6, -1, 8, 10};
    const vector<int> sequence = {1, 6, -1, 10};
    cout << isValidSubsequence1(array, sequence) << endl;
    cout << isValidSubsequence2(array, sequence) << endl;
    return 0;
}
```

## 两数求和

假设在一个序列中存在两个数相加的值等于一个给出的数，现在要快速找出序列中的这两个数并返回其值

```python
# O(n^2) time | O(1) space
def two_number_sum1(array, target_sum):
    for i in range(len(array) - 1):
        first_num = array[i]
        for j in range(i + 1, len(array)):
            second_num = array[j]
            if target_sum == first_num + second_num:
                return first_num, second_num
    return tuple()


# O(n) time | O(n) space
def two_number_sum2(array, target_sum):
    nums = {}
    for num in array:
        potential_match = target_sum - num
        if potential_match in nums:
            return potential_match, num
        else:
            nums[num] = 1
    return tuple()


# O(n*log(n)) time | O(1) space
def two_number_sum3(array, target_sum):
    array.sort()
    right_idx = len(array) - 1
    left_idx = 0
    while left_idx < right_idx:
        current_sum = array[left_idx] + array[right_idx]
        if target_sum == current_sum:
            return array[left_idx], array[right_idx]
        elif target_sum > current_sum:
            left_idx += 1
        elif target_sum < current_sum:
            right_idx -= 1
    return tuple()


if __name__ == '__main__':
    a = [3, 5, -4, 8, 11, 1, -1, 6]
    print(two_number_sum1(a, -3))
    print(two_number_sum2(a, 10))
    print(two_number_sum3(a, 13))
```

## 三数求和

在一个序列中找出符合相加的和等于某个指定的数的三个数，这三个数不能重复，需要找出所有可能的组合

```python
# O(n^2) time | O(n) space
def three_number_sum(array, target_sum):
    array.sort()
    triplets = list()
    length = len(array)
    for i in range(length - 2):
        left_idx = i + 1
        right_idx = length - 1
        while left_idx < right_idx:
            current_sum = array[i] + array[left_idx] + array[right_idx]
            if target_sum == current_sum:
                triplets.append((array[i], array[left_idx], array[right_idx],))
                left_idx += 1
                right_idx -= 1
            elif target_sum > current_sum:
                left_idx += 1
            elif target_sum < current_sum:
                right_idx -= 1
    return triplets


if __name__ == '__main__':
    a = [12, 3, 1, 2, -6, 5, -8, 6]
    print(three_number_sum(a, 0))
```

## 最小差异

在两个序列中分别找出序列 1 和序列 2 中相差最小的两个数

```python
# O(n*log(n) + m*log(m)) time | O(1) space
def smallest_difference(array1, array2):
    array1.sort()
    array2.sort()
    len1 = len(array1)
    len2 = len(array2)
    idx1 = 0
    idx2 = 0
    smallest = float('inf')
    current = float('inf')
    smallest_pair = tuple()
    while idx1 < len1 and idx2 < len2:
        first_num = array1[idx1]
        second_num = array2[idx2]
        if first_num < second_num:
            current = second_num - first_num
            idx1 += 1
        elif first_num > second_num:
            current = first_num - second_num
            idx2 += 1
        else:
            return first_num, second_num
        if current < smallest:
            smallest = current
            smallest_pair = (first_num, second_num,)
    return smallest_pair


if __name__ == '__main__':
    a = [-1, 5, 10, 20, 28, 3]
    b = [26, 134, 135, 15, 17]
    print(smallest_difference(a, b))
```

## 移动元素至末尾

指定一个序列和一个存在于序列中的数，将所有与指定的数相等的数全部移动到序列的末尾

```python
# O(n) time | O(1) space
def move_element_ti_end(array, to_move):
    i = 0
    j = len(array) - 1
    while i < j:
        while i < j and array[j] == to_move:
            j -= 1
        if array[i] == to_move:
            array[i], array[j] = array[j], array[i]
        i += 1
    return array


if __name__ == '__main__':
    a = [4, 1, 5, 8, 8, 8, 5, 8, 8, 3]
    print(move_element_ti_end(a, 8))
```

## 单调数组

判断一个整型序列，是否是升序排序或者降序排序，如果满足两个条件中的一个，就表示它是单调的

```python
# O(n) time | O(1) space
def is_monotonic(array):
    length = len(array)
    if length <= 2:
        return True
    direction = array[1] - array[0]
    for i in range(2, length):
        if direction == 0:
            direction = array[i] - array[i - 1]
            continue
        if not break_direction(direction, array[i - 1], array[i]):
            return False
    return True


def break_direction(direction, previous_int, current_int):
    difference = current_int - previous_int
    if direction > 0:
        return difference > 0
    return difference < 0


if __name__ == '__main__':
    a = [872, 743, 65, 30, 14, 10, 5, 3, 1, -3]
    # a.reverse()
    print(is_monotonic(a))
```

## 螺旋线

将一个类似顺时针向内螺旋的特殊二维序列转为一维序列

```python
# O(n) time | O(n) space
def spiral_traverse1(array):
    result = list()
    start_row, end_row = 0, len(array) - 1
    start_col, end_col = 0, len(array[0]) - 1
    while start_row <= end_row and start_col <= end_col:
        for col in range(start_col, end_col + 1):
            result.append(array[start_row][col])
        for row in range(start_row + 1, end_row + 1):
            result.append(array[row][end_col])
        # for col in reversed(range(start_col, end_col)):
        for col in range(end_col - 1, start_col - 1, -1):
            result.append(array[end_row][col])
        # for row in reversed(range(start_row + 1, end_row)):
        for row in range(end_row - 1, start_row, -1):
            result.append(array[row][start_col])
        start_row += 1
        end_row -= 1
        start_col += 1
        end_col -= 1
    return result


# O(n) time | O(n) space
def spiral_traverse2(array):
    result = list()
    spiral_fill(array, 0, len(array) - 1, 0, len(array[0]) - 1, result)
    return result


def spiral_fill(array, start_row, end_row, start_col, end_col, result):
    if start_row > end_row and start_col > end_col:
        return False
    for col in range(start_col, end_col + 1):
        result.append(array[start_row][col])
    for row in range(start_row + 1, end_row + 1):
        result.append(array[row][end_col])
    for col in range(end_col - 1, start_col - 1, -1):
        result.append(array[end_row][col])
    for row in range(end_row - 1, start_row, -1):
        result.append(array[row][start_col])
    spiral_fill(array, start_row + 1, end_row - 1, start_col + 1, end_col - 1, result)


if __name__ == '__main__':
    a = [
        [ 1,  2,  3, 4],
        [12, 13, 14, 5],
        [11, 16, 15, 6],
        [10,  9,  8, 7],
    ]
    print(spiral_traverse1(a))
    print(spiral_traverse2(a))
```

## 最宽（长）波峰

给出一个整数序列，将序列的索引作为 x 坐标，每个索引的数值作为 y 坐标，将所有的点连接起来形成一个波谱，找出一个波谱中波峰
宽度最长的

```python
# O(n) time | O(1) space
def longest_peak(array):
    longest_peak_length = 0
    length = len(array)
    i = 0
    while i < length - 1:
        is_peak = array[i - 1] < array[i] and array[i] > array[i + 1]
        if not is_peak:
            i += 1
            continue

        left_idx = i - 2
        while left_idx >= 0 and array[left_idx] < array[left_idx + 1]:
            left_idx -= 1
        right_idx = i + 2
        while right_idx < length and array[right_idx] < array[right_idx - 1]:
            right_idx += 1

        current_peak_length = right_idx - left_idx - 1
        longest_peak_length = max(longest_peak_length, current_peak_length)
        i = right_idx
    return longest_peak_length


if __name__ == '__main__':
    a = [1, 2, 3, 3, 4, 0, 10, 6, 5, -1, -3, 2, 3]
    print(longest_peak(a))
```

## 数组消费者

给定一个整数序列，求出另一个序列满足第 n 个元素是除了其本身之外的其它元素的乘积

```python
# O(n^2) time | O(n) space
def array_of_products1(array):
    length = len(array)
    products = [1 for _ in range(length)]

    for i in range(length):
        running_product = 1
        for j in range(length):
            if i != j:
                running_product *= array[j]
        products[i] = running_product

    return products


# O(n) time | O(n) space
def array_of_products2(array):
    length = len(array)
    products = [1 for _ in range(length)]
    left_products = [1 for _ in range(length)]
    right_products = [1 for _ in range(length)]

    left_running_product = 1
    for i in range(length):
        left_products[i] = left_running_product
        left_running_product *= array[i]

    right_running_product = 1
    # for i in reversed(range(length)):
    for i in range(length - 1, -1, -1):
        right_products[i] = right_running_product
        right_running_product *= array[i]

    for i in range(length):
        products[i] = left_products[i] * right_products[i]

    return products


# O(n) time | O(n) space
def array_of_products3(array):
    length = len(array)
    products = [1 for _ in range(length)]

    left_running_product = 1
    for i in range(length):
        products[i] = left_running_product
        left_running_product *= array[i]

    right_running_product = 1
    # for i in reversed(range(length)):
    for i in range(length - 1, -1, -1):
        products[i] *= right_running_product
        right_running_product *= array[i]

    return products


if __name__ == '__main__':
    a = [1, 5, 10, 20]
    print(array_of_products1(a))
    print(array_of_products2(a))
    a = [5, 1, 4, 2]
    print(array_of_products3(a))
```

## 四数求和

在一个序列中找出符合相加的和等于某个指定的数的四个数，这四个数不能重复，需要找出所有可能的组合

```python
# O(n^2) time | O(n^2) space
def four_number_sum(array, target_sum):
    length = len(array)
    all_pair_sums = dict()
    quadruplets = list()
    for i in range(1, length - 1):
        for j in range(i + 1, length):
            current_sum = array[i] + array[j]
            difference = target_sum - current_sum
            if difference in all_pair_sums:
                for pair in all_pair_sums[difference]:
                    quadruplets.append(pair + [array[i], array[j]])
        for k in range(0, i):
            current_sum = array[i] + array[k]
            if current_sum not in all_pair_sums:
                all_pair_sums[current_sum] = [[array[k], array[i]]]
            else:
                all_pair_sums[current_sum].append([array[k], array[i]])
    return quadruplets


if __name__ == '__main__':
    a = [7, 6, 4, -1, 1, 2]
    print(four_number_sum(a, 16))
```

## 子序列排序

编写一个函数，该函数接受一个至少包含两个整数的序列，并返回一个包含输入序列中最小子序列的开始和结束索引的序列，该子序列进
行排序后可以使整个输入序列按升序排序。如果输入数组已经排序，函数应该返回 [-1, -1]

```python
# O(n) time | O(1) space
def sub_array_sort(array):
    min_out_of_order = float("inf")
    max_out_of_order = float("-inf")
    for i in range(len(array)):
        num = array[i]
        if is_out_of_order(i, num, array):
            min_out_of_order = min(min_out_of_order, num)
            max_out_of_order = max(max_out_of_order, num)
    if min_out_of_order == float("inf"):
        return [-1, -1]
    sub_array_left_idx = 0
    while min_out_of_order >= array[sub_array_left_idx]:
        sub_array_left_idx += 1
    sub_array_right_idx = len(array) - 1
    while max_out_of_order <= array[sub_array_right_idx]:
        sub_array_right_idx -= 1
    return sub_array_left_idx, sub_array_right_idx


def is_out_of_order(i, num, array):
    if i == 0:
        return num > array[i + 1]
    if i == len(array) - 1:
        return num < array[i - 1]
    return num > array[i + 1] or num < array[i - 1]


if __name__ == '__main__':
    a = [1, 2, 4, 7, 10, 11, 7, 12, 6, 7, 16, 18, 19]
    print(sub_array_sort(a))
```

## 最大连续范围

找出一个序列中最长的连续数字范围，并返回开始值和结束值

```python
# O(n) time | O(n) space
def largest_range(array):
    best_range = list()
    longest_length = 0
    nums = dict()
    for num in array:
        nums[num] = True
    for num in array:
        if not nums[num]:
            continue
        nums[num] = False
        current_length = 1
        left = num - 1
        right = num + 1
        while left in nums:
            nums[left] = False
            current_length += 1
            left -= 1
        while right in nums:
            nums[right] = False
            current_length += 1
            right += 1
        if current_length > longest_length:
            longest_length = current_length
            best_range = [left + 1, right - 1]
    return best_range


if __name__ == '__main__':
    a = [1, 11, 3, 0, 15, 5, 2, 4, 10, 7, 12, 6]
    print(largest_range(a))
```

## 最少奖励

老师有一系列学生的分数，老师必须根据学生的分数购买奖励给他们。奖励金额必须遵循 2 个标准：1. 每个学生必须至少获得一项奖励
；2. 两个相邻学生中排名较高的学生必须比另一个获得更多的奖励。

```python
# O(n^2) time | O(n) space
def min_rewards1(scores):
    rewards = [1 for _ in scores]
    for i in range(1, len(scores)):
        j = i - 1
        if scores[i] > scores[j]:
            rewards[i] = rewards[j] + 1
        else:
            while j >= 0 and scores[j] > scores[j + 1]:
                rewards[j] = max(rewards[j], rewards[j + 1] + 1)
                j -= 1
    return sum(rewards)


# O(n^2) time | O(n) space
def min_rewards4(scores):
    length = len(scores)
    rewards = [1 for _ in scores]
    position = 0
    while position < length:
        plus_rewards(position, rewards, scores)
        position += 1
    return sum(rewards)


def plus_rewards(i, rewards, array):
    if i == 0 or i == len(array) - 1:
        return False
    n = i - 1
    if array[n] > array[i]:
        rewards[n] = rewards[n] + 1
        plus_rewards(n, rewards, array)
    n = i + 1
    if array[n] > array[i]:
        rewards[n] = rewards[n] + 1
        plus_rewards(n, rewards, array)


# O(n) time | O(n) space
def min_rewards2(scores):
    rewards = [1 for _ in scores]
    local_min_idx_list = get_local_min_idx(scores)
    for local_min_idx in local_min_idx_list:
        expand_from_local_min_idx(local_min_idx, scores, rewards)
    return sum(rewards)


def get_local_min_idx(array):
    length = len(array)
    if length == 1:
        return [0]
    local_min_idx = []
    for i in range(length):
        if i == 0 and array[i] < array[i + 1]:
            local_min_idx.append(i)
        if i == length - 1 and array[i] < array[i - 1]:
            local_min_idx.append(i)
        if i == 0 or i == length - 1:
            continue
        if array[i] < array[i + 1] and array[i] + array[i - 1]:
            local_min_idx.append(i)
    return local_min_idx


def expand_from_local_min_idx(local_min_idx, scores, rewards):
    left_idx = local_min_idx - 1
    while left_idx >= 0 and scores[left_idx] > scores[left_idx + 1]:
        rewards[left_idx] = max(rewards[left_idx], rewards[left_idx + 1] + 1)
        left_idx -= 1
    right_idx = local_min_idx + 1
    length = len(scores)
    while right_idx < length and scores[right_idx] > scores[right_idx - 1]:
        rewards[right_idx] = rewards[right_idx - 1] + 1
        right_idx += 1


# O(n) time | O(n) space
def min_rewards3(scores):
    rewards = [1 for _ in range(len(scores))]
    for i in range(1, len(scores)):
        if scores[i] > scores[i - 1]:
            rewards[i] = rewards[i - 1] + 1
    # for i in reversed(range(len(scores) - 1)):
    for i in range(len(scores) - 2, -1, -1):
        if scores[i] > scores[i + 1]:
            rewards[i] = max(rewards[i], rewards[i + 1] + 1)
    return sum(rewards)


if __name__ == '__main__':
    a = [8, 4, 2, 1, 3, 6, 7, 9, 5]
    print(min_rewards1(a))
    print(min_rewards2(a))
    print(min_rewards3(a))
    print(min_rewards4(a))
```

## Z 型遍历

```python
# O(n) time | O(n) space
def zigzag_traverse(array):
    height = len(array) - 1
    width = len(array[0]) - 1
    result = list()
    row, col = 0, 0
    going_down = True
    while not is_out_of_bounds(row, col, height, width):
        result.append(array[row][col])
        if going_down:
            if col == 0 or row == height:
                going_down = False
                if row == height:
                    col += 1
                else:
                    row += 1
            else:
                row += 1
                col -= 1
        else:
            if row == 0 or col == width:
                going_down = True
                if col == width:
                    row += 1
                else:
                    col += 1
            else:
                row -= 1
                col += 1
    return result


def is_out_of_bounds(row, col, height, width):
    return row < 0 or row > height or col < 0 or col > width


if __name__ == '__main__':
    a = [
        [ 1,  3,  4, 10, 11],
        [ 2,  5,  9, 12, 19],
        [ 6,  8, 13, 18, 20],
        [ 7, 14, 17, 21, 24],
        [15, 16, 22, 23, 25],
    ]
    print(zigzag_traverse(a))
```

## Apartment Hunting

```python
# O(b^2 * r) time | O(b) space
def apartment_hunting1(blocks, request_lists):
    max_distances_at_blocks = [float('-inf') for _ in blocks]
    length = len(blocks)
    for i in range(length):
        for req in request_lists:
            closest_req_distance = float('inf')
            for j in range(length):
                if blocks[j][req]:
                    closest_req_distance = min(closest_req_distance, distance_between(i, j))
            max_distances_at_blocks[i] = max(max_distances_at_blocks[i], closest_req_distance)
    return get_idx_at_min_value(max_distances_at_blocks)


def get_idx_at_min_value(array):
    idx_at_min_value = 0
    min_value = float('inf')
    for i in range(len(array)):
        current_value = array[i]
        if current_value < min_value:
            min_value = current_value
            idx_at_min_value = i
    return idx_at_min_value


def distance_between(a, b):
    return abs(a - b)


# O(br) time | O(b * r) space
def apartment_hunting2(blocks, request_lists):
    min_distances_from_blocks = list(map(lambda req: get_min_distances(blocks, req), request_lists))
    max_distance_at_blocks = get_max_distance_at_blocks(blocks, min_distances_from_blocks)
    return get_idx_at_min_value(max_distance_at_blocks)


def get_min_distances(blocks, req):
    min_distances = [0 for _ in blocks]
    closest_req_idx = float('inf')
    for i in range(len(blocks)):
        if blocks[i][req]:
            closest_req_idx = i
        min_distances[i] = distance_between(i, closest_req_idx)
    for i in reversed(range(len(blocks))):
        if blocks[i][req]:
            closest_req_idx = i
        min_distances[i] = min(min_distances[i], distance_between(i, closest_req_idx))
    return min_distances


def get_max_distance_at_blocks(blocks, min_distance_form_blocks):
    max_distance_at_blocks = [0 for _ in blocks]
    for i in range(len(blocks)):
        min_distance_at_block = list(map(lambda distances: distances[i], min_distance_form_blocks))
        max_distance_at_blocks[i] = max(min_distance_at_block)
    return max_distance_at_blocks


if __name__ == '__main__':
    block = [
        {'School': True, 'Gym': False, 'Store': False},
        {'School': False, 'Gym': True, 'Store': False},
        {'School': True, 'Gym': True, 'Store': False},
        {'School': True, 'Gym': False, 'Store': False},
        {'School': True, 'Gym': False, 'Store': True}
    ]
    r = ['Gym', 'School', 'Store']
    print(apartment_hunting1(block, r))
    print(apartment_hunting2(block, r))
```

## 日程表匹配

在两个日程表以及对应日程表的工作时间范围内，找出符合指定会议时间的可用会议时段

```python
# O(c1 + c2) time | O(c1 + c2) space
def calendar_matching(calendar1, daily_bounds1, calendar2, daily_bounds2, meeting_duration):
    updated_calendar1 = update_calendar(calendar1, daily_bounds1)
    updated_calendar2 = update_calendar(calendar2, daily_bounds2)
    merged_calendar = merge_calendar(updated_calendar1, updated_calendar2)
    flattened_calendar = flatten_calendar(merged_calendar)
    return get_matching_availabilities(flattened_calendar, meeting_duration)


def update_calendar(calendar, daily_bounds):
    updated_calendar = calendar[:]
    updated_calendar.insert(0, ('0:00', daily_bounds[0],))
    updated_calendar.append((daily_bounds[1], '23:59',))
    return list(map(lambda m: (time_to_minutes(m[0]), time_to_minutes(m[1]),), updated_calendar))


def merge_calendar(calendar1, calendar2):
    merged = list()
    i, j = 0, 0
    length1 = len(calendar1)
    length2 = len(calendar2)
    while i < length1 and j < length2:
        meeting1, meeting2 = calendar1[i], calendar2[j]
        if meeting1[0] < meeting2[0]:
            merged.append(meeting1)
            i += 1
        else:
            merged.append(meeting2)
            j += 1
    while i < length1:
        merged.append(calendar1[i])
        i += 1
    while j < length2:
        merged.append(calendar2[j])
        j += 1
    return merged


def flatten_calendar(calendar):
    flattened = [calendar[0][:]]
    for i in range(1, len(calendar)):
        current_meeting = calendar[i]
        previous_meeting = flattened[-1]
        current_start, current_end = current_meeting
        previous_start, previous_end = previous_meeting
        if previous_end >= current_start:
            new_previous_meeting = (previous_start, max(previous_end, current_end),)
            flattened[-1] = new_previous_meeting
        else:
            flattened.append(current_meeting[:])
    return flattened


def get_matching_availabilities(calendar, meeting_duration):
    matching_availabilities = list()
    for i in range(1, len(calendar)):
        start = calendar[i - 1][1]
        end = calendar[i][0]
        availabilities_duration = end - start
        if availabilities_duration >= meeting_duration:
            matching_availabilities.append((start, end,))
    return list(map(lambda m: (minutes_to_time(m[0]), minutes_to_time(m[1]),), matching_availabilities))


def time_to_minutes(time):
    hours, minutes = list(map(int, time.split(':')))
    return hours * 60 + minutes


def minutes_to_time(minutes):
    hours = minutes // 60
    minute = minutes % 60
    hours_string = str(hours)
    minute_string = '0' + str(minute) if minute < 10 else str(minute)
    return ':'.join((hours_string, minute_string,))


if __name__ == '__main__':
    cal1 = [
        ('9:00', '10:30',),
        ('12:00', '13:00',),
        ('16:00', '18:00',)
    ]
    db1 = ('9:00', '20:00',)
    cal2 = [
        ('10:00', '11:30',),
        ('12:30', '14:30',),
        ('14:30', '15:00',),
        ('16:00', '17:00',)
    ]
    db2 = ('10:00', '18:30',)
    duration = 30
    print(calendar_matching(cal1, db1, cal2, db2, duration))
```

## 瀑布流

假设有一个二维序列表示一个向下的瀑布，瀑布的某些地方存在石头会阻碍水流，给定一个开始位置让 100%的水向下流动，遇到石头一
分为二向左和向右流动，遇到可以向下流动的位置后继续往下，计算出最后到达瀑布底部剩余的水量百分比

```python
# O(w^2 * h) time | O(w) space
def waterfall_streams(array, source):
    row_above = array[0][:]
    row_above[source] = -1

    for row in range(1, len(array)):
        current_row = array[row][:]

        for idx in range(len(row_above)):
            value_above = row_above[idx]

            has_water_above = value_above < 0
            has_block = current_row[idx] == 1

            if not has_water_above:
                continue

            if not has_block:
                current_row[idx] += value_above
                continue

            split_water = value_above / 2
            right_idx = idx
            length = len(row_above) - 1
            while right_idx < length:
                right_idx += 1
                if row_above[right_idx] == 1:
                    break
                if current_row[right_idx] != 1:
                    current_row[right_idx] += split_water
                    break

            left_idx = idx
            while left_idx > 1:
                left_idx -= 1
                if row_above[left_idx] == 1:
                    break
                if current_row[left_idx] != 1:
                    current_row[left_idx] += split_water
                    break
        row_above = current_row

    final_percentages = list(map(lambda num: num * -100, row_above))

    return final_percentages


if __name__ == '__main__':
    a = [[0, 0, 0, 0, 0, 0, 0],
         [1, 0, 0, 0, 0, 0, 0],
         [0, 0, 1, 1, 1, 0, 0],
         [0, 0, 0, 0, 0, 0, 0],
         [1, 1, 1, 0, 0, 1, 0],
         [0, 0, 0, 0, 0, 0, 1],
         [0, 0, 0, 0, 0, 0, 0]]
    print(waterfall_streams(a, 3))
```

Last Modified 2021-06-23
