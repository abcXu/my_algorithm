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

#### 岛屿周长

[题目链接](https://leetcode.cn/problems/island-perimeter/)

**非DFS算法**

* 遍历每一个节点，当遍历到岛屿时，向四个方向判断边界
* 如果是边界则周长+1
* ![463.岛屿的周长.png](https://pic.leetcode-cn.com/1604025390-NccJlv-463.%E5%B2%9B%E5%B1%BF%E7%9A%84%E5%91%A8%E9%95%BF.png)

```cpp
class Solution {
public:
    int direction[4][2] = {0,1,1,0,-1,0,0,-1};

    int islandPerimeter(vector<vector<int>>& grid) {
        int result = 0;
        for(int i = 0;i<grid.size();i++){
            for(int j = 0;j<grid[0].size();j++){
                if(grid[i][j]==1){
                    for(int k = 0;k<4;k++){
                        int x = i+direction[k][0];
                        int y = j+direction[k][1];
                        if(x<0||x>=grid.size()||y<0||y>=grid[0].size()||grid[x][y]==0){
                            result++;
                        }
                    }
                }
            }
        }
        return result;
    }
};
```

* result = 岛屿数量 * 4 - cover * 2;

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        int sum = 0;    // 陆地数量
        int cover = 0;  // 相邻数量
        for(int i = 0;i<grid.size();i++){
            for(int j = 0;j<grid[0].size();j++){
                if(grid[i][j]==1){
                    sum++;
                
                // 统计上边相邻的陆地
                if(i-1>=0&&grid[i-1][j]==1) cover++;
                // 统计左边相邻
                if(j-1>=0&&grid[i][j-1]==1) cover++;
                // 不需要再统计右边和下边相邻，否则将重复
                }
            }
        }
        return sum*4-cover*2;
    }
};
```

#### 岛屿的数目

**并查集**

```cpp
class Solution {
public:
    //并查集
    vector<int> parent;
    int cnt; //集合元素总数

    int find(int x){ //找元素的祖宗
        if (x == parent[x]) return x;

        parent[x] = find(parent[x]);
        return parent[x];
    }

    void merge(int x, int y){ //合并集合
        x = find(x);
        y = find(y);
        if (x == y) return ;
        parent[x] = y;
        cnt--; //合并一次集合总数减一
    }

    int numIslands(vector<vector<char>>& grid) {
        int i, j, m = grid.size(), n = grid[0].size();
        cnt = m * n;
        parent.resize(cnt);

        for (i = 0; i < cnt; i++) parent[i] = i;
        for (i = 0; i < m; i++){
            for (j = 0; j < n; j++){
                if (grid[i][j] == '1'){ // 陆地
                    //二维坐标转一维坐标
                    if (i > 0 && grid[i - 1][j] == '1') merge(i * n + j, (i - 1) * n + j);
                    if (j > 0 && grid[i][j - 1] == '1') merge(i * n + j, i * n + j - 1);
                } 
                else cnt--; //之前初始化集合总数还包括了海洋,要将海洋从集合中剔除
            }
        }

        return cnt;
    }
};

```

**DFS**

* DFS 搜索的次数就是岛屿的数量

```cpp
class Solution {
public:
    int direction[4][2] = {0,1,1,0,-1,0,0,-1};
    void dfs(vector<vector<char>>& grid,vector<vector<bool>>& vis,int x,int y){
        if(vis[x][y]||grid[x][y]=='0') return;
        vis[x][y]=true;
        for(int i = 0;i<4;i++){
            int nextx = x+direction[i][0];
            int nexty = y+direction[i][1];
            if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;  // 越界了，直接跳过
            dfs(grid, vis, nextx, nexty);
        }

    }
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size(),m = grid[0].size();
        int result = 0;
        vector<vector<bool>> vis = vector<vector<bool>>(n, vector<bool>(m, false));
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(!vis[i][j]&&grid[i][j]=='1'){// 当前位置没有访问过，并且为陆地
                    dfs(grid,vis,i,j);
                    result++;
                }
            }
        }
        return result;
    }
};
```

**BFS**

* 坑：**只要 加入队列就代表 走过，就需要标记，而不是从队列拿出来的时候再去标记走过**。

```cpp
class Solution {
public:
    int direction[4][2] = {0,1,1,0,-1,0,0,-1};
    void bfs(vector<vector<char>>& grid,vector<vector<bool>>& vis,int x,int y){
        queue<pair<int,int>> que;
        que.push({x,y}); 
        vis[x][y] = true;
        while(!que.empty()){
            pair<int,int> cur = que.front();
            que.pop();
            int curx = cur.first;
            int cury = cur.second;
           for(int i = 0;i<4;i++){
                int nextx = curx + direction[i][0];
                int nexty = cury + direction[i][1];
                if(nextx<0||nextx>=grid.size()||nexty<0||nexty>=grid[0].size()) continue;
                if(!vis[nextx][nexty]&&grid[nextx][nexty]=='1'){
                    que.push({nextx,nexty});
                    vis[nextx][nexty] = true; // 只要加入队列立刻标记
                }
           }
            
        }

    }
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size(),m = grid[0].size();
        int result = 0;
        vector<vector<bool>> vis = vector<vector<bool>>(n, vector<bool>(m, false));
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(!vis[i][j]&&grid[i][j]=='1'){// 当前位置没有访问过，并且为陆地
                    bfs(grid,vis,i,j);
                    result++;
                }
            }
        }
        return result;
    }
};
```

#### 岛的最大面积

[题目链接](https://leetcode.cn/problems/max-area-of-island/description/)

**DFS**

* 在上一题的基础上增加一个变量，area用于记录当前岛屿面积
* 比较当前面积和历史最大面积，当前面积更大则更新岛屿面积

```cpp
class Solution {
public:
    int direction[4][2] = {0,1,1,0,-1,0,0,-1};
    void dfs(vector<vector<int>>& grid,vector<vector<bool>>& vis,int x,int y,int &area){
        if(vis[x][y]||grid[x][y]==0) return;
        vis[x][y]=true;
        area++;
        for(int i = 0;i<4;i++){
            int nextx = x+direction[i][0];
            int nexty = y+direction[i][1];
            if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;  // 越界了，直接跳过
            dfs(grid, vis, nextx, nexty,area);
        }

    }
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int n = grid.size(),m = grid[0].size();
        int maxArea = 0,curArea=0;
        vector<vector<bool>> vis = vector<vector<bool>>(n, vector<bool>(m, false));
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(!vis[i][j]&&grid[i][j]== 1){// 当前位置没有访问过，并且为陆地
                    dfs(grid,vis,i,j,curArea);
                    maxArea = max(maxArea,curArea);
                    curArea = 0;	// 记得将面积重置
                }
            }
        }
        return maxArea;
    }
};
```

**BFS**

```cpp
class Solution {
public:
    int direction[4][2] = {0,1,1,0,-1,0,0,-1};
    void bfs(vector<vector<int>>& grid,vector<vector<bool>>& vis,int x,int y,int &area){
        queue<pair<int,int>> que;
        que.push({x,y});
        area++;
        vis[x][y] = true;
        while(!que.empty()){
            pair<int,int> cur = que.front();
            que.pop();
            int curx = cur.first,cury = cur.second;

            for(int i = 0;i<4;i++){
                int nextx = curx+direction[i][0];
                int nexty = cury+ direction[i][1];
                if(nextx<0||nextx>=grid.size()||nexty<0||nexty>=grid[0].size()) continue;

                if(!vis[nextx][nexty]&&grid[nextx][nexty]==1)
                {
                    que.push({nextx,nexty});
                    area++;
                    vis[nextx][nexty] = true;
                }
            }
        }
    }
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int n = grid.size(),m = grid[0].size();
        int maxArea = 0,curArea=0;
        vector<vector<bool>> vis = vector<vector<bool>>(n, vector<bool>(m, false));
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(!vis[i][j]&&grid[i][j]== 1){// 当前位置没有访问过，并且为陆地
                    bfs(grid,vis,i,j,curArea);
                    maxArea = max(maxArea,curArea);
                    curArea = 0;
                }
            }
        }
        return maxArea;
    }
};
```

**并查集**

```cpp
class UnionFind{
public:
    vector<int> parent;

    UnionFind(int n){
        parent = vector<int> (n);
        for(int i = 0; i < n; i++){
            parent[i] = i;
        }
    }

    int find(int x){
        if(x == parent[x]){
            return x;
        }
        return parent[x] = find(parent[x]);
    }

    void merge(int x, int y){
        int fx = find(x);
        int fy = find(y);
        if(fx != fy)
            parent[fx] = fy;
    }
};

class Solution {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        UnionFind uf = UnionFind(m * n);

        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if (grid[i][j] == 1){ // 陆地
                    // 二维坐标转一维坐标
                    if (i > 0 && grid[i - 1][j] == 1) uf.merge(i * n + j, (i - 1) * n + j);
                    if (j > 0 && grid[i][j - 1] == 1) uf.merge(i * n + j, i * n + j - 1);
                } 
            }
        }

        unordered_map<int, int> mp;  // 哈希表用于存储连通分量的大小
        int maxArea = 0;  // 最大连通分量的大小
        int maxele = 0;  // 最大连通分量的元素

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int root = uf.find(i * n + j);
                    mp[root]++;  // 增加连通分量的大小

                    if (mp[root] > maxArea) {
                        maxArea = mp[root];
                        maxele = root;
                    }
                }
            }
        }

        return maxArea;
    }
};

```

