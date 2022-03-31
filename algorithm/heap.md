# 堆

## 构建最小堆

```python
class MinHeap:
    def __init__(self, array):
        self.heap = self.build_heap(array)

    # O(n) time | O(1) space
    def build_heap(self, array):
        first_parent_idx = (len(array) - 2) // 2
        for current_idx in reversed(range(first_parent_idx + 1)):
            self.sift_down(current_idx, len(array) - 1, array)
        return array

    # O(log(n)) time | O(1) space
    def sift_down(self, current_idx, end_idx, heap):
        child_one_idx = current_idx * 2 + 1
        while child_one_idx <= end_idx:
            child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
            idx_to_swap = child_one_idx
            # In Max Heap, change comparison operator '<' to '>'
            if child_two_idx != -1 and heap[child_two_idx] < heap[child_one_idx]:
                idx_to_swap = child_two_idx
            # In Max Heap, change comparison operator '<' to '>'
            if heap[idx_to_swap] < heap[current_idx]:
                self.swap(current_idx, idx_to_swap, heap)
                current_idx = idx_to_swap
                child_one_idx = current_idx * 2 + 1
            else:
                return

    # O(log(n)) time | O(1) space
    def sift_up(self, current_idx, heap):
        parent_idx = (current_idx - 1) // 2
        # In Max Heap, change comparison operator '<' to '>'
        while current_idx > 0 and heap[current_idx] < heap[parent_idx]:
            self.swap(current_idx, parent_idx, heap)
            current_idx = parent_idx
            parent_idx = (current_idx - 1) // 2

    # O(1) time | O(1) space
    def peek(self):
        return self.heap[0]

    # O(log(n)) time | O(1) space
    def remove(self):
        self.swap(0, len(self.heap) - 1, self.heap)
        value_to_remove = self.heap.pop()
        self.sift_down(0, len(self.heap) - 1, self.heap)
        return value_to_remove

    # O(log(n)) time | O(1) space
    def insert(self, value):
        self.heap.append(value)
        self.sift_up(len(self.heap) - 1, self.heap)

    @staticmethod
    def swap(i, j, heap):
        heap[i], heap[j] = heap[j], heap[i]


if __name__ == '__main__':
    source = [48, 12, 7, 8, -5, 24, 391, 24, 56, 2, 8, 41]
    min_heap = MinHeap(source)
    print(min_heap.heap)
    min_heap.insert(36)
    print(min_heap.heap)
    print("peek", min_heap.peek())
    print("remove", min_heap.remove())
    print(min_heap.heap)
    print("peek", min_heap.peek())
    print("remove", min_heap.remove())
    print(min_heap.heap)
    min_heap.insert(67)
    print(min_heap.heap)
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class MinHeap
{
public:
    vector<int> heap;

    MinHeap(vector<int> vector) { heap = buildHeap(vector); }

    // O(n) time | O(1) space
    vector<int> buildHeap(vector<int> &vector)
    {
        int firstParentIdx = (vector.size() - 2) / 2;
        for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
        {
            siftDown(currentIdx, vector.size() - 1, vector);
        }
        return vector;
    }

    // O(log(n)) time | O(1) space
    void siftDown(int currentIdx, int endIdx, vector<int> &heap)
    {
        int childOneIdx = currentIdx * 2 + 1;
        while (childOneIdx <= endIdx)
        {
            int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
            int idxToSwap = childOneIdx;
            // In Max Heap, change comparison operator '<' to '>'
            if (childTwoIdx != -1 && heap[childTwoIdx] < heap[childOneIdx])
            {
                idxToSwap = childTwoIdx;
            }
            // In Max Heap, change comparison operator '<' to '>'
            if (heap[idxToSwap] < heap[currentIdx])
            {
                swap(heap[currentIdx], heap[idxToSwap]);
                currentIdx = idxToSwap;
                childOneIdx = currentIdx * 2 + 1;
            }
            else
            {
                break;
            }
        }
    }

    // O(log(n)) time | O(1) space
    void siftUp(int currentIdx)
    {
        int parentIdx = (currentIdx - 1) / 2;
        // In Max Heap, change comparison operator '<' to '>'
        while (currentIdx > 0 && heap[currentIdx] < heap[parentIdx])
        {
            swap(heap[currentIdx], heap[parentIdx]);
            currentIdx = parentIdx;
            parentIdx = (currentIdx - 1) / 2;
        }
    }

    int peek() { return heap[0]; }

    int remove()
    {
        swap(heap[0], heap[heap.size() - 1]);
        int valueToRemove = heap.back();
        heap.pop_back();
        siftDown(0, heap.size() - 1, heap);
        return valueToRemove;
    }

    void insert(int value)
    {
        heap.push_back(value);
        siftUp(heap.size() - 1);
    }
};

void iteratorList(vector<int> array)
{
    for (const int value : array)
    {
        cout << value << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> source = {48, 12, 24, 7, 8, -5, 24, 391, 24, 56, 2, 6, 8, 41};
    MinHeap heap(source);
    iteratorList(heap.heap);
    heap.insert(36);
    iteratorList(heap.heap);
    cout << "peek " << heap.peek() << endl;
    cout << "remove " << heap.remove() << endl;
    iteratorList(heap.heap);
    cout << "peek " << heap.peek() << endl;
    cout << "remove " << heap.remove() << endl;
    iteratorList(heap.heap);
    heap.insert(67);
    iteratorList(heap.heap);
    return 0;
}
```

## 连续中位数

创建一个数据结构，记录一组数据（自然数），要求每次新添加一个整数进去之后，都可以自动重新计算这组数据集的中位数

```python
class Heap:
    def __init__(self, comparison_function, array):
        self.comparison_function = comparison_function
        self.heap = self.build_heap(array)

    @property
    def length(self):
        return len(self.heap)

    def build_heap(self, array):
        first_parent_idx = (len(array) - 2) // 2
        for current_idx in reversed(range(first_parent_idx + 1)):
            self.sift_down(current_idx, len(array) - 1, array)
        return array

    def sift_down(self, current_idx, end_idx, heap):
        child_one_idx = current_idx * 2 + 1
        while child_one_idx <= end_idx:
            child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
            if child_two_idx != -1:
                if self.comparison_function(heap[child_two_idx], heap[child_one_idx]):
                    idx_to_swap = child_two_idx
                else:
                    idx_to_swap = child_one_idx
            else:
                idx_to_swap = child_one_idx
            if self.comparison_function(heap[idx_to_swap], heap[current_idx]):
                self.swap(current_idx, idx_to_swap, heap)
                current_idx = idx_to_swap
                child_one_idx = current_idx * 2 + 1
            else:
                return

    def sift_up(self, current_idx, heap):
        parent_idx = (current_idx - 1) // 2
        while current_idx > 0:
            if self.comparison_function(heap[current_idx], heap[parent_idx]):
                self.swap(current_idx, parent_idx, heap)
                current_idx = parent_idx
                parent_idx = (current_idx - 1) // 2
            else:
                return

    def peek(self):
        return self.heap[0]

    def remove(self):
        self.swap(0, self.length - 1, self.heap)
        value_to_remove = self.heap.pop()
        self.sift_down(0, self.length - 1, self.heap)
        return value_to_remove

    def insert(self, value):
        self.heap.append(value)
        self.sift_up(self.length - 1, self.heap)

    @staticmethod
    def swap(i, j, array):
        array[i], array[j] = array[j], array[i]


MAX_HEAP_FUNCTION = lambda a, b: a > b

MIN_HEAP_FUNCTION = lambda a, b: a < b


class ContinuousMedianHandler:
    def __init__(self):
        # 通过两个堆来存储数据
        # 因为计算中位数之前需要先排序一组数据,
        # 而利用堆的特点可以在每次添加新的数据到数据集中时,
        # 一直保持跟踪排序后中间位置的两个值
        # 即前一半较小的数据放在最大堆中, 每次计算相当于取小数据集中最大的数来计算中位数
        # 即后一半较大的数据放在最小堆中, 每次计算相当于取大数据集中最小的数来计算中位数
        # 最大堆记录较小的数, 在最大堆中, 满足父元素的值总是比子元素的值要大
        self.lowers = Heap(MAX_HEAP_FUNCTION, [])
        # 最小堆记录较大的数, 在最小堆中, 满足父元素的值总是比子元素的值要小
        self.greaters = Heap(MIN_HEAP_FUNCTION, [])
        # 记录中位数
        self.median = None

    # O(log(n)) time | O(n) space
    def insert(self, number):
        # 存储较小数据的最大堆长度为0或者添加的数小于最大堆的最大值
        if not self.lowers.length or number < self.lowers.peek():
            self.lowers.insert(number)
        # 添加的数大于最大堆的最大值
        else:
            self.greaters.insert(number)
        # 重新平衡两个堆的数据, 保持两个堆的数据量只会相差1
        self.rebalance_heaps()
        # 重新计算中位数
        self.update_median()

    def rebalance_heaps(self):
        # 判断最大堆中的数据量与最小堆的数据量相差是否等于2
        if self.lowers.length - self.greaters.length == 2:
            # 最大堆的数据量比最小堆多, 将数据量多的堆内顶端的值移动到另一个堆中
            self.greaters.insert(self.lowers.remove())
        elif self.greaters.length - self.lowers.length == 2:
            # 最小堆的数据量比最大堆多, 将数据量多的堆内顶端的值移动到另一个堆中
            self.lowers.insert(self.greaters.remove())

    def update_median(self):
        # 如果两个堆的数据量一致, 说明数据集中的数值个数是偶数, 则需要取中间的两个数相除得出中位数
        if self.lowers.length == self.greaters.length:
            self.median = (self.lowers.peek() + self.greaters.peek()) / 2
        # 如果最大堆和最小堆的数据量相差1, 则取数据量较多的堆顶端的数作为中位数
        elif self.lowers.length > self.greaters.length:
            self.median = self.lowers.peek()
        else:
            self.median = self.greaters.peek()

    def get_median(self):
        return self.median


if __name__ == '__main__':
    root = ContinuousMedianHandler()
    root.insert(5)
    root.insert(10)
    print(root.get_median())
    root.insert(100)
    print(root.get_median())
    root.insert(1000)
    root.insert(35)
    print(root.get_median())
```

```cpp
#include <iostream>
#include <vector>
#include <functional>
using namespace std;

bool MAX_HEAP_FUNCTION(int a, int b);
bool MIN_HEAP_FUNCTION(int a, int b);

class Heap
{
public:
    vector<int> heap;
    function<bool(int, int)> comparisonFunction;
    int length;

    Heap(function<bool(int, int)> func, vector<int> vector)
    {
        comparisonFunction = func;
        heap = buildHeap(&vector);
        length = heap.size();
    }

    vector<int> buildHeap(vector<int> *vector)
    {
        int firstParentIdx = (vector->size() - 2) / 2;
        for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
        {
            siftDown(currentIdx, vector->size() - 1, vector);
        }
        return *vector;
    }

    void siftDown(int currentIdx, int endIdx, vector<int> *heap)
    {
        int childOneIdx = currentIdx * 2 + 1;
        while (childOneIdx <= endIdx)
        {
            int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
            int idxToSwap;
            if (childTwoIdx != -1)
            {
                if (comparisonFunction(heap->at(childTwoIdx), heap->at(childOneIdx)))
                {
                    idxToSwap = childTwoIdx;
                }
                else
                {
                    idxToSwap = childOneIdx;
                }
            }
            else
            {
                idxToSwap = childOneIdx;
            }
            if (comparisonFunction(heap->at(idxToSwap), heap->at(currentIdx)))
            {
                swap(currentIdx, idxToSwap, heap);
                currentIdx = idxToSwap;
                childOneIdx = currentIdx * 2 + 1;
            }
            else
            {
                return;
            }
        }
    }

    void siftUp(int currentIdx, vector<int> *heap)
    {
        int parentIdx = (currentIdx - 1) / 2;
        while (currentIdx > 0)
        {
            if (comparisonFunction(heap->at(currentIdx), heap->at(parentIdx)))
            {
                swap(currentIdx, parentIdx, heap);
                currentIdx = parentIdx;
                parentIdx = (currentIdx - 1) / 2;
            }
            else
            {
                return;
            }
        }
    }

    int peek() { return heap[0]; }

    int remove()
    {
        swap(0, heap.size() - 1, &heap);
        int valueToRemove = heap.back();
        heap.pop_back();
        length--;
        siftDown(0, heap.size() - 1, &heap);
        return valueToRemove;
    }

    void insert(int value)
    {
        heap.push_back(value);
        length++;
        siftUp(heap.size() - 1, &heap);
    }

    void swap(int i, int j, vector<int> *heap)
    {
        int temp = heap->at(j);
        heap->at(j) = heap->at(i);
        heap->at(i) = temp;
    }
};

class ContinuousMedianHandler
{
public:
    Heap lowers;
    Heap greaters;
    double median;

    ContinuousMedianHandler()
        : lowers(MAX_HEAP_FUNCTION, {}), greaters(MIN_HEAP_FUNCTION, {})
    {
        median = 0;
    }

    // O(log(n)) time | O(n) space
    void insert(int number)
    {
        if (!lowers.length || number < lowers.peek())
        {
            lowers.insert(number);
        }
        else
        {
            greaters.insert(number);
        }
        rebalanceHeaps();
        updateMedian();
    }

    void rebalanceHeaps()
    {
        if (lowers.length - greaters.length == 2)
        {
            greaters.insert(lowers.remove());
        }
        else if (greaters.length - lowers.length == 2)
        {
            lowers.insert(greaters.remove());
        }
    }

    void updateMedian()
    {
        if (lowers.length == greaters.length)
        {
            median = ((double)lowers.peek() + (double)greaters.peek()) / 2;
        }
        else if (lowers.length > greaters.length)
        {
            median = lowers.peek();
        }
        else
        {
            median = greaters.peek();
        }
    }

    double getMedian() { return median; }
};

bool MAX_HEAP_FUNCTION(int a, int b) { return a > b; }

bool MIN_HEAP_FUNCTION(int a, int b) { return a < b; }

int main()
{
    ContinuousMedianHandler handler;
    handler.insert(5);
    handler.insert(10);
    cout << handler.getMedian() << endl;
    handler.insert(100);
    cout << handler.getMedian() << endl;
    handler.insert(1000);
    handler.insert(35);
    cout << handler.getMedian() << endl;
    return 0;
}
```

## 按 K 次排序

给定一个数组和一个整数 K ，K 表示数组内的元素最多只需要向左或向右移动 K 次就可以处在正确的排序位置，按此限制从小到大排序输入数组

```python
# O(nlog(k)) time | O(k) space - where n is the number
# of elements in the array and k is how far away elements
# are from their sorted position
def sort_k_sorted_array(array, k):
    # 先将前面第 K 个元素构建成最小堆
    min_heap_with_k_elements = MinHeap(array[: min(k + 1, len(array))])
    # 记录最小元素放置的位置, 从第一个位置开始
    next_index_to_insert_element = 0
    # 从 K + 1 位置开始
    for idx in range(k + 1, len(array)):
        # 取出最小堆顶端元素, 最小堆中顶端元素为最小
        min_element = min_heap_with_k_elements.remove()
        # 将最小的元素放到前面
        array[next_index_to_insert_element] = min_element
        # 移动下一个到位置
        next_index_to_insert_element += 1
        # 将当前元素放入最小堆中
        current_element = array[idx]
        min_heap_with_k_elements.insert(current_element)
    # 将最小堆中剩余的元素全部放入数组中
    while not min_heap_with_k_elements.is_empty():
        min_element = min_heap_with_k_elements.remove()
        array[next_index_to_insert_element] = min_element
        next_index_to_insert_element += 1

    return array


class MinHeap:
    def __init__(self, array):
        self.heap = self.build_heap(array)

    def is_empty(self):
        return len(self.heap) == 0

    def build_heap(self, array):
        first_parent_idx = (len(array) - 2) // 2
        for current_idx in reversed(range(first_parent_idx + 1)):
            self.sift_down(current_idx, len(array) - 1, array)
        return array

    def sift_down(self, current_idx, end_idx, heap):
        child_one_idx = current_idx * 2 + 1
        while child_one_idx <= end_idx:
            child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
            if child_two_idx != -1 and heap[child_two_idx] < heap[child_one_idx]:
                idx_to_swap = child_two_idx
            else:
                idx_to_swap = child_one_idx
            if heap[idx_to_swap] < heap[current_idx]:
                self.swap(current_idx, idx_to_swap, heap)
                current_idx = idx_to_swap
                child_one_idx = current_idx * 2 + 1
            else:
                return

    def sift_up(self, current_idx, heap):
        parent_idx = (current_idx - 1) // 2
        while current_idx > 0 and heap[current_idx] < heap[parent_idx]:
            self.swap(current_idx, parent_idx, heap)
            current_idx = parent_idx
            parent_idx = (current_idx - 1) // 2

    def peek(self):
        return self.heap[0]

    def remove(self):
        self.swap(0, len(self.heap) - 1, self.heap)
        value_to_remove = self.heap.pop()
        self.sift_down(0, len(self.heap) - 1, self.heap)
        return value_to_remove

    def insert(self, value):
        self.heap.append(value)
        self.sift_up(len(self.heap) - 1, self.heap)

    @staticmethod
    def swap(i, j, heap):
        heap[i], heap[j] = heap[j], heap[i]


if __name__ == '__main__':
    print(sort_k_sorted_array([3, 2, 1, 5, 4, 7, 6, 5], 3))
```

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class MinHeap
{
public:
    vector<int> heap;

    MinHeap(vector<int> array) { heap = buildHeap(array); }

    bool isEmpty() { return heap.size() == 0; }

    vector<int> buildHeap(vector<int> array)
    {
        int firstParentIdx = (array.size() - 2) / 2;
        for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
        {
            siftDown(currentIdx, array.size() - 1, array);
        }
        return array;
    }

    void siftDown(int currentIdx, int endIdx, vector<int> &heap)
    {
        int childOneIdx = currentIdx * 2 + 1;
        while (childOneIdx <= endIdx)
        {
            int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
            int idxToSwap;
            if (childTwoIdx != -1 && heap[childTwoIdx] < heap[childOneIdx])
            {
                idxToSwap = childTwoIdx;
            }
            else
            {
                idxToSwap = childOneIdx;
            }
            if (heap[idxToSwap] < heap[currentIdx])
            {
                swap(currentIdx, idxToSwap, heap);
                currentIdx = idxToSwap;
                childOneIdx = currentIdx * 2 + 1;
            }
            else
            {
                return;
            }
        }
    }

    void siftUp(int currentIdx, vector<int> &heap)
    {
        int parentIdx = (currentIdx - 1) / 2;
        while (currentIdx > 0 && heap[currentIdx] < heap[parentIdx])
        {
            swap(currentIdx, parentIdx, heap);
            currentIdx = parentIdx;
            parentIdx = (currentIdx - 1) / 2;
        }
    }

    int peek() { return heap[0]; }

    int remove()
    {
        swap(0, heap.size() - 1, heap);
        int valueToRemove = heap.back();
        heap.pop_back();
        siftDown(0, heap.size() - 1, heap);
        return valueToRemove;
    }

    void insert(int value)
    {
        heap.push_back(value);
        siftUp(heap.size() - 1, heap);
    }

    void swap(int i, int j, vector<int> &heap)
    {
        int temp = heap[j];
        heap[j] = heap[i];
        heap[i] = temp;
    }
};

// O(nlog(k)) time | O(k) space - where n is the number
// of elements in the array and k is how far away elements
// are from their sorted position
vector<int> sortKSortedArray(vector<int> array, int k)
{
    vector<int> minHeapInputArray(array.begin(),
                                  array.begin() + min(k + 1, (int)array.size()));
    auto minHeapWithKElements = new MinHeap(minHeapInputArray);

    int nextIndexToInsertElement = 0;
    const int arraySize = array.size();
    for (int idx = k + 1; idx < arraySize; idx++)
    {
        auto minElement = minHeapWithKElements->remove();
        array[nextIndexToInsertElement] = minElement;
        nextIndexToInsertElement++;

        auto currentElement = array[idx];
        minHeapWithKElements->insert(currentElement);
    }

    while (!minHeapWithKElements->isEmpty())
    {
        auto minElement = minHeapWithKElements->remove();
        array[nextIndexToInsertElement] = minElement;
        nextIndexToInsertElement++;
    }

    return array;
}

int main()
{
    vector<int> result = sortKSortedArray({3, 2, 1, 5, 4, 7, 6, 5}, 3);
    for (const int element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

Last Modified 2022-03-31
