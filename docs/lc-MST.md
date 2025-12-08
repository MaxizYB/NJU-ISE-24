# 最小生成树与图的应用

## 第一题

1. 建图 + 统计每个节点的度数

2. 把所有度为 1 的节点加入队列（叶子）

3. 反复 BFS 层序去掉叶子

4. 最后剩下的节点（1 个或 2 个）就是答案

```cpp
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        if (n == 1) return {0};  // 特例：只有一个节点

        vector<vector<int>> graph(n);
        vector<int> degree(n, 0);

        // 建图 + 度数
        for (auto& e : edges) {
            int a = e[0], b = e[1];
            graph[a].push_back(b);
            graph[b].push_back(a);
            degree[a]++;
            degree[b]++;
        }

        // 初始叶子（度为 1）
        queue<int> q;
        for (int i = 0; i < n; i++) {
            if (degree[i] == 1) q.push(i);
        }

        vector<int> res;

        // 拓扑排序剥叶子
        while (!q.empty()) {
            int sz = q.size();
            res.clear();  // 本轮剩下的节点，有可能是最终答案

            for (int i = 0; i < sz; i++) {
                int leaf = q.front(); q.pop();
                res.push_back(leaf);

                for (int nei : graph[leaf]) {
                    degree[nei]--;
                    if (degree[nei] == 1) {
                        q.push(nei);
                    }
                }
            }
        }

        return res;  // 剩下的即是树的中心（1 或 2 个）
    }
};
```

## 第二题

本题是之前作业的一个拓展，具体讨论和优化可见[proof](https://leetcode.cn/problems/min-cost-to-connect-all-points/solutions/565801/lian-jie-suo-you-dian-de-zui-xiao-fei-yo-kcx7/)

## 第三题

```cpp
class Solution {
public:
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        // 建图（无向图）
        vector<vector<int>> g(n);
        for (auto &e : connections) {
            int a = e[0], b = e[1];
            g[a].push_back(b);
            g[b].push_back(a);
        }

        // Tarjan 需要的数组
        vector<int> dfn(n, 0);   // 时间戳
        vector<int> low(n, 0);   // 能回到的最早时间戳
        int timer = 0;
        vector<vector<int>> ans; // 存储所有桥

        // Tarjan DFS
        function<void(int, int)> tarjan = [&](int u, int parent) {
            dfn[u] = low[u] = ++timer;

            for (int v : g[u]) {
                if (v == parent) continue;  // 不能走回父节点

                if (!dfn[v]) {  // v 未访问，是树边
```