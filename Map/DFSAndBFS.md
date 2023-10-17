## DFSAndBFS

### 概念总结

#### 代码框架

**DFS**

```cpp
void dfs(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本节点所连接的其他节点) {
        处理节点;
        dfs(图，选择的节点); // 递归
        回溯，撤销处理结果
    }
}

```

**BFS**

```cpp
```



### 相关题目

#### 飞地的数量

[题目链接]()

**思路**

* 采用深度优先或者广度优先搜索策略
* 先从边沿搜索，将边沿搜索到的岛屿全部置为0，剩下的岛屿都是飞地
* 再搜索一次，返回搜索到的count值

**代码**

**BFS**

```cpp
class Solution {
private:
    int dir[4][2] = {-1, 0, 0, -1, 1, 0, 0, 1}; // 保存四个方向
    int count; // 统计符合题目要求的陆地空格数量
    void dfs(vector<vector<int>>& grid, int x, int y) {
        queue<pair<int,int>> que;
        que.push({x,y});
        grid[x][y]=0;
        count++;
        while(!que.empty()){
            pair<int,int> cur = que.front();que.pop();
            int curx = cur.first;
            int cury = cur.second;
            for(int i = 0;i<4;i++){
                int nextx = curx+dir[i][0];
                int nexty = cury+dir[i][1];
                if(nextx<0||nextx>=grid.size()||nexty<0||nexty>=grid[0].size()) continue;

                if(grid[nextx][nexty]==1){
                    que.push({nextx,nexty});
                    count++;
                    grid[nextx][nexty]=0;
                }
            }
        }
    }

public:
    int numEnclaves(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        // 从左侧边，和右侧边 向中间遍历
        for (int i = 0; i < n; i++) {
            if (grid[i][0] == 1) dfs(grid, i, 0);
            if (grid[i][m - 1] == 1) dfs(grid, i, m - 1);
        }
        // 从上边和下边 向中间遍历
        for (int j = 0; j < m; j++) {
            if (grid[0][j] == 1) dfs(grid, 0, j);
            if (grid[n - 1][j] == 1) dfs(grid, n - 1, j);
        }
        count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) dfs(grid, i, j);
            }
        }
        return count;
    }
};
```

**DFS**

```cpp
class Solution {
private:
    int dir[4][2] = {-1, 0, 0, -1, 1, 0, 0, 1}; // 保存四个方向
    int count; // 统计符合题目要求的陆地空格数量
    void dfs(vector<vector<int>>& grid, int x, int y) {
        grid[x][y] = 0;
        count++;
        for (int i = 0; i < 4; i++) { // 向四个方向遍历
            int nextx = x + dir[i][0];
            int nexty = y + dir[i][1];
            // 超过边界
            if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;
            // 不符合条件，不继续遍历
            if (grid[nextx][nexty] == 0) continue;

            dfs (grid, nextx, nexty);
        }
        return;

    }

public:
    int numEnclaves(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        // 从左侧边，和右侧边 向中间遍历
        for (int i = 0; i < n; i++) {
            if (grid[i][0] == 1) dfs(grid, i, 0);
            if (grid[i][m - 1] == 1) dfs(grid, i, m - 1);
        }
        // 从上边和下边 向中间遍历
        for (int j = 0; j < m; j++) {
            if (grid[0][j] == 1) dfs(grid, 0, j);
            if (grid[n - 1][j] == 1) dfs(grid, n - 1, j);
        }
        count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) dfs(grid, i, j);
            }
        }
        return count;
    }
};

```

#### [所有可能的路径](https://leetcode.cn/problems/all-paths-from-source-to-target/description/)

**DFS**

* 利用result记录可能的路径集合，path记录当前路径
* 采用DFS+回溯进行遍历

```cpp
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    void dfs(vector<vector<int>>& graph,int x){
        if(x==graph.size()-1){
            result.push_back(path);
            return;
        }
        for(int i = 0;i<graph[x].size();i++){   // 遍历当前节点的邻接点
            path.push_back(graph[x][i]);    // 遍历到的节点加入到路径中来
            dfs(graph,graph[x][i]);
            path.pop_back();    // 回溯，撤销本节点
        }
    }
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        path.push_back(0);
        dfs(graph,0);
        return result;
    }
};
```

**BFS**

```C++
class Solution {
public:
    vector<vector<int>> rse;
    vector<int> path;
    queue<vector<int>> que;
    void bfs(vector<vector<int>>& graph,int x){
        while(!que.empty()){
            path = que.front();
            if(!graph[path.back()].empty()){
                for(auto &y : graph[path.back()]){
                    path = que.front();
                    path.emplace_back(y);
                    que.push(path);
                }
                que.pop();
            }
            if(que.front().back() == graph.size()-1){
                rse.emplace_back(que.front());
                que.pop();
            }
            else if(graph[que.front().back()].empty()){
                que.pop();
            }
        }
    }
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        path.emplace_back(0);
        que.push(path);
        bfs(graph, 0);
        return rse;
    }
};
```





