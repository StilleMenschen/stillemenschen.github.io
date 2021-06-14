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

在两个序列中分别找出序列1和序列2中相差最小的两个数

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