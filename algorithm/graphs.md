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
def get_unvisited_neighbors(row, col, matrix, visited):
    unvisited_neighbors = []
    if row > 0 and not visited[row - 1][col]:
        unvisited_neighbors.append((row - 1, col,))
    if row < len(matrix) - 1 and not visited[row + 1][col]:
        unvisited_neighbors.append((row + 1, col,))
    if col > 0 and not visited[row][col - 1]:
        unvisited_neighbors.append((row, col - 1,))
    if col < len(matrix[row]) - 1 and not visited[row][col + 1]:
        unvisited_neighbors.append((row, col + 1,))
    return unvisited_neighbors


def traverse_node(row, col, matrix, visited, sizes):
    number_of_river_size = 0
    nodes_to_explore = [(row, col,)]
    while len(nodes_to_explore):
        row, col = nodes_to_explore.pop()
        if visited[row][col]:
            continue
        visited[row][col] = True
        if matrix[row][col] == 0:
            continue
        number_of_river_size += 1
        unvisited_neighbors = get_unvisited_neighbors(row, col, matrix, visited)
        for neighbor in unvisited_neighbors:
            nodes_to_explore.append(neighbor)
    if number_of_river_size > 0:
        sizes.append(number_of_river_size)


# O(w * h) time | O(w * h) space
def river_sizes(matrix):
    sizes = []
    visited = [[False for _ in row] for row in matrix]
    for row in range(len(matrix)):
        for col in range(len(matrix[row])):
            if visited[row][col]:
                continue
            traverse_node(row, col, matrix, visited, sizes)
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

vector<vector<int>> getUnvisitedNeighbors(int row, int col,
                                          vector<vector<int>> &matrix, vector<vector<int>> &visited)
{
    vector<vector<int>> unvisitedNeighbors{};
    if (row > 0 && !visited[row - 1][col])
    {
        unvisitedNeighbors.push_back({row - 1, col});
    }
    const int matrixSize = matrix.size();
    if (row < matrixSize - 1 && !visited[row + 1][col])
    {
        unvisitedNeighbors.push_back({row + 1, col});
    }
    if (col > 0 && !visited[row][col - 1])
    {
        unvisitedNeighbors.push_back({row, col - 1});
    }
    const int subMatrixSize = matrix[row].size();
    if (col < subMatrixSize - 1 && !visited[row][col + 1])
    {
        unvisitedNeighbors.push_back({row, col + 1});
    }
    return unvisitedNeighbors;
}

void traverseNode(int row, int col, vector<vector<int>> &matrix,
                  vector<vector<int>> &visited, vector<int> &sizes)
{
    int currentRiverSize = 0;
    vector<vector<int>> nodesToExplore{{row, col}};
    while (nodesToExplore.size() != 0)
    {
        vector<int> currentNode = nodesToExplore.back();
        nodesToExplore.pop_back();
        row = currentNode[0];
        col = currentNode[1];
        if (visited[row][col])
        {
            continue;
        }
        visited[row][col] = true;
        if (matrix[row][col] == 0)
        {
            continue;
        }
        currentRiverSize++;
        vector<vector<int>> unvisitedNeighbors = getUnvisitedNeighbors(row, col, matrix, visited);
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
    for (int row = 0; row < matrixSize; row++)
    {
        subMatrixSize = matrix[row].size();
        for (int col = 0; col < subMatrixSize; col++)
        {
            if (visited[row][col])
            {
                continue;
            }
            traverseNode(row, col, matrix, visited, sizes);
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

## 移除孤岛

给出一个整数表示的矩阵，其中 0 表示水域，1 表示土地；找出没有接触边界的孤岛并删除掉

如下一个矩阵

```
[
  [1, 0, 0, 0, 0, 0],
  [0, 1, 0, 1, 1, 1],
  [0, 0, 1, 0, 1, 0],
  [1, 1, 0, 0, 1, 0],
  [1, 0, 1, 1, 0, 0],
  [1, 0, 0, 0, 0, 1]
]
```

需要删除的孤岛为

```
[
  [                ],
  [   1,           ],
  [      1,        ],
  [                ],
  [      1, 1,     ],
  [                ]
]
```

```python
def get_unvisited_neighbors1(row, col, matrix, connected_to_border_islands):
    unvisited_neighbors = []
    if row > 0 and not connected_to_border_islands[row - 1][col]:
        unvisited_neighbors.append((row - 1, col,))
    if row < len(matrix) - 1 and not connected_to_border_islands[row + 1][col]:
        unvisited_neighbors.append((row + 1, col,))
    if col > 0 and not connected_to_border_islands[row][col - 1]:
        unvisited_neighbors.append((row, col - 1,))
    if col < len(matrix[row]) - 1 and not connected_to_border_islands[row][col + 1]:
        unvisited_neighbors.append((row, col + 1,))
    return unvisited_neighbors


def mark_border_islands1(row, col, matrix, connected_to_border_islands):
    nodes_to_traverse = [(row, col,)]
    while len(nodes_to_traverse):
        row, col = nodes_to_traverse.pop()
        if connected_to_border_islands[row][col] or matrix[row][col] == 0:
            continue
        else:
            connected_to_border_islands[row][col] = True
        unvisited_neighbors = get_unvisited_neighbors1(row, col, matrix, connected_to_border_islands)
        for neighbor in unvisited_neighbors:
            nodes_to_traverse.append(neighbor)


def get_unvisited_neighbors2(row, col, matrix):
    unvisited_neighbors = []
    if row > 0 and matrix[row - 1][col] != 2:
        unvisited_neighbors.append((row - 1, col,))
    if row < len(matrix) - 1 and matrix[row + 1][col] != 2:
        unvisited_neighbors.append((row + 1, col,))
    if col > 0 and matrix[row][col - 1] != 2:
        unvisited_neighbors.append((row, col - 1,))
    if col < len(matrix[row]) - 1 and matrix[row][col + 1] != 2:
        unvisited_neighbors.append((row, col + 1,))
    return unvisited_neighbors


def mark_border_islands2(row, col, matrix):
    nodes_to_traverse = [(row, col,)]
    while len(nodes_to_traverse):
        row, col = nodes_to_traverse.pop()
        if matrix[row][col] == 0 or matrix[row][col] == 2:
            continue
        else:
            matrix[row][col] = 2
        unvisited_neighbors = get_unvisited_neighbors2(row, col, matrix)
        for neighbor in unvisited_neighbors:
            nodes_to_traverse.append(neighbor)


# O(w * h) time | O(w * h) space
def remove_islands1(matrix):
    connected_to_border_islands = [[False for _ in row] for row in matrix]
    matrix_size = len(matrix)
    for row in range(matrix_size):
        if row == 0 or row == matrix_size - 1:
            traverse_nodes = list(range(len(matrix[row])))
        else:
            traverse_nodes = [0, len(matrix[row]) - 1]
        for col in traverse_nodes:
            if connected_to_border_islands[row][col]:
                continue
            mark_border_islands1(row, col, matrix, connected_to_border_islands)

    for row in range(1, matrix_size - 1):
        for col in range(1, len(matrix[row]) - 1):
            if connected_to_border_islands[row][col]:
                continue
            matrix[row][col] = 0

    return matrix


# O(w * h) time | O(w * h) space
def remove_islands2(matrix):
    matrix_size = len(matrix)
    for row in range(matrix_size):
        if row == 0 or row == matrix_size - 1:
            traverse_nodes = list(range(len(matrix[row])))
        else:
            traverse_nodes = [0, len(matrix[row]) - 1]
        for col in traverse_nodes:
            if matrix[row][col] == 2:
                continue
            mark_border_islands2(row, col, matrix)

    for row in range(matrix_size):
        for col in range(len(matrix[row])):
            if matrix[row][col] == 0:
                continue
            if matrix[row][col] == 2:
                matrix[row][col] = 1
            else:
                matrix[row][col] = 0

    return matrix


def print_matrix(matrix):
    for i in range(len(matrix)):
        for j in range(len(matrix[i])):
            print(matrix[i][j], end='  ')
        print()


if __name__ == '__main__':
    print_matrix(remove_islands1([
        [1, 0, 0, 0, 0, 0],
        [0, 1, 0, 1, 1, 1],
        [0, 0, 1, 0, 1, 0],
        [1, 1, 0, 0, 1, 0],
        [1, 0, 1, 1, 0, 0],
        [1, 0, 0, 0, 0, 1],
    ]))
    print_matrix(remove_islands2([
        [1, 0, 0, 0, 0, 0],
        [0, 1, 0, 1, 1, 1],
        [0, 0, 1, 0, 1, 0],
        [1, 1, 0, 0, 1, 0],
        [1, 0, 1, 1, 0, 0],
        [1, 0, 0, 0, 0, 1],
    ]))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

void findOnesConnectedToBorder(vector<vector<int>> &matrix, int startRow,
                               int startCol,
                               vector<vector<bool>> &onesConnectedToBorder);

vector<vector<int>> getNeighbors(vector<vector<int>> &matrix, int row, int col);

// O(wh) time | O(wh) space - where w and h
// are the width and height of the input matrix
vector<vector<int>> removeIslands1(vector<vector<int>> matrix)
{
    vector<vector<bool>> onesConnectedToBorder;
    const int matrixSize = matrix.size();
    for (int i = 0; i < matrixSize; i++)
    {
        onesConnectedToBorder.push_back(vector<bool>(matrix[0].size(), false));
    }

    // Find all the 1s that are not islands
    for (int row = 0; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 0; col < matrixRowSize; col++)
        {
            bool rowIsBorder = row == 0 || row == matrixSize - 1;
            bool colIsBorder = col == 0 || col == matrixRowSize - 1;
            bool isBorder = rowIsBorder || colIsBorder;
            if (!isBorder)
            {
                continue;
            }
            if (matrix[row][col] != 1)
            {
                continue;
            }
            findOnesConnectedToBorder(matrix, row, col, onesConnectedToBorder);
        }
    }

    for (int row = 1; row < matrixSize - 1; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 1; col < matrixRowSize - 1; col++)
        {
            if (onesConnectedToBorder[row][col])
            {
                continue;
            }
            matrix[row][col] = 0;
        }
    }

    return matrix;
}

void findOnesConnectedToBorder(vector<vector<int>> &matrix,
                               int startRow, int startCol,
                               vector<vector<bool>> &onesConnectedToBorder)
{

    vector<vector<int>> stack = {{startRow, startCol}};
    while (stack.size() > 0)
    {
        auto currentPosition = stack[stack.size() - 1];
        stack.pop_back();
        int currentRow = currentPosition[0];
        int currentCol = currentPosition[1];
        bool alreadyVisited = onesConnectedToBorder[currentRow][currentCol];
        if (alreadyVisited)
        {
            continue;
        }
        onesConnectedToBorder[currentRow][currentCol] = true;
        auto neighbors = getNeighbors(matrix, currentRow, currentCol);
        for (auto neighbor : neighbors)
        {
            int row = neighbor[0];
            int col = neighbor[1];
            if (matrix[row][col] != 1)
            {
                continue;
            }
            stack.push_back(neighbor);
        }
    }
}

vector<vector<int>> getNeighbors(vector<vector<int>> &matrix,
                                 int row, int col)
{
    vector<vector<int>> neighbors;
    const int matrixSize = matrix.size();
    const int matrixRowSize = matrix[row].size();
    int numRows = matrixSize;
    int numCols = matrixRowSize;
    if (row - 1 >= 0)
    {
        neighbors.push_back(vector<int>{row - 1, col}); // UP
    }
    if (row + 1 < numRows)
    {
        neighbors.push_back(vector<int>{row + 1, col}); // DOWN
    }
    if (col - 1 >= 0)
    {
        neighbors.push_back(vector<int>{row, col - 1}); // LEFT
    }
    if (col + 1 < numCols)
    {
        neighbors.push_back(vector<int>{row, col + 1}); // RIGHT
    }

    return neighbors;
}

void changeOnesConnectedToBorderToTwos2(vector<vector<int>> &matrix,
                                        int startRow, int startCol);

vector<vector<int>> getNeighbors(vector<vector<int>> &matrix, int row, int col);

// O(wh) time | O(wh) space - where w and h
// are the width and height of the input matrix
vector<vector<int>> removeIslands2(vector<vector<int>> matrix)
{
    const int matrixSize = matrix.size();
    for (int row = 0; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 0; col < matrixRowSize; col++)
        {
            bool rowIsBorder = row == 0 || row == matrixSize - 1;
            bool colIsBorder = col == 0 || col == matrixRowSize - 1;
            bool isBorder = rowIsBorder || colIsBorder;
            if (!isBorder)
            {
                continue;
            }
            if (matrix[row][col] != 1)
            {
                continue;
            }
            changeOnesConnectedToBorderToTwos2(matrix, row, col);
        }
    }

    for (int row = 0; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 0; col < matrixRowSize; col++)
        {
            int color = matrix[row][col];
            if (color == 1)
            {
                matrix[row][col] = 0;
            }
            else if (color == 2)
            {
                matrix[row][col] = 1;
            }
        }
    }

    return matrix;
}

void changeOnesConnectedToBorderToTwos2(vector<vector<int>> &matrix,
                                        int startRow, int startCol)
{
    vector<vector<int>> stack = {{startRow, startCol}};
    while (stack.size() > 0)
    {
        auto currentPosition = stack[stack.size() - 1];
        stack.pop_back();
        int currentRow = currentPosition[0];
        int currentCol = currentPosition[1];
        matrix[currentRow][currentCol] = 2;
        auto neighbors = getNeighbors(matrix, currentRow, currentCol);
        for (auto neighbor : neighbors)
        {
            int row = neighbor[0];
            int col = neighbor[1];
            if (matrix[row][col] != 1)
            {
                continue;
            }
            stack.push_back(neighbor);
        }
    }
}

void print_matrix(vector<vector<int>> &matrix)
{
    for (vector<int> row : matrix)
    {
        for (int col : row)
        {
            cout << col << "  ";
        }
        cout << endl;
    }
}

int main()
{
    vector<vector<int>> source = {
        {1, 0, 0, 0, 0, 0},
        {0, 1, 0, 1, 1, 1},
        {0, 0, 1, 0, 1, 0},
        {1, 1, 0, 0, 1, 0},
        {1, 0, 1, 1, 0, 0},
        {1, 0, 0, 0, 0, 1}};
    removeIslands1(source);
    print_matrix(source);
    source = {
        {1, 0, 0, 0, 0, 0},
        {0, 1, 0, 1, 1, 1},
        {0, 0, 1, 0, 1, 0},
        {1, 1, 0, 0, 1, 0},
        {1, 0, 1, 1, 0, 0},
        {1, 0, 0, 0, 0, 1}};
    removeIslands2(source);
    print_matrix(source);
    return 0;
}
```

## 循环的连接

定义一个表示图的二维数组，一维表示图的顶点，二维表示当前的顶点可以访问其他顶点的索引

如下表示的图中，存在一个循环的连接 `0 -> 1 -> 2 -> 0`

```
[
    [1, 3],
    [2, 3, 4],
    [0],
    [],
    [2, 5],
    []
]
```

```python
def is_node_cycle(node, edges, visited, currently_in_stack):
    visited[node] = True
    currently_in_stack[node] = True

    for vertx in edges[node]:
        if not visited[vertx]:
            if is_node_cycle(vertx, edges, visited, currently_in_stack):
                return True
        elif currently_in_stack[vertx]:
            return True

    currently_in_stack[node] = False
    return False


WHITE, GREY, BLACK = 0, 1, 2


def traverse_and_color_nodes(node, edges, colors):
    colors[node] = GREY

    for vertx in edges[node]:
        color = colors[vertx]
        if color == GREY:
            return True
        if color == BLACK:
            continue
        if traverse_and_color_nodes(vertx, edges, colors):
            return True

    colors[node] = BLACK
    return False


# O(v + e) time | O(v) space
def cycle_in_graph1(edges):
    visited = [False for _ in edges]
    currently_in_stack = [False for _ in edges]
    for node in range(len(edges)):
        if visited[node]:
            continue
        if is_node_cycle(node, edges, visited, currently_in_stack):
            return True

    return False


# O(v + e) time | O(v) space
def cycle_in_graph2(edges):
    colors = [WHITE for _ in edges]

    for node in range(len(edges)):
        if colors[node] != WHITE:
            continue

        if traverse_and_color_nodes(node, edges, colors):
            return True

    return False


if __name__ == '__main__':
    source = [
        [1, 3],
        [2, 3, 4],
        [0],
        [],
        [2, 5],
        []
    ]
    print(cycle_in_graph1(source))
    print(cycle_in_graph2(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int WHITE = 0;
const int GREY = 1;
const int BLACK = 2;

bool isNodeInCycle(int node, vector<vector<int>> &edges,
                   vector<bool> &visited, vector<bool> &currentlyInStack);

bool traverseAndColorNodes(int node, vector<vector<int>> &edges,
                           vector<int> &colors);

// O(v + e) time | O(v) space - where v is the number of
// vertices and e is the number of edges in the graph
bool cycleInGraph1(vector<vector<int>> edges)
{
    int numberOfNodes = edges.size();
    vector<bool> visited(numberOfNodes, false);
    vector<bool> currentlyInStack(numberOfNodes, false);

    for (int node = 0; node < numberOfNodes; node++)
    {
        if (visited[node])
            continue;

        bool containsCycle = isNodeInCycle(node, edges, visited, currentlyInStack);
        if (containsCycle)
            return true;
    }

    return false;
}


bool isNodeInCycle(int node, vector<vector<int>> &edges, vector<bool> &visited,
                   vector<bool> &currentlyInStack)
{
    visited[node] = true;
    currentlyInStack[node] = true;

    auto neighbors = edges[node];
    for (auto neighbor : neighbors)
    {
        if (!visited[neighbor])
        {
            bool containsCycle = isNodeInCycle(neighbor, edges, visited, currentlyInStack);
            if (containsCycle)
                return true;
        }
        else if (currentlyInStack[neighbor])
        {
            return true;
        }
    }

    currentlyInStack[node] = false;
    return false;
}

// O(v + e) time | O(v) space - where v is the number of
// vertices and e is the number of edges in the graph
bool cycleInGraph2(vector<vector<int>> edges)
{
    int numberOfNodes = edges.size();
    vector<int> colors(numberOfNodes, WHITE);

    for (int node = 0; node < numberOfNodes; node++)
    {
        if (colors[node] != WHITE)
            continue;
        bool containsCycle = traverseAndColorNodes(node, edges, colors);
        if (containsCycle)
            return true;
    }

    return false;
}



bool traverseAndColorNodes(int node, vector<vector<int>> &edges,
                           vector<int> &colors)
{
    colors[node] = GREY;

    auto neighbors = edges[node];
    for (auto neighbor : neighbors)
    {
        int neighborColor = colors[neighbor];
        if (neighborColor == GREY)
            return true;
        if (neighborColor == BLACK)
            continue;
        bool containsCycle = traverseAndColorNodes(neighbor, edges, colors);
        if (containsCycle)
            return true;
    }

    colors[node] = BLACK;
    return false;
}

int main()
{
    vector<vector<int>> source =
    {
        {1, 3},
        {2, 3, 4},
        {0},
        {},
        {2, 5},
        {}
    };
    cout << cycleInGraph1(source);
    cout << cycleInGraph2(source);
    return 0;
}
```

## 最小通过次数

给定一个包含整数元素的矩阵，规定矩阵中正数周围的负数（上下左右）可以转为正数，求出转化所有负数为正数的最小次数，如果存在无法转化的负数，则转化次数为 `-1`。

例，给出以下矩阵，矩阵中存在负数

```
[
    [ 0, -2, -1],
    [-5,  2,  0],
    [-6, -2,  0]
]
```

第一次通过后，通过位置`(1, 1)`的正数将一部分负数转为了正数

```
[
    [ 0, 2, -1],
    [ 5, 2,  0],
    [-6, 2,  0]
]
```

再次通过，通过位置`(0, 1)`和`(1, 0)`的正数将剩余的负数转为了正数

```
[
    [ 0, 2, 1],
    [ 5, 2, 0],
    [ 6, 2, 0]
]
```

此时矩阵内已经没有负数可以转化，最后得出的最小通过次数为 `2`

```python
def get_adjacent_positions(row, col, matrix):
    adjacent_positions = []
    if row > 0 > matrix[row - 1][col]:
        matrix[row - 1][col] *= -1
        adjacent_positions.append((row - 1, col,))
    if row < len(matrix) - 1 and matrix[row + 1][col] < 0:
        matrix[row + 1][col] *= -1
        adjacent_positions.append((row + 1, col,))
    if col > 0 > matrix[row][col - 1]:
        matrix[row][col - 1] *= -1
        adjacent_positions.append((row, col - 1,))
    if col < len(matrix[row]) - 1 and matrix[row][col + 1] < 0:
        matrix[row][col + 1] *= -1
        adjacent_positions.append((row, col + 1,))
    return adjacent_positions


def get_all_positive_positions(matrix):
    positive_positions = []
    for row in range(len(matrix)):
        for col in range(len(matrix[row])):
            if matrix[row][col] > 0:
                positive_positions.append((row, col,))

    return positive_positions


def contains_negative(matrix):
    for row in range(len(matrix)):
        for col in range(len(matrix[row])):
            if matrix[row][col] < 0:
                return True

    return False


# O(w * h) time | O(w * h) space
def minimum_passes_of_matrix(matrix):
    queue = get_all_positive_positions(matrix)
    minimum_passes = 0

    while len(queue):
        number_of_process = len(queue)

        while number_of_process > 0:
            row, col = queue.pop(0)
            adjacent_positions = get_adjacent_positions(row, col, matrix)
            for position in adjacent_positions:
                queue.append(position)
            number_of_process -= 1

        minimum_passes += 1

    return minimum_passes - 1 if not contains_negative(matrix) else -1


if __name__ == '__main__':
    source = [
        [0, -1, -3, 2, 0],
        [1, -2, -5, -1, -3],
        [3, 0, 0, -4, -1],
    ]
    print(minimum_passes_of_matrix(source))
```

```cpp
#include <iostream>
#include <vector>
using namespace std;

int convertNegatives(vector<vector<int>> &matrix);
vector<vector<int>> getAllPositivePositions(const vector<vector<int>> &matrix);
vector<vector<int>> getAdjacentPositions(int row, int col,
                 const vector<vector<int>> &matrix);
bool containsNegative(const vector<vector<int>> &matrix);

// O(w * h) time | O(w * h) space - where w is the
// width of the matrix and h is the height
int minimumPassesOfMatrix(vector<vector<int>> matrix)
{
    int passes = convertNegatives(matrix);
    return !containsNegative(matrix) ? passes - 1 : -1;
}

int convertNegatives(vector<vector<int>> &matrix)
{
    vector<vector<int>> queue = getAllPositivePositions(matrix);

    int passes = 0;

    while (queue.size() > 0)
    {
        int currentSize = queue.size();

        while (currentSize > 0)
        {
            // In C++, removing elements from the start of a vector is an O(n)-time
            // operation. To make this an O(1)-time operation, we could use a more
            // legitimate queue structure. For our time complexity analysis, we'll
            // assume this runs in O(1) time.
            auto currentPosition = queue.front();
            queue.erase(queue.begin());
            int currentRow = currentPosition[0];
            int currentCol = currentPosition[1];

            vector<vector<int>> adjacentPositions =
                                 getAdjacentPositions(currentRow, currentCol, matrix);
            for (const auto &position : adjacentPositions)
            {
                int row = position[0];
                int col = position[1];

                int value = matrix[row][col];
                if (value < 0)
                {
                    matrix[row][col] *= -1;
                    queue.push_back({row, col});
                }
            }

            currentSize--;
        }

        passes++;
    }

    return passes;
}

vector<vector<int>> getAllPositivePositions(const vector<vector<int>> &matrix)
{
    vector<vector<int>> positivePositions;
    const int matrixSize = matrix.size();
    for (int row = 0; row < matrixSize; row++)
    {
        const int matrixRowSize = matrix[row].size();
        for (int col = 0; col < matrixRowSize; col++)
        {
            int value = matrix[row][col];
            if (value > 0)
                positivePositions.push_back({row, col});
        }
    }

    return positivePositions;
}

vector<vector<int>> getAdjacentPositions(int row, int col,
                 const vector<vector<int>> &matrix)
{
    vector<vector<int>> adjacentPositions;

    if (row > 0)
        adjacentPositions.push_back({row - 1, col});
    const int matrixSize = matrix.size();
    if (row < matrixSize - 1)
        adjacentPositions.push_back({row + 1, col});
    if (col > 0)
        adjacentPositions.push_back({row, col - 1});
    const int matrixRowSize = matrix[row].size();
    if (col < matrixRowSize - 1)
        adjacentPositions.push_back({row, col + 1});

    return adjacentPositions;
}

bool containsNegative(const vector<vector<int>> &matrix)
{
    for (const auto &row : matrix)
    {
        for (const auto &value : row)
        {
            if (value < 0)
                return true;
        }
    }

    return false;
}

int main()
{
    vector<vector<int>> source =
    {
        {0, -1, -3, 2, 0},
        {1, -2, -5, -1, -3},
        {3, 0, 0, -4, -1}
    };
    cout << minimumPassesOfMatrix(source);
    return 0;
}
```

## 迪杰斯特拉算法

Dijkstra 算法，是由荷兰计算机科学家 Edsger Wybe Dijkstra 在 1956 年发现的算法，迪克斯特拉算法使用类似广度优先搜索的方法解决赋权图的单源最短路径问题。Dijkstra 算法原始版本仅适用于找到两个顶点之间的最短路径，后来更常见的变体固定了一个顶点作为源结点然后找到该顶点到图中所有其它结点的最短路径，产生一个最短路径树。本算法每次取出未访问结点中距离最小的，用该结点更新其他结点的距离。

```python
# O(v^2 + e) time | O(v) space - where v is the number of
# vertices and e is the number of edges in the input graph
def dijkstras_algorithm1(start, edges):
    number_of_vertices = len(edges)
    # 记录每个顶点的最短到达路径
    min_distances = [float("inf") for _ in range(number_of_vertices)]
    # 初始位置的最短路径为 0, 即顶点到达自身的距离为 0
    # 这里的预设值是下面循环开始的关键
    # 下面获取开始顶点时默认是找路径最小的作为开始
    min_distances[start] = 0
    # 缓存已经访问过的顶点
    visited = set()
    # 通过缓存的顶点数量来判断图是否已经遍历完
    while len(visited) != number_of_vertices:
        # 获取当前路径最短的顶点
        vertex, current_min_distance = get_vertex_with_min_distance(min_distances, visited)
        # 最短路径为无穷大说明没有找到合适的路径, 结束搜索
        if current_min_distance == float("inf"):
            break
        # 将顶点标记为已访问
        visited.add(vertex)
        # 循环顶点中的有向边
        for edge in edges[vertex]:
            # 解构获得单个边的目标顶点和距离
            destination, distance_to_destination = edge
            # 如果目标顶点已经访问过则忽略
            if destination in visited:
                continue
            # 计算当前最短路径和当前边的路径和
            new_path_distance = current_min_distance + distance_to_destination
            # 获取缓存的到达当前边指向的顶点的路径
            current_destination_distance = min_distances[destination]
            # 如果新计算的路径比缓存的路径小则更新
            if new_path_distance < current_destination_distance:
                min_distances[destination] = new_path_distance
    # 由于上面初始化时, 最短路径都是默认无穷大, 题目要求找不到路径的标记为 -1, 这里做一些过滤
    return list(map(lambda x: -1 if x == float("inf") else x, min_distances))


def get_vertex_with_min_distance(distances, visited):
    # 当前最短路径先默认为无穷大
    current_min_distance = float("inf")
    # 表示当前顶点
    vertex = -1
    # 循环缓存中记录的顶点和已经记录的最短到达路径
    # 由于给定的图(顶点与边)数据是通过邻接表的索引来表示顶点的
    # 所以这里可以直接以数组的索引来表示顶点
    for vertex_idx, distance in enumerate(distances):
        # 顶点已访问过则忽略
        if vertex_idx in visited:
            continue
        # 根据记录中的到达路径更新当前最短到达路径
        if distance <= current_min_distance:
            vertex = vertex_idx
            current_min_distance = distance

    return vertex, current_min_distance


# O((v + e) * log(v)) time | O(v) space - where v is the number
# of vertices and e is the number of edges in the input graph
def dijkstras_algorithm2(start, edges):
    number_of_vertices = len(edges)

    min_distances = [float("inf") for _ in range(number_of_vertices)]
    min_distances[start] = 0
    # 与上面第一种方式不同的是用最小堆来记录
    # 操作最小堆的时间复杂度平均为 log(v)
    min_distances_heap = MinHeap([(idx, float("inf")) for idx in range(number_of_vertices)])
    min_distances_heap.update(start, 0)

    while not min_distances_heap.is_empty():
        vertex, current_min_distance = min_distances_heap.remove()

        if current_min_distance == float("inf"):
            break

        for edge in edges[vertex]:
            destination, distance_to_destination = edge

            new_path_distance = current_min_distance + distance_to_destination
            current_destination_distance = min_distances[destination]
            if new_path_distance < current_destination_distance:
                min_distances[destination] = new_path_distance
                min_distances_heap.update(destination, new_path_distance)

    return list(map(lambda x: -1 if x == float("inf") else x, min_distances))


class MinHeap:
    def __init__(self, array):
        # 记录每个顶点在堆中的位置, key 表示堆中相应的数组索引, value 表示顶点
        self.vertex_map = {idx: idx for idx in range(len(array))}
        # 最小堆的实际存储方式是数组, 数组索引 i 表示堆中的节点
        # 则 2 * i + 1 表示左子树, 而 2 * i + 2 表示右子树
        self.heap = self.build_heap(array)

    def is_empty(self):
        return len(self.heap) == 0

    # O(n) time | O(1) space
    def build_heap(self, array):
        # 构建最小堆, 从中间位置开始向前下坠节点
        first_parent_idx = (len(array) - 2) // 2
        for current_idx in reversed(range(first_parent_idx + 1)):
            self.sift_down(current_idx, len(array) - 1, array)
        return array

    # O(log(n)) time | O(1) space
    def sift_down(self, current_idx, end_idx, heap):
        child_one_idx = current_idx * 2 + 1
        while child_one_idx <= end_idx:
            child_two_idx = current_idx * 2 + 2 if current_idx * 2 + 2 <= end_idx else -1
            # 根据加权图的边的距离来判断大小下坠节点
            if child_two_idx != -1 and heap[child_two_idx][1] < heap[child_one_idx][1]:
                idx_to_swap = child_two_idx
            else:
                idx_to_swap = child_one_idx
            # 根据加权图的边的距离来判断大小下坠节点
            if heap[idx_to_swap][1] < heap[current_idx][1]:
                self.swap(current_idx, idx_to_swap, heap)
                current_idx = idx_to_swap
                child_one_idx = current_idx * 2 + 1
            else:
                return

    # O(log(n)) time | O(1) space
    def sift_up(self, current_idx, heap):
        parent_idx = (current_idx - 1) // 2
        # 根据加权图的边的距离来判断大小上升节点
        while current_idx > 0 and heap[current_idx][1] < heap[parent_idx][1]:
            self.swap(current_idx, parent_idx, heap)
            current_idx = parent_idx
            parent_idx = (current_idx - 1) // 2

    # O(log(n)) time | O(1) space
    def remove(self):
        if self.is_empty():
            return
        # 将堆的顶端节点交换至末尾
        self.swap(0, len(self.heap) - 1, self.heap)
        # 取出末尾的节点数据
        vertex, distance = self.heap.pop()
        # 清理缓存记录
        self.vertex_map.pop(vertex)
        # 重新下坠顶端节点
        self.sift_down(0, len(self.heap) - 1, self.heap)
        # 返回顶端节点
        return vertex, distance

    def swap(self, i, j, heap):
        # 更新缓存, 交换记录的顶点位置
        self.vertex_map[heap[i][0]] = j
        self.vertex_map[heap[j][0]] = i
        heap[i], heap[j] = heap[j], heap[i]

    def update(self, vertex, value):
        self.heap[self.vertex_map[vertex]] = (vertex, value)
        self.sift_up(self.vertex_map[vertex], self.heap)


if __name__ == '__main__':
    # 起始顶点
    start = 0
    # 使用邻接表表示的加权图
    edges = [[[1, 7]], [[2, 6], [3, 20], [4, 3]], [[3, 14]], [[4, 2]], [], []]
    print(dijkstras_algorithm1(start, edges))
    print(dijkstras_algorithm2(start, edges))
```

```cpp
#include <iostream>
#include <vector>
#include <limits>
#include <unordered_map>
#include <set>
#include <tuple>
using namespace std;

tuple<int, int> getVertexWithMinDistances(vector<int> minDistances,
                                          set<int> distances);

// O(v^2 + e) time | O(v) space - where v is the number of
// vertices and e is the number of edges in the input graph
vector<int> dijkstrasAlgorithm1(int start, vector<vector<vector<int>>> edges)
{
    size_t numberOfVertices = edges.size();

    vector<int> minDistances(edges.size(), numeric_limits<int>::max());
    minDistances[start] = 0;

    set<int> visited;

    while (visited.size() != numberOfVertices)
    {
        int vertex = 0;
        int currentMinDistance = 0;
        tuple<int, int> temp =
            getVertexWithMinDistances(minDistances, visited);
        vertex = get<0>(temp);
        currentMinDistance = get<1>(temp);
        if (currentMinDistance == numeric_limits<int>::max())
        {
            break;
        }

        visited.insert(vertex);

        for (auto edge : edges[vertex])
        {
            auto destination = edge[0];
            auto distanceToDestination = edge[1];

            if (visited.find(destination) != visited.end())
            {
                continue;
            }

            auto newPathDistance = currentMinDistance + distanceToDestination;
            auto currentDestinationDistance = minDistances[destination];
            if (newPathDistance < currentDestinationDistance)
            {
                minDistances[destination] = newPathDistance;
            }
        }
    }

    vector<int> finalDistances;
    for (auto distance : minDistances)
    {
        if (distance == numeric_limits<int>::max())
        {
            finalDistances.push_back(-1);
        }
        else
        {
            finalDistances.push_back(distance);
        }
    }
    return finalDistances;
}

tuple<int, int> getVertexWithMinDistances(vector<int> distances,
                                          set<int> visited)
{
    int currentMinDistance = numeric_limits<int>::max();
    int vertex = -1;

    for (size_t vertexIdx = 0; vertexIdx < distances.size(); vertexIdx++)
    {
        int distance = distances[vertexIdx];

        if (visited.find(vertexIdx) != visited.end())
        {
            continue;
        }

        if (distance <= currentMinDistance)
        {
            vertex = vertexIdx;
            currentMinDistance = distance;
        }
    }

    return tuple<int, int>{vertex, currentMinDistance};
}

struct Item
{
    int vertex, distance;
};

class MinHeap
{
public:
    vector<Item> heap;
    unordered_map<int, int> vertexMap;

    MinHeap(vector<Item> array)
    {
        for (auto item : array)
        {
            vertexMap[item.vertex] = item.vertex;
        }
        heap = buildHeap(array);
    }

    // O(n) time | O(1) space
    vector<Item> buildHeap(vector<Item> &array)
    {
        int firstParentIdx = (array.size() - 2) / 2;
        for (int currentIdx = firstParentIdx + 1; currentIdx >= 0; currentIdx--)
        {
            siftDown(currentIdx, array.size() - 1, array);
        }
        return array;
    }

    bool isEmpty() { return heap.size() == 0; }

    // O(log(n)) time | O(1) space
    void siftDown(int currentIdx, int endIdx, vector<Item> &heap)
    {
        int childOneIdx = currentIdx * 2 + 1;
        while (childOneIdx <= endIdx)
        {
            int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
            int idxToSwap;
            if (childTwoIdx != -1 &&
                heap[childTwoIdx].distance < heap[childOneIdx].distance)
            {
                idxToSwap = childTwoIdx;
            }
            else
            {
                idxToSwap = childOneIdx;
            }
            if (heap[idxToSwap].distance < heap[currentIdx].distance)
            {
                swap(currentIdx, idxToSwap);
                currentIdx = idxToSwap;
                childOneIdx = currentIdx * 2 + 1;
            }
            else
            {
                return;
            }
        }
    }

    // O(log(n)) time | O(1) space
    void siftUp(int currentIdx)
    {
        int parentIdx = (currentIdx - 1) / 2;
        while (currentIdx > 0 &&
               heap[currentIdx].distance < heap[parentIdx].distance)
        {
            swap(currentIdx, parentIdx);
            currentIdx = parentIdx;
            parentIdx = (currentIdx - 1) / 2;
        }
    }

    Item remove()
    {
        swap(0, heap.size() - 1);
        Item temp = heap.back();
        const int vertex = temp.vertex;
        const int distance = temp.distance;
        heap.pop_back();
        vertexMap.erase(vertex);
        siftDown(0, heap.size() - 1, heap);
        return Item{vertex, distance};
    }

    void update(int vertex, int value)
    {
        heap[vertexMap[vertex]] = Item{vertex, value};
        siftUp(vertexMap[vertex]);
    }

    void swap(int i, int j)
    {
        vertexMap[heap[i].vertex] = j;
        vertexMap[heap[j].vertex] = i;
        auto temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
};

// O((v + e) * log(v)) time | O(v) space - where v is the number
// of vertices and e is the number of edges in the input graph
vector<int> dijkstrasAlgorithm2(int start, vector<vector<vector<int>>> edges)
{
    const int edgesSize = edges.size();

    vector<int> minDistances(edgesSize, numeric_limits<int>::max());
    minDistances[start] = 0;

    vector<Item> minDistancesPairs;
    for (int i = 0; i < edgesSize; i++)
    {
        minDistancesPairs.push_back(Item{i, numeric_limits<int>::max()});
    }

    MinHeap minDistancesHeap(minDistancesPairs);
    minDistancesHeap.update(start, 0);

    while (!minDistancesHeap.isEmpty())
    {
        Item temp = minDistancesHeap.remove();
        const int vertex = temp.vertex;
        const int currentMinDistance = temp.distance;

        if (currentMinDistance == numeric_limits<int>::max())
        {
            break;
        }

        for (auto edge : edges[vertex])
        {
            auto destination = edge[0];
            auto distanceToDestination = edge[1];
            auto newPathDistance = currentMinDistance + distanceToDestination;
            auto currentDestinationDistance = minDistances[destination];
            if (newPathDistance < currentDestinationDistance)
            {
                minDistances[destination] = newPathDistance;
                minDistancesHeap.update(destination, newPathDistance);
            }
        }
    }

    vector<int> finalDistances;
    for (auto distance : minDistances)
    {
        if (distance == numeric_limits<int>::max())
        {
            finalDistances.push_back(-1);
        }
        else
        {
            finalDistances.push_back(distance);
        }
    }
    return finalDistances;
}

void iteration(vector<int> array)
{
    for (int element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    vector<vector<vector<int>>> source = {{{1, 7}}, {{2, 6}, {3, 20}, {4, 3}}, {{3, 14}}, {{4, 2}}, {}, {}};
    iteration(dijkstrasAlgorithm1(0, source));
    iteration(dijkstrasAlgorithm2(0, source));
    return 0;
}
```

## 拓扑排序

给定一组任务和任务执行的前置依赖，前置依赖表示一个任务执行之前必须先执行另一个要求的任务才能执行此任务，找出在符合每个任务前置依赖的前提下执行该组任务的实际顺序

```python
# O(j + d) time | O(j + d) space
def topological_sort1(jobs, deps):
    # 创建一个有向图, 顶点表示每个任务, 边表示任务相互之间的依赖
    job_graph = create_job_graph1(jobs, deps)
    # 计算任务的执行顺序
    return get_ordered_jobs1(job_graph)


def create_job_graph1(jobs, deps):
    # 先创建一个基础的图
    graph = JobGraph1(jobs)
    # 根据任务的前置依赖创建有向图的边
    for pre_requirement, job in deps:
        graph.add_pre_requirement(job, pre_requirement)
    return graph


def get_ordered_jobs1(graph):
    # 记录已经排序好的任务索引
    ordered_jobs = []
    nodes = graph.nodes
    # 按顺序遍历所有节点
    while len(nodes):
        node = nodes.pop()
        # 深度搜索图的顶点, 并判断是否存在循环依赖的任务
        contains_cycle = depth_first_traverse(node, ordered_jobs)
        # 如果有循环依赖的任务意味着总会有任务因为依赖的原因而无法执行
        # 说明按给定的任务依赖无法顺序执行这组任务
        if contains_cycle:
            return []
    return ordered_jobs


def depth_first_traverse(node, ordered_jobs):
    # 如果顶点已经访问则直接返回
    if node.visited:
        return False
    # 如果顶点已经访问过, 但还从此顶点出发的深度优先遍历还未结束,
    # 意味着已经循环回来了, 有任务存在循环依赖
    if node.visiting:
        return True
    # 设置为搜索中
    node.visiting = True
    # 搜索此顶点对应任务的依赖任务
    for pre_requirement_node in node.pre_requirements:
        # 递归调用深度优先搜索
        contains_cycle = depth_first_traverse(pre_requirement_node, ordered_jobs)
        if contains_cycle:
            return True
    # 搜索结束后标记为已访问
    node.visited = True
    # 标记为已经搜索结束
    node.visiting = False
    # 添加此任务到执行顺序记录表中
    ordered_jobs.append(node.job)
    return False


class JobGraph1:
    def __init__(self, jobs):
        # 记录所有任务节点
        self.nodes = []
        # 记录任务相互间依赖的有向图
        self.graph = {}
        for job in jobs:
            self.add_node(job)

    def add_pre_requirement(self, job, pre_requirement):
        # 获取任务
        job_node = self.get_node(job)
        # 获取任务的前置依赖
        pre_requirement_node = self.get_node(pre_requirement)
        # 记录任务的前置依赖
        job_node.pre_requirements.append(pre_requirement_node)

    def add_node(self, job):
        self.graph[job] = JobNode1(job)
        self.nodes.append(self.graph[job])

    def get_node(self, job):
        # 如果任务不存在于有向图中则添加
        if job not in self.graph:
            self.add_node(job)
        return self.graph[job]


class JobNode1:
    def __init__(self, job):
        # 表示当前任务
        self.job = job
        # 表示任务的前置依赖
        self.pre_requirements = []
        # 记录图的顶点是否已经被访问
        self.visited = False
        # 记录图的顶点是否正被访问且未完成深度优先遍历
        self.visiting = False


# O(j + d) time | O(j + d) space
def topological_sort2(jobs, deps):
    # 创建一个有向图, 顶点表示每个任务, 边表示任务相互之间的依赖
    job_graph = create_job_graph2(jobs, deps)
    return get_ordered_jobs2(job_graph)


def create_job_graph2(jobs, deps):
    # 创建一个基础的图
    graph = JobGraph2(jobs)
    # 通过任务和其依赖创建有向图
    for job, dep in deps:
        graph.add_dep(job, dep)
    return graph


# 与上面的第一种方法不同的是这里直接通过搜索依赖并递减任务的被依赖次数来计算最终的任务执行顺序
def get_ordered_jobs2(graph):
    ordered_jobs = []
    # 找到所有没有前置依赖的任务
    nodes_with_no_pre_requirements = list(filter(lambda element: element.num_of_pre_requirements == 0, graph.nodes))
    # 从没有前置依赖的任务开始逐个搜索
    while len(nodes_with_no_pre_requirements):
        node = nodes_with_no_pre_requirements.pop()
        # 没有前置依赖的任务表面已经解决了依赖, 可以添加早任务顺序记录中
        ordered_jobs.append(node.job)
        # 重新计算所有依赖此任务的任务, 递减依赖此任务的后置任务的依赖次数
        remove_deps(node, nodes_with_no_pre_requirements)
    graph_has_edges = any(node.num_of_pre_requirements for node in graph.nodes)
    return [] if graph_has_edges else ordered_jobs


def remove_deps(node, nodes_with_no_pre_requirements):
    # 循环此任务的所有依赖任务
    while len(node.deps):
        dep = node.deps.pop()
        # 递减任务的依赖次数
        dep.num_of_pre_requirements -= 1
        # 如果任务的已经没有前置依赖, 添加到执行队列中
        if dep.num_of_pre_requirements == 0:
            nodes_with_no_pre_requirements.append(dep)


class JobGraph2:
    def __init__(self, jobs):
        self.nodes = []
        self.graph = {}
        for job in jobs:
            self.add_node(job)

    def add_dep(self, job, dep):
        job_node = self.get_node(job)
        dep_node = self.get_node(dep)
        # 记录任务的依赖
        job_node.deps.append(dep_node)
        # 累加任务的被依赖次数
        dep_node.num_of_pre_requirements += 1

    def add_node(self, job):
        self.graph[job] = JobNode2(job)
        self.nodes.append(self.graph[job])

    def get_node(self, job):
        if job not in self.graph:
            self.add_node(job)
        return self.graph[job]


class JobNode2:
    def __init__(self, job):
        self.job = job
        # 表示此任务的前置依赖
        self.deps = []
        # 表示此任务的被依赖次数
        self.num_of_pre_requirements = 0


if __name__ == '__main__':
    print(topological_sort1([1, 2, 3, 4], [[1, 2], [1, 3], [3, 2], [4, 2], [4, 3]]))
    print(topological_sort2([1, 2, 3, 4], [[1, 2], [1, 3], [3, 2], [4, 2], [4, 3]]))
```

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

class JobNode1
{
public:
    int job;
    vector<JobNode1 *> preRequirement;
    bool visited;
    bool visiting;

    JobNode1(int job);
};

class JobGraph1
{
public:
    vector<JobNode1 *> nodes;
    unordered_map<int, JobNode1 *> graph;

    JobGraph1(vector<int> jobs);
    void addPrereq(int job, int prereq);
    void addNode(int job);
    JobNode1 *getNode(int job);
};

JobGraph1 *createJobGraph1(vector<int> jobs, vector<vector<int>> deps);
vector<int> getOrderedJobs1(JobGraph1 *graph);
bool depthFirstTraverse1(JobNode1 *node, vector<int> *orderedJobs);

// O(j + d) time | O(j + d) space
vector<int> topologicalSort1(vector<int> jobs, vector<vector<int>> deps)
{
    JobGraph1 *jobGraph = createJobGraph1(jobs, deps);
    return getOrderedJobs1(jobGraph);
}

JobGraph1 *createJobGraph1(vector<int> jobs, vector<vector<int>> deps)
{
    JobGraph1 *graph = new JobGraph1(jobs);
    for (vector<int> dep : deps)
    {
        graph->addPrereq(dep[1], dep[0]);
    }
    return graph;
}

vector<int> getOrderedJobs1(JobGraph1 *graph)
{
    vector<int> orderedJobs = {};
    vector<JobNode1 *> nodes = graph->nodes;
    while (nodes.size())
    {
        JobNode1 *node = nodes.back();
        nodes.pop_back();
        bool containsCycle = depthFirstTraverse1(node, &orderedJobs);
        if (containsCycle)
            return {};
    }
    return orderedJobs;
}

bool depthFirstTraverse1(JobNode1 *node, vector<int> *orderedJobs)
{
    if (node->visited)
        return false;
    if (node->visiting)
        return true;
    node->visiting = true;
    for (JobNode1 *prereqNode : node->preRequirement)
    {
        bool containsCycle = depthFirstTraverse1(prereqNode, orderedJobs);
        if (containsCycle)
            return true;
    }
    node->visited = true;
    node->visiting = false;
    orderedJobs->push_back(node->job);
    return false;
}

JobGraph1::JobGraph1(vector<int> jobs)
{
    nodes = {};
    for (int job : jobs)
    {
        addNode(job);
    }
}

void JobGraph1::addPrereq(int job, int prereq)
{
    JobNode1 *jobNode = getNode(job);
    JobNode1 *prereqNode = getNode(prereq);
    jobNode->preRequirement.push_back(prereqNode);
}

void JobGraph1::addNode(int job)
{
    graph[job] = new JobNode1(job);
    nodes.push_back(graph[job]);
}

JobNode1 *JobGraph1::getNode(int job)
{
    if (graph.find(job) == graph.end())
        addNode(job);
    return graph[job];
}

JobNode1::JobNode1(int job)
{
    this->job = job;
    preRequirement = {};
    visited = false;
    visiting = false;
}

class JobNode2
{
public:
    int job;
    vector<JobNode2 *> deps;
    int numOfPreRequirement;

    JobNode2(int job);
};

class JobGraph2
{
public:
    vector<JobNode2 *> nodes;
    unordered_map<int, JobNode2 *> graph;

    JobGraph2(vector<int> jobs);
    void addDep(int job, int dep);
    void addNode(int job);
    JobNode2 *getNode(int job);
};

JobGraph2 *createJobGraph2(vector<int> jobs, vector<vector<int>> deps);
vector<int> getOrderedJobs2(JobGraph2 *graph);
void removeDeps2(JobNode2 *node, vector<JobNode2 *> *nodesWithNoPreRequirement);

// O(j + d) time | O(j + d) space
vector<int> topologicalSort2(vector<int> jobs, vector<vector<int>> deps)
{
    JobGraph2 *jobGraph = createJobGraph2(jobs, deps);
    return getOrderedJobs2(jobGraph);
}

JobGraph2 *createJobGraph2(vector<int> jobs, vector<vector<int>> deps)
{
    JobGraph2 *graph = new JobGraph2(jobs);
    for (vector<int> dep : deps)
    {
        graph->addDep(dep[0], dep[1]);
    }
    return graph;
}

vector<int> getOrderedJobs2(JobGraph2 *graph)
{
    vector<int> orderedJobs = {};
    vector<JobNode2 *> nodesWithNoPreRequirement(graph->nodes.size());
    auto it = copy_if(graph->nodes.begin(), graph->nodes.end(),
                      nodesWithNoPreRequirement.begin(),
                      [](JobNode2 *node)
                      { return node->numOfPreRequirement == 0; });
    nodesWithNoPreRequirement.resize(distance(nodesWithNoPreRequirement.begin(), it));
    while (nodesWithNoPreRequirement.size())
    {
        JobNode2 *node = nodesWithNoPreRequirement.back();
        nodesWithNoPreRequirement.pop_back();
        orderedJobs.push_back(node->job);
        removeDeps2(node, &nodesWithNoPreRequirement);
    }
    bool graphHasEdges = false;
    for (JobNode2 *node : graph->nodes)
    {
        if (node->numOfPreRequirement)
        {
            graphHasEdges = true;
        }
    }
    return graphHasEdges ? vector<int>{} : orderedJobs;
}

void removeDeps2(JobNode2 *node, vector<JobNode2 *> *nodesWithNoPreRequirement)
{
    while (node->deps.size())
    {
        JobNode2 *dep = node->deps.back();
        node->deps.pop_back();
        dep->numOfPreRequirement--;
        if (!dep->numOfPreRequirement)
            nodesWithNoPreRequirement->push_back(dep);
    }
}

JobGraph2::JobGraph2(vector<int> jobs)
{
    nodes = {};
    for (int job : jobs)
    {
        addNode(job);
    }
}

void JobGraph2::addDep(int job, int dep)
{
    JobNode2 *jobNode = getNode(job);
    JobNode2 *depNode = getNode(dep);
    jobNode->deps.push_back(depNode);
    depNode->numOfPreRequirement++;
}

void JobGraph2::addNode(int job)
{
    graph[job] = new JobNode2(job);
    nodes.push_back(graph[job]);
}

JobNode2 *JobGraph2::getNode(int job)
{
    if (graph.find(job) == graph.end())
        addNode(job);
    return graph[job];
}

JobNode2::JobNode2(int job)
{
    this->job = job;
    deps = {};
    numOfPreRequirement = 0;
}

void iteration(vector<int> array)
{
    for (int element : array)
    {
        cout << element << " ";
    }
    cout << endl;
}

int main()
{
    vector<int> jobs = {1, 2, 3, 4};
    vector<vector<int>> deps = {{1, 2}, {1, 3}, {3, 2}, {4, 2}, {4, 3}};
    iteration(topologicalSort1(jobs, deps));
    iteration(topologicalSort2(jobs, deps));
    return 0;
}
```

## 拼词板

给定一个二维数组和一个字符串数组，二维数组的每个元素表示单个字母，遍历字符串数组，判断每个字符串是否可以使用二维数组中的单词拼接出来，
要求在二维数组中拼成字符串的单词是相连的（上、下、左、右、左上、右上、左下、右下），且使用过的字符不可以再次使用，假设二维数组中存在三个位置连续的字符`a`、`b`、`c`，
这三个连续的字符可以拼接出`abc`和`cba`，但是不能拼出`abcb`，因为`b`字母已经使用过了

```python
# O(nm*8^s + ws) time | O(nm + ws) space
def boggle_board(board, words):
    # 创建一个字典树, 记录所有的字符串到树中, 方便访问
    trie = Trie()
    for word in words:
        trie.add(word)
    # 记录可以拼出的字符串
    final_words = {}
    # 标记或者追踪已经访问过的位置, 因为要求字符不可重复使用
    visited = [[False for _ in row] for row in board]
    # 从每一个字母位置开始深度优先搜索
    for row in range(len(board)):
        for col in range(len(board[row])):
            explore(row, col, board, trie.root, visited, final_words)
    # 最后返回找到的可以拼接出来的所有字符串
    return list(final_words.keys())


def explore(row, col, board, trie_node, visited, final_words):
    # 已经使用过的字母不可以重复使用
    if visited[row][col]:
        return
    letter = board[row][col]
    # 字母不在字典树中, 结束递归
    if letter not in trie_node:
        return
    # 标记为已访问
    visited[row][col] = True
    # 切换到字典树的下一个字母
    trie_node = trie_node[letter]
    # 如果已经到达字典树的末尾, 表示可以拼接出字符串
    if "*" in trie_node:
        # 记录拼接成功的字符串
        final_words[trie_node["*"]] = True
    # 找到相邻的节点, 上、下、左、右、左上、右上、左下、右下
    neighbors = get_neighbors(row, col, board)
    for neighbor in neighbors:
        # 在相邻的位置中继续查找, 即深度优先搜索
        explore(neighbor[0], neighbor[1], board, trie_node, visited, final_words)
    # 标记为未访问
    visited[row][col] = False


def get_neighbors(row, col, board):
    board_size = len(board)
    board_row_size = len(board[0])
    neighbors = []
    # 添加相邻的位置并考虑索引越界的情况
    # 包括: 上、下、左、右、左上、右上、左下、右下
    if row > 0 and col > 0:
        neighbors.append([row - 1, col - 1])
    if row > 0 and col < board_row_size - 1:
        neighbors.append([row - 1, col + 1])
    if row < board_size - 1 and col < board_row_size - 1:
        neighbors.append([row + 1, col + 1])
    if row < board_size - 1 and col > 0:
        neighbors.append([row + 1, col - 1])
    if row > 0:
        neighbors.append([row - 1, col])
    if row < board_size - 1:
        neighbors.append([row + 1, col])
    if col > 0:
        neighbors.append([row, col - 1])
    if col < board_row_size - 1:
        neighbors.append([row, col + 1])
    return neighbors


class Trie:
    def __init__(self):
        self.root = {}
        self.endSymbol = "*"

    def add(self, word):
        current = self.root
        for letter in word:
            if letter not in current:
                current[letter] = {}
            current = current[letter]
        # 字典树的末端保存最终字符串, 方便记录
        current[self.endSymbol] = word


if __name__ == '__main__':
    print(boggle_board([
        ["y", "g", "f", "y", "e", "i"],
        ["c", "o", "r", "p", "o", "u"],
        ["j", "u", "z", "s", "e", "l"],
        ["s", "y", "u", "r", "h", "p"],
        ["e", "a", "e", "g", "n", "d"],
        ["h", "e", "l", "s", "a", "t"]
    ], ["san", "sana", "at", "vomit", "yours", "help", "end", "been", "bed", "danger", "calm", "ok", "chaos",
        "complete", "rear", "going", "storm", "face", "epual", "dangerous"]))
```

```cpp
#include <iostream>
#include <unordered_set>
#include <unordered_map>
#include <vector>
using namespace std;

class TrieNode
{
public:
    unordered_map<char, TrieNode *> children;
    string word = "";
};

class Trie
{
public:
    TrieNode *root;
    char endSymbol;

    Trie();
    void add(string str);
};

void explore(int i, int j, vector<vector<char>> board, TrieNode *trieNode,
             vector<vector<bool>> *visited, unordered_set<string> *finalWords);
vector<vector<int>> getNeighbors(int i, int j, vector<vector<char>> board);

// O(nm*8^s + ws) time | O(nm + ws) space
vector<string> boggleBoard(vector<vector<char>> board, vector<string> words)
{
    Trie trie;
    for (string word : words)
    {
        trie.add(word);
    }
    unordered_set<string> finalWords;
    vector<vector<bool>> visited(board.size(),
                                 vector<bool>(board[0].size(), false));
    const int boardSize = board.size();
    for (int row = 0; row < boardSize; row++)
    {
        const int boardRowSize = board[0].size();
        for (int col = 0; col < boardRowSize; col++)
        {
            explore(row, col, board, trie.root, &visited, &finalWords);
        }
    }
    vector<string> finalWordsArray;
    for (auto it : finalWords)
    {
        finalWordsArray.push_back(it);
    }
    return finalWordsArray;
}

void explore(int i, int j, vector<vector<char>> board, TrieNode *trieNode,
             vector<vector<bool>> *visited, unordered_set<string> *finalWords)
{
    if (visited->at(i)[j])
    {
        return;
    }
    char letter = board[i][j];
    if (trieNode->children.find(letter) == trieNode->children.end())
    {
        return;
    }
    visited->at(i)[j] = true;
    trieNode = trieNode->children[letter];
    if (trieNode->children.find('*') != trieNode->children.end())
    {
        finalWords->insert(trieNode->word);
    }
    vector<vector<int>> neighbors = getNeighbors(i, j, board);
    for (vector<int> neighbor : neighbors)
    {
        explore(neighbor[0], neighbor[1], board, trieNode, visited, finalWords);
    }
    visited->at(i)[j] = false;
}

vector<vector<int>> getNeighbors(int i, int j, vector<vector<char>> board)
{
    const int boardSize = board.size();
    const int boardRowSize = board[0].size();
    vector<vector<int>> neighbors;
    if (i > 0 && j > 0)
    {
        neighbors.push_back({i - 1, j - 1});
    }
    if (i > 0 && j < boardRowSize - 1)
    {
        neighbors.push_back({i - 1, j + 1});
    }
    if (i < boardSize - 1 && j < boardRowSize - 1)
    {
        neighbors.push_back({i + 1, j + 1});
    }
    if (i < boardSize - 1 && j > 0)
    {
        neighbors.push_back({i + 1, j - 1});
    }
    if (i > 0)
    {
        neighbors.push_back({i - 1, j});
    }
    if (i < boardSize - 1)
    {
        neighbors.push_back({i + 1, j});
    }
    if (j > 0)
    {
        neighbors.push_back({i, j - 1});
    }
    if (j < boardRowSize - 1)
    {
        neighbors.push_back({i, j + 1});
    }
    return neighbors;
}

Trie::Trie()
{
    this->root = new TrieNode();
    this->endSymbol = '*';
}

void Trie::add(string str)
{
    TrieNode *node = this->root;
    for (char letter : str)
    {
        if (node->children.find(letter) == node->children.end())
        {
            TrieNode *newNode = new TrieNode();
            node->children.insert({letter, newNode});
        }
        node = node->children[letter];
    }
    node->children.insert({this->endSymbol, nullptr});
    node->word = str;
}

int main()
{
    vector<vector<char>> boards = {
        {'y', 'g', 'f', 'y', 'e', 'i'},
        {'c', 'o', 'r', 'p', 'o', 'u'},
        {'j', 'u', 'z', 's', 'e', 'l'},
        {'s', 'y', 'u', 'r', 'h', 'p'},
        {'e', 'a', 'e', 'g', 'n', 'd'},
        {'h', 'e', 'l', 's', 'a', 't'}};
    vector<string> words = {"san", "sana", "at", "vomit", "yours", "help", "end", "been", "bed", "danger", "calm", "ok", "chaos", "complete", "rear", "going", "storm", "face", "epual", "dangerous"};
    vector<string> result = boggleBoard(boards, words);
    for (string element : result)
    {
        cout << element << " ";
    }
    return 0;
}
```

Last Modified 2022-03-29
