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

Last Modified 2021-12-19
