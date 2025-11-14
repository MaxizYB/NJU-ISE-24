# DFS & BFS

下面按照知识点进行讲解，并提供对应 LeetCode 题目与题目链接，以及常用的 C++ 示例模板代码（可直接使用）。

---

## 0. 通用 DFS / BFS 模板（C++）

### **DFS（递归）模板**

```cpp
void dfs(int x, int y) {
    if (!合法) return;
    vis[x][y] = true;
    for (auto &d : dirs) {
        int nx = x + d[0], ny = y + d[1];
        dfs(nx, ny);
    }
}
```

### **BFS 模板**

```cpp
queue<pair<int,int>> q;
q.push({sx, sy});
vis[sx][sy] = true;
while (!q.empty()) {
    auto [x, y] = q.front(); q.pop();
    for (auto &d : dirs) {
        int nx = x + d[0], ny = y + d[1];
        if (!合法) continue;
        vis[nx][ny] = true;
        q.push({nx, ny});
    }
}
```

---

## 1. 图的遍历基础

### 知识点

* DFS：适合探索连通性、区域、递归结构
* BFS：适合最短路径、层级遍历

---

### 练习题

#### **DFS**

* 200. Number of Islands（岛屿数量）  [https://leetcode.com/problems/number-of-islands/](https://leetcode.com/problems/number-of-islands/)
* 695. Max Area of Island（岛屿最大面积） [https://leetcode.com/problems/max-area-of-island/](https://leetcode.com/problems/max-area-of-island/)
* 417. Pacific Atlantic Water Flow（双海洋） [https://leetcode.com/problems/pacific-atlantic-water-flow/](https://leetcode.com/problems/pacific-atlantic-water-flow/)

#### **BFS**

* 102. Binary Tree Level Order Traversal [https://leetcode.com/problems/binary-tree-level-order-traversal/](https://leetcode.com/problems/binary-tree-level-order-traversal/)
* 994. Rotting Oranges [https://leetcode.com/problems/rotting-oranges/](https://leetcode.com/problems/rotting-oranges/)
* 752. Open the Lock [https://leetcode.com/problems/open-the-lock/](https://leetcode.com/problems/open-the-lock/)

---

## 2. 树的遍历

### 知识点

* DFS：前中后序
* BFS：层序
* 应用：路径总和、深度、视图

### C++ 示例：二叉树 DFS

```cpp
void dfs(TreeNode* root) {
    if (!root) return;
    dfs(root->left);
    dfs(root->right);
}
```

### 练习题

* 104. Maximum Depth of Binary Tree  [https://leetcode.com/problems/maximum-depth-of-binary-tree/](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
* 112. Path Sum  [https://leetcode.com/problems/path-sum/](https://leetcode.com/problems/path-sum/)
* 113. Path Sum II  [https://leetcode.com/problems/path-sum-ii/](https://leetcode.com/problems/path-sum-ii/)
* 199. Binary Tree Right Side View  [https://leetcode.com/problems/binary-tree-right-side-view/](https://leetcode.com/problems/binary-tree-right-side-view/)

---

## 3. 网格搜索（Grid Search）

### 知识点

* DFS：连通块、染色、区域
* BFS：最短路径、距离
* 常用方向数组：

```cpp
vector<vector<int>> dirs = {{1,0},{-1,0},{0,1},{0,-1}};
```

### 练习题

* 130. Surrounded Regions  [https://leetcode.com/problems/surrounded-regions/](https://leetcode.com/problems/surrounded-regions/)
* 79. Word Search  [https://leetcode.com/problems/word-search/](https://leetcode.com/problems/word-search/)
* 733. Flood Fill  [https://leetcode.com/problems/flood-fill/](https://leetcode.com/problems/flood-fill/)
* 542. 01 Matrix（BFS） [https://leetcode.com/problems/01-matrix/](https://leetcode.com/problems/01-matrix/)

---

## 4. 图的连通性 / 拓扑排序

### 知识点

* DFS：判断环、连通分量
* BFS：拓扑排序（Kahn）

### 练习题

* 207. Course Schedule  [https://leetcode.com/problems/course-schedule/](https://leetcode.com/problems/course-schedule/)
* 210. Course Schedule II  [https://leetcode.com/problems/course-schedule-ii/](https://leetcode.com/problems/course-schedule-ii/)
* 323. Number of Connected Components  [https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)
* 261. Graph Valid Tree  [https://leetcode.com/problems/graph-valid-tree/](https://leetcode.com/problems/graph-valid-tree/)

---

## 5. BFS 最短路径专题

### 知识点

* BFS 是无权图的最短路径算法

### C++ BFS 模板（最短路径）

```cpp
int steps = 0;
queue<int> q;
q.push(start);
visited.insert(start);
while (!q.empty()) {
    int sz = q.size();
    while (sz--) {
        auto cur = q.front(); q.pop();
        if (cur == target) return steps;
        for (auto &nxt : neighbors(cur)) {
            if (!visited.count(nxt)) {
                visited.insert(nxt);
                q.push(nxt);
            }
        }
    }
    steps++;
}
```

### 练习题

* 127. Word Ladder  [https://leetcode.com/problems/word-ladder/](https://leetcode.com/problems/word-ladder/)
* 815. Bus Routes [https://leetcode.com/problems/bus-routes/](https://leetcode.com/problems/bus-routes/)
* 934. Shortest Bridge [https://leetcode.com/problems/shortest-bridge/](https://leetcode.com/problems/shortest-bridge/)
* 490. The Maze [https://leetcode.com/problems/the-maze/](https://leetcode.com/problems/the-maze/)

---

## 6. DFS 回溯（Backtracking）

### 知识点

* 本质是 DFS + 状态选择与回退
* 常处理排列、组合、子集

### 典型模板（C++）

```cpp
void dfs(vector<int>& path) {
    if (终止条件) {
        ans.push_back(path);
        return;
    }
    for (...) {
        path.push_back(x);
        dfs(path);
        path.pop_back();
    }
}
```

### 练习题

* 46. Permutations  [https://leetcode.com/problems/permutations/](https://leetcode.com/problems/permutations/)
* 78. Subsets [https://leetcode.com/problems/subsets/](https://leetcode.com/problems/subsets/)
* 79. Word Search [https://leetcode.com/problems/word-search/](https://leetcode.com/problems/word-search/)
* 90. Subsets II [https://leetcode.com/problems/subsets-ii/](https://leetcode.com/problems/subsets-ii/)

---

## 7. 双向 BFS

### 知识点

* 提升 BFS 性能
* 两端同时扩展

### 练习题

* 127. Word Ladder（强烈推荐）
* 815. Bus Routes

---

## 8. 状态压缩图搜索（bitmask BFS）

### 知识点

* 状态空间大时用位压缩表示状态
* BFS 求最短步数

### 练习题

* 864. Shortest Path to Get All Keys  [https://leetcode.com/problems/shortest-path-to-get-all-keys/](https://leetcode.com/problems/shortest-path-to-get-all-keys/)
* 847. Shortest Path Visiting All Nodes  [https://leetcode.com/problems/shortest-path-visiting-all-nodes/](https://leetcode.com/problems/shortest-path-visiting-all-nodes/)

---

## 9. 多源 BFS

### 知识点

将所有起点同时入队 → 一次 BFS

### 练习题

* 994. Rotting Oranges [https://leetcode.com/problems/rotting-oranges/](https://leetcode.com/problems/rotting-oranges/)
* 542. 01 Matrix [https://leetcode.com/problems/01-matrix/](https://leetcode.com/problems/01-matrix/)

---

## 10. DFS/BFS + DP 高级题

### 知识点

* DFS 用于树 DP
* BFS 用于距离预处理

### 练习题

* 124. Binary Tree Maximum Path Sum  [https://leetcode.com/problems/binary-tree-maximum-path-sum/](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
* 337. House Robber III  [https://leetcode.com/problems/house-robber-iii/](https://leetcode.com/problems/house-robber-iii/)
* 1162. As Far from Land as Possible  [https://leetcode.com/problems/as-far-from-land-as-possible/](https://leetcode.com/problems/as-far-from-land-as-possible/)

---

