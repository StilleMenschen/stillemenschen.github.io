# 图

## 深度优先遍历

```python
class Node:
    def __init__(self, name):
        self.children = []
        self.name = name

    def add_child(self, name):
        self.children.append(Node(name))
        return self

    # O(v + e) time | O(v) space
    # v is graphs vertex
    # e is graphs edge
    def depth_first_search(self, array):
        array.append(self.name)
        for child in self.children:
            child.depth_first_search(array)
        return array


if __name__ == '__main__':
    root = Node('A')
    B = Node('B')
    B.children = [Node('E'), Node('F').add_child('I').add_child('J')]
    D = Node('D')
    D.children = [Node('G').add_child('K'), Node('H')]
    root.children = [B, Node('C'), D]
    print(root.depth_first_search([]))
```

Last Modified 2021-11-15
