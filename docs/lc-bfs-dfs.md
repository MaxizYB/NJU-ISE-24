# 图及其遍历（BFS & DFS）

## 有向无环图中一个节点的所有祖先

### 方法1：暴力反向DFS

把所有边反向，`answer[i]`为能从节点`i`出发通过DFS到达的所有节点，所以我们只需要对于图中的每一个节点都进行一次DFS，就能找到所有节点的祖先。设共有V个顶点，E条边，该方法复杂度为 $O(V(V+E))$ 

```cpp
class Solution {
public:
    vector<vector<int>> getAncestors(int n, vector<vector<int>> &edges) {
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[1]].push_back(e[0]); 
        }

        vector<vector<int>> ans(n);
        vector<int> vis(n);
        function<void(int)> dfs = [&](int x) {
            vis[x] = true; 
            for (int y : g[x]) {
                if (!vis[y]) {
                    dfs(y); 
                }
            }
        };
        for (int i = 0; i < n; i++) {
            ranges::fill(vis, false);
            dfs(i); 
            vis[i] = false; 
            for (int j = 0; j < n; j++) {
                if (vis[j]) {
                    ans[i].push_back(j);
                }
            }
        }
        return ans;
    }
};
```

### 方法2：拓扑排序

对于每一个节点，它的祖先是所有直接父节点+直接父节点的祖先的并集，也就是当前节点的答案依赖于它的父节点的答案，这隐含了一个拓扑排序。我们可以先对所有节点进行拓扑排序，再对排序好的数组从小到大遍历，使用另外一个数组记录反图，每次遍历的时候通过反图找到直接父节点，再将所有父节点答案合并即可。时间复杂度： $O(V^2)$ 

```cpp
class Solution {
public:
    vector<vector<int>> getAncestors(int n, vector<vector<int>>& edges) {
        // 1. 构建图结构
        vector<vector<int>> g(n);
        vector<vector<int>> g_reverse(n);
        for (int i = 0; i < edges.size(); i++) {
            g[edges[i][0]].push_back(edges[i][1]);
            g_reverse[edges[i][1]].push_back(edges[i][0]);
        }
        
        // 2. 拓扑排序
        vector<int> topo;
        vector<int> vis(n);
        auto dfs = [&](this auto&& dfs, int pos) -> int {
            vis[pos] = 1;
            for (auto u : g[pos]) {
                if (!vis[u]) {
                    dfs(u);
                }
            }
            topo.push_back(pos);
            return 1;
        };
        
        for (int i = 0; i < n; i++) {
            if (!vis[i]) {
                dfs(i);
            }
        }
        reverse(topo.begin(), topo.end());
        
        // 3. 按拓扑序计算祖先集合
        vector<vector<int>> ans(n);
        for (int u : topo) {
            set<int> ancestors;
            for (int parent : g_reverse[u]) {
                ancestors.insert(parent);
                for (int grand_ancestor : ans[parent]) {
                    ancestors.insert(grand_ancestor);
                }
            }
            ans[u] = vector<int>(ancestors.begin(), ancestors.end());
        }
        
        return ans;
    }
};
```

## 引爆最多的炸弹

### 方法1：DFS

对于图中任意两个节点，判断两者之间有无边，构建有向图g，此后，对于每一个顶点，使用dfs计算其能够到达的顶点的数量，取所有数量的最大值即可。时间复杂度： $O(n^2)$ ，其中n是顶点个数

```cpp
class Solution {
public:
    int maximumDetonation(vector<vector<int>>& bombs) {
        int n = bombs.size();
        vector<vector<int>> g(n);
        for (int i = 0; i < n; i++) {
            long long x = bombs[i][0], y = bombs[i][1], r = bombs[i][2];
            for (int j = 0; j < n; j++) {
                long long dx = x - bombs[j][0];
                long long dy = y - bombs[j][1];
                if (j != i && dx * dx + dy * dy <= r * r) {
                    g[i].push_back(j); // i 可以引爆 j
                }
            }
        }

        int ans = 0;
        vector<int> vis(n);
        auto dfs = [&](auto&& dfs, int x) -> int {
            vis[x] = true;
            int cnt = 1;
            for (int y : g[x]) {
                if (!vis[y]) {
                    cnt += dfs(dfs, y);
                }
            }
            return cnt;
        };
        for (int i = 0; i < n && ans < n; i++) {
            ranges::fill(vis, 0);
            ans = max(ans, dfs(dfs, i));
        }
        return ans;
    }
};
```

### 方法2：传递闭包

回顾传递闭包的定义：

> 在有向图 $G = (V, E)$ 中，传递闭包是一个新的图 $G^* = (V, E^*)$，其中：如果原图中存在从顶点 i 到顶点 j 的路径（长度≥1），则 E* 中包含边 (i, j)

本题中，如果可以计算出该图的传递闭包，那么选择传递闭包中出度最大的即可，所以关键在于如何找到传递闭包。

这就用到了离散学过的**Floyd-Warshall**算法，该算法的思路是

1. 枚举每一个节点k（或者称为时间点）
2. 对所有节点，考察能否在经过节点k的情况下到达另一个节点

!!! note
    Floyd算法还可以用于全源最小路的计算，本质上传递闭包是最小路的一个特例，算法正确性证明可见：[proof](https://zhuanlan.zhihu.com/p/593219173)

最终代码：

```cpp
class Solution {
public:
    int maximumDetonation(vector<vector<int>>& bombs) {
        int n = bombs.size();
        vector<bitset<100>> f(n);
        for (int i = 0; i < n; i++) {
            long long x = bombs[i][0], y = bombs[i][1], r = bombs[i][2];
            for (int j = 0; j < n; j++) {
                long long dx = x - bombs[j][0];
                long long dy = y - bombs[j][1];
                if (dx * dx + dy * dy <= r * r) {
                    f[i].set(j); // i 可以到达 j
                }
            }
        }

        for (int k = 0; k < n; k++) {
            for (auto& fi : f) {
                if (fi[k]) { // i 可以到达 k
                    fi |= f[k]; // i 也可以到 k 可以到达的点
                }
            }
        }

        size_t ans = 0;
        for (auto& s : f) {
            ans = max(ans, s.count()); // 集合大小的最大值
        }
        return ans;
    }
};
```

## 拆分合并数组

本质是bfs状态转移，可以通过枚举长度、截取位置、插入位置找到下一个状态

```cpp
class Solution {
public:
    int minSplitMerge(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        if (nums1 == nums2) return 0;
        
        set<vector<int>> vis;
        queue<vector<int>> q;
        
        q.push(nums1);
        vis.insert(nums1);
        
        int steps = 0;
        while (!q.empty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                auto a = q.front();
                q.pop();
                
                if (a == nums2) {
                    return steps;
                }
                
                // 生成所有可能的操作
                for (int l = 0; l < n; l++) {
                    for (int r = l + 1; r <= n; r++) {
                        auto b = a;
                        vector<int> sub(b.begin() + l, b.begin() + r);
                        b.erase(b.begin() + l, b.begin() + r);
                        
                        for (int i = 0; i <= b.size(); i++) {
                            auto c = b;
                            c.insert(c.begin() + i, sub.begin(), sub.end());
                            
                            if (vis.find(c) == vis.end()) {
                                vis.insert(c);
                                q.push(c);
                            }
                        }
                    }
                }
            }
            steps++;
        }
        
        return -1; // 理论上应该能找到
    }
};
```

## 找到最终的安全状态

### 方法1：三色法判断有向图的环

如果一个节点在一个环或者在一条包含环的路上，那么该节点是不安全的。或者说，在dfs的遍历到某一个节点时，若从该节点继续向后dfs的过程中出现了回边，那么该节点是不安全的。检测回边可以用课上讲过的三色法，即：未探索的点为白色0，正在探索的点为灰色1，已经探索完毕的为黑色2，如果出现了1->1这样的情况，那么说明它是一条回边，此时可以退出，栈中的所有节点都是不安全的。

实现代码：

```cpp
class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>> &graph) {
        int n = graph.size();
        vector<int> color(n);

        function<bool(int)> safe = [&](int x) {
            if (color[x] > 0) {
                return color[x] == 2;
            }
            color[x] = 1;
            for (int y : graph[x]) {
                if (!safe(y)) {
                    return false;
                }
            }
            color[x] = 2;
            return true;
        };

        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            if (safe(i)) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```

### 方法2：拓扑排序

如果一个节点的子节点都是安全节点，那么这个节点也是安全的，此时该题的解决方法与第一题一致，可先进行拓扑排序。为了多样性，这里采用Kahn算法，首先对图取反，入度为0的点就是安全节点，通过不断遍历入度为0的点进行bfs,具体实现如下：

```cpp
class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>> &graph) {
        int n = graph.size();
        vector<vector<int>> rg(n);
        vector<int> inDeg(n);
        for (int x = 0; x < n; ++x) {
            for (int y : graph[x]) {
                rg[y].push_back(x);
            }
            inDeg[x] = graph[x].size();
        }

        queue<int> q;
        for (int i = 0; i < n; ++i) {
            if (inDeg[i] == 0) {
                q.push(i);
            }
        }
        while (!q.empty()) {
            int y = q.front();
            q.pop();
            for (int x : rg[y]) {
                if (--inDeg[x] == 0) {
                    q.push(x);
                }
            }
        }

        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            if (inDeg[i] == 0) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```