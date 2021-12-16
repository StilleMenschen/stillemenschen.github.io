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

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

class Node
{
public:
    string name;
    vector<Node *> children;
    Node(string name)
    {
        this->name = name;
    }

    // O(v + e) time | O(v) space
    vector<string> depthFirstSearch(vector<string> &array)
    {
        array.push_back(this->name);
        const int childrenSize = this->children.size();
        for (int i = 0; i < childrenSize; i++)
        {
            children[i]->depthFirstSearch(array);
        }
        return array;
    }

    Node *addChild(string name)
    {
        Node *child = new Node(name);
        children.push_back(child);
        return this;
    }
};

int main()
{
    Node *root = new Node("A");
    Node *B = new Node("B");
    Node *F = new Node("F");
    F->addChild("I")->addChild("J");
    B->children = {new Node("E"), F};
    Node *D = new Node("D");
    Node *G = new Node("G");
    G->addChild("K");
    D->children = {G, new Node("H")};
    root->children = {B, new Node("C"), D};
    vector<string> result;
    result = root->depthFirstSearch(result);
    for (string element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 是否单向循环

接收一个有正负整数数组，数组中的数值表示跳跃的步数，正数表示向前跳跃，负数表示向后跳跃，如果步数超过数组边界则折返再从开头或末尾开始；
如果从数组的某个位置开始，经过与数组元素数量相同的步数走回到开始位置，则认为是单向循环的

```python
def get_next_idx(current_idx, array, length):
    jump = array[current_idx]
    next_idx = (current_idx + jump) % length
    return next_idx if next_idx >= 0 else next_idx + length


# O(n) time | O(1) space
def has_single_cycle(array):
    length = len(array)
    if not length:
        return False
    num_elements_visited = 0
    current_idx = 0
    while num_elements_visited < length:
        if num_elements_visited > 0 and current_idx == 0:
            return False
        num_elements_visited += 1
        current_idx = get_next_idx(current_idx, array, length)
    return current_idx == 0


if __name__ == '__main__':
    source = [10, 11, -6, -23, -2, 3, 88, 908, -26]
    print(has_single_cycle(source))
```

```cpp
#include <iostream>
#include <vector>

using namespace std;

int getNextIdx(int currentIdx, vector<int> array, int length)
{
    int jump = array[currentIdx];
    int nextIdx = (currentIdx + jump) % length;
    return nextIdx >= 0 ? nextIdx : nextIdx + length;
}

// O(n) time | O(1) space - where n is the length of the input array
bool hasSingleCycle(vector<int> array)
{
    int numElementsVisited = 0;
    int currentIdx = 0;
    const int length = array.size();
    while (numElementsVisited < length)
    {
        if (numElementsVisited > 0 && currentIdx == 0) return false;
        numElementsVisited++;
        currentIdx = getNextIdx(currentIdx, array, length);
    }
    return currentIdx == 0;
}

int main()
{
    cout << hasSingleCycle({10, 11, -6, -23, -2, 3, 88, 908, -26});
    return 0;
}
```

## 广度优先遍历

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
    def breadth_first_search(self, array):
        queue = [self]
        while len(queue):
            current = queue.pop(0)
            array.append(current.name)
            for child in current.children:
                queue.append(child)
        return array


if __name__ == '__main__':
    root = Node('A')
    B = Node('B')
    B.children = [Node('E'), Node('F').add_child('I').add_child('J')]
    D = Node('D')
    D.children = [Node('G').add_child('K'), Node('H')]
    root.children = [B, Node('C'), D]
    print(root.breadth_first_search([]))
```

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

class Node
{
public:
    string name;
    vector<Node *> children;
    Node(string name)
    {
        this->name = name;
    }

    // O(v + e) time | O(v) space
    vector<string> breadthFirstSearch(vector<string> &array)
    {
        deque<Node *> queue{this};
        while (!queue.empty())
        {
            Node current = *queue.front();
            queue.pop_front();
            array.push_back(current.name);
            const int childrenSize = current.children.size();
            for (int i = 0; i < childrenSize; i++)
            {
                queue.push_back(current.children[i]);
            }
        }
        return array;
    }

    Node *addChild(string name)
    {
        Node *child = new Node(name);
        children.push_back(child);
        return this;
    }
};

int main()
{
    Node *root = new Node("A");
    Node *B = new Node("B");
    Node *F = new Node("F");
    F->addChild("I")->addChild("J");
    B->children = {new Node("E"), F};
    Node *D = new Node("D");
    Node *G = new Node("G");
    G->addChild("K");
    D->children = {G, new Node("H")};
    root->children = {B, new Node("C"), D};
    vector<string> result;
    result = root->breadthFirstSearch(result);
    for (string element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 河流大小

```python
def get_unvisited_neighbors(i, j, matrix, visited):
    unvisited_neighbors = []
    if i > 0 and not visited[i - 1][j]:
        unvisited_neighbors.append((i - 1, j,))
    if i < len(matrix) - 1 and not visited[i + 1][j]:
        unvisited_neighbors.append((i + 1, j,))
    if j > 0 and not visited[i][j - 1]:
        unvisited_neighbors.append((i, j - 1,))
    if j < len(matrix[i]) - 1 and not visited[i][j + 1]:
        unvisited_neighbors.append((i, j + 1,))
    return unvisited_neighbors


def traverse_node(i, j, matrix, visited, sizes):
    number_of_river_size = 0
    nodes_to_explore = [(i, j,)]
    while len(nodes_to_explore):
        i, j = nodes_to_explore.pop()
        if visited[i][j]:
            continue
        visited[i][j] = True
        if matrix[i][j] == 0:
            continue
        number_of_river_size += 1
        unvisited_neighbors = get_unvisited_neighbors(i, j, matrix, visited)
        for neighbor in unvisited_neighbors:
            nodes_to_explore.append(neighbor)
    if number_of_river_size > 0:
        sizes.append(number_of_river_size)


# O(w * h) time | O(w * h) space
def river_sizes(matrix):
    sizes = []
    visited = [[False for _ in row] for row in matrix]
    for i in range(len(matrix)):
        for j in range(len(matrix[i])):
            if visited[i][j]:
                continue
            traverse_node(i, j, matrix, visited, sizes)
    return sizes


if __name__ == '__main__':
    source = [
        [1, 0, 0, 1, 0],
        [1, 0, 1, 0, 0],
        [0, 0, 1, 0, 1],
        [1, 0, 1, 0, 1],
        [1, 0, 1, 1, 0],
    ]
    print(river_sizes(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> getUnvisitedNeighbors(int i, int j,
                                          vector<vector<int>> &matrix, vector<vector<int>> &visited)
{
    vector<vector<int>> unvisitedNeighbors{};
    if (i > 0 && !visited[i - 1][j])
    {
        unvisitedNeighbors.push_back({i - 1, j});
    }
    const int matrixSize = matrix.size();
    if (i < matrixSize - 1 && !visited[i + 1][j])
    {
        unvisitedNeighbors.push_back({i + 1, j});
    }
    if (j > 0 && !visited[i][j - 1])
    {
        unvisitedNeighbors.push_back({i, j - 1});
    }
    const int subMatrixSize = matrix[i].size();
    if (j < subMatrixSize - 1 && !visited[i][j + 1])
    {
        unvisitedNeighbors.push_back({i, j + 1});
    }
    return unvisitedNeighbors;
}

void traverseNode(int i, int j, vector<vector<int>> &matrix,
                  vector<vector<int>> &visited, vector<int> &sizes)
{
    int currentRiverSize = 0;
    vector<vector<int>> nodesToExplore{{i, j}};
    while (nodesToExplore.size() != 0)
    {
        vector<int> currentNode = nodesToExplore.back();
        nodesToExplore.pop_back();
        i = currentNode[0];
        j = currentNode[1];
        if (visited[i][j])
        {
            continue;
        }
        visited[i][j] = true;
        if (matrix[i][j] == 0)
        {
            continue;
        }
        currentRiverSize++;
        vector<vector<int>> unvisitedNeighbors = getUnvisitedNeighbors(i, j, matrix, visited);
        for (vector<int> neighbor : unvisitedNeighbors)
        {
            nodesToExplore.push_back(neighbor);
        }
    }
    if (currentRiverSize > 0)
    {
        sizes.push_back(currentRiverSize);
    }
}

// O(w * h) time | O(w * h) space
vector<int> riverSizes(vector<vector<int>> matrix)
{
    vector<int> sizes = {};
    vector<vector<int>> visited(matrix.size(),
                                vector<int>(matrix[0].size(), false));
    const int matrixSize = matrix.size();
    int subMatrixSize = 0;
    for (int i = 0; i < matrixSize; i++)
    {
        subMatrixSize = matrix[i].size();
        for (int j = 0; j < subMatrixSize; j++)
        {
            if (visited[i][j])
            {
                continue;
            }
            traverseNode(i, j, matrix, visited, sizes);
        }
    }
    return sizes;
}

int main()
{
    vector<vector<int>> source = {
        {1, 0, 0, 1, 0},
        {1, 0, 1, 0, 0},
        {0, 0, 1, 0, 1},
        {1, 0, 1, 0, 1},
        {1, 0, 1, 1, 0}};
    vector<int> result = riverSizes(source);
    for (int element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

## 共同祖先

有如下祖先树，找出节点 E 和 节点 I 的共同祖先 B

```
           A
        /     \
       B       C
     /   \   /   \
    D     E F     G
  /  \
 G    I
```

```python
class AncestralTree:
    def __init__(self, name):
        self.name = name
        self.ancestor = None


# O(d) time | O(1) space - where d is the depth (height) of the ancestral tree
def get_youngest_common_ancestor(top_ancestor, descendant_cne, descendant_two):
    depth_one = get_descendant_depth(descendant_cne, top_ancestor)
    depth_two = get_descendant_depth(descendant_two, top_ancestor)
    if depth_one > depth_two:
        return backtrack_ancestral_tree(descendant_cne, descendant_two, depth_one - depth_two)
    else:
        return backtrack_ancestral_tree(descendant_two, descendant_cne, depth_two - depth_one)


def get_descendant_depth(descendant, top_ancestor):
    depth = 0
    while descendant != top_ancestor:
        depth += 1
        descendant = descendant.ancestor
    return depth


def backtrack_ancestral_tree(lower_descendant, higher_descendant, diff):
    while diff > 0:
        lower_descendant = lower_descendant.ancestor
        diff -= 1
    while lower_descendant != higher_descendant:
        lower_descendant = lower_descendant.ancestor
        higher_descendant = higher_descendant.ancestor
    return lower_descendant


if __name__ == '__main__':
    A = AncestralTree('A')
    B = AncestralTree('B')
    B.ancestor = A
    C = AncestralTree('C')
    C.ancestor = A
    D = AncestralTree('D')
    D.ancestor = B
    E = AncestralTree('E')
    E.ancestor = B
    F = AncestralTree('F')
    F.ancestor = C
    G = AncestralTree('G')
    G.ancestor = C
    H = AncestralTree('H')
    H.ancestor = D
    I = AncestralTree('I')
    I.ancestor = D
    common_ancestor = get_youngest_common_ancestor(A, E, I)
    print(common_ancestor.name)
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

class AncestralTree
{
public:
    char name;
    AncestralTree *ancestor;

    AncestralTree(char name)
    {
        this->name = name;
        this->ancestor = nullptr;
    }
};

int getDescendantDepth(AncestralTree *descendant, AncestralTree *topAncestor)
{
    int depth = 0;
    while (descendant != topAncestor)
    {
        depth++;
        descendant = descendant->ancestor;
    }
    return depth;
}

AncestralTree *backtrackAncestralTree(AncestralTree *lowerDescendant,
                                      AncestralTree *higherDescendant,
                                      int diff)
{
    while (diff > 0)
    {
        lowerDescendant = lowerDescendant->ancestor;
        diff--;
    }
    while (lowerDescendant != higherDescendant)
    {
        lowerDescendant = lowerDescendant->ancestor;
        higherDescendant = higherDescendant->ancestor;
    }
    return lowerDescendant;
}

// O(d) time | O(1) space - where d is the depth (height) of the ancestral tree
AncestralTree *getYoungestCommonAncestor(AncestralTree *topAncestor,
                                         AncestralTree *descendantOne,
                                         AncestralTree *descendantTwo)
{
    int depthOne = getDescendantDepth(descendantOne, topAncestor);
    int depthTwo = getDescendantDepth(descendantTwo, topAncestor);
    if (depthOne > depthTwo)
    {
        return backtrackAncestralTree(descendantOne, descendantTwo,
                                      depthOne - depthTwo);
    }
    else
    {
        return backtrackAncestralTree(descendantTwo, descendantOne,
                                      depthTwo - depthOne);
    }
}

int main()
{
    AncestralTree *A = new AncestralTree('A');
    AncestralTree *B = new AncestralTree('B');
    B->ancestor = A;
    AncestralTree *C = new AncestralTree('C');
    C->ancestor = A;
    AncestralTree *D = new AncestralTree('D');
    D->ancestor = B;
    AncestralTree *E = new AncestralTree('E');
    E->ancestor = B;
    AncestralTree *F = new AncestralTree('F');
    F->ancestor = C;
    AncestralTree *G = new AncestralTree('G');
    G->ancestor = C;
    AncestralTree *H = new AncestralTree('H');
    H->ancestor = D;
    AncestralTree *I = new AncestralTree('I');
    I->ancestor = D;
    AncestralTree *common_ancestor = getYoungestCommonAncestor(A, E, I);
    cout << common_ancestor->name;
    return 0;
}
```

Last Modified 2021-12-16
