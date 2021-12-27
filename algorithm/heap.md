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


def iterator_list(array):
    for element in array:
        print(element, end=' ')
    print()


if __name__ == '__main__':
    source = [48, 12, 24, 7, 8, -5, 24, 391, 24, 56, 2, 6, 8, 41]
    heap = MinHeap(source)
    iterator_list(heap.heap)
    heap.insert(36)
    iterator_list(heap.heap)
    print("peek", heap.peek())
    print("remove", heap.remove())
    iterator_list(heap.heap)
    print("peek", heap.peek())
    print("remove", heap.remove())
    iterator_list(heap.heap)
    heap.insert(67)
    iterator_list(heap.heap)
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

Last Modified 2021-12-27
