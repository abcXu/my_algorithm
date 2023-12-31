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

#### 最大人工岛

[题目链接](https://leetcode.cn/problems/making-a-large-island/)

* 先对区域进行dfs，将每个岛屿岛上标记
* 再对区域进行一次搜索，如果当前位置是海洋，则统计其四方的岛屿
* 将四方的岛屿进行相加，最后返回最大值

```cpp
class Solution {
public:
    int dir[4][2] = {0,1,1,0,-1,0,0,-1};
    int count;
    void dfs(vector<vector<int>>& grid,vector<vector<bool>>& vis,int x,int y,int mark){
        if(vis[x][y]||grid[x][y]==0) return;
        vis[x][y] = true;
        grid[x][y] = mark;  // 将当前格子打上标记
        count++;    // 当前岛屿面积加一
        for(int i = 0;i<4;i++){
            int nextx = x+dir[i][0];
            int nexty = y+dir[i][1];
            if(nextx<0||nextx>=grid.size()||nexty<0||nexty>=grid[0].size()) continue;
            dfs(grid,vis,nextx,nexty,mark);
        }
    }
    int largestIsland(vector<vector<int>>& grid) {
        int n = grid.size(),m = grid[0].size(); // 记录行数和列数
        vector<vector<bool>> vis = vector<vector<bool>>(n,vector<bool>(m,false)); // 初始化vis数组
        unordered_map<int ,int> gridNum;    // 用于存储岛屿对应的面积
        int mark = 2; // 记录每个岛屿的编号
        bool isAllGrid = true; // 标记是否整个地图都是陆地
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                if(grid[i][j]==0) isAllGrid=false;  // 出现了陆地，说明不是纯陆地
                if(!vis[i][j]&&grid[i][j]==1){  // 当前格子没有访问并且是陆地
                    count = 0;  // 用于统计当前岛屿的面积
                    dfs(grid,vis,i,j,mark); // 进入dfs
                    gridNum[mark] = count;  // 记下当前岛屿对应的面积
                    mark++; // 编号++，用于记录下一块岛屿
                }
            }
        }

        if(isAllGrid) return m*n;   
        // 以下逻辑是根据添加陆地的位置，计算周边岛屿面积之和
        int result = 0; // 记录最后结果
        unordered_set<int> visitedGrid; // 标记访问过的岛屿
        for(int i = 0;i<n;i++){
            for(int j = 0;j<m;j++){
                int tmpCount = 1; // 记录连接之后的岛屿数量
                visitedGrid.clear(); // 每次使用时，清空
                if (grid[i][j] == 0) {
                    for (int k = 0; k < 4; k++) {
                        int neari = i + dir[k][1]; // 计算相邻坐标
                        int nearj = j + dir[k][0];
                        if (neari < 0 || neari >= grid.size() || nearj < 0 || nearj >= grid[0].size()) continue;
                        if (visitedGrid.count(grid[neari][nearj])) continue; // 添加过的岛屿不要重复添加
                        // 把相邻四面的岛屿数量加起来
                        tmpCount += gridNum[grid[neari][nearj]];
                        visitedGrid.insert(grid[neari][nearj]); // 标记该岛屿已经添加过
                    }
                }
                result = max(result, tmpCount);
            }
        }
        return result;
    }
};
```

**时间复杂度$O(n^2)$**

**并查集**

```cpp
class UnionFind {
private:
    vector<int> parent;
    vector<int> size;   // 用于记录当前岛的大小

public:
    UnionFind(int n) {
        parent = vector<int>(n);    // 初始化parent数组
        size = vector<int>(n, 1);   // 将size初始化为1 
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }

    int find(int x) {
        if (x == parent[x]) {
            return x;
        }
        return parent[x] = find(parent[x]); // 路径压缩 
    }

    void merge(int x, int y) {  
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            parent[rootX] = rootY;
            size[rootY] += size[rootX];
        }
    }

    int getSize(int x) {    // 获取当前岛屿面积
        return size[find(x)];
    }
};

class Solution {
public:
    int largestIsland(vector<vector<int>>& grid) {
        int n = grid.size();
        UnionFind uf(n * n);

        vector<int> dx = {1, -1, 0, 0};
        vector<int> dy = {0, 0, 1, -1};

        // 遍历网格，建立并查集
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int current = i * n + j;    // 将二位数组索引转换为一维数组
                    for (int k = 0; k < 4; k++) {
                        int ni = i + dx[k];
                        int nj = j + dy[k];
                        if (ni >= 0 && ni < n && nj >= 0 && nj < n && grid[ni][nj] == 1) {
                            int neighbor = ni * n + nj;
                            uf.merge(current, neighbor);
                        }
                    }
                }
            }
        }

        int maxIslandSize = 0;  // 用于记录最大的人造岛

        // 遍历网格，查找最大的岛屿
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {  // 当前为海域
                    int current = i * n + j;
                    unordered_map<int, int> neighbors;  
                    for (int k = 0; k < 4; k++) {
                        int ni = i + dx[k];
                        int nj = j + dy[k];
                        if (ni >= 0 && ni < n && nj >= 0 && nj < n && grid[ni][nj] == 1) {
                            int neighbor = uf.find(ni * n + nj);    // 查找邻居的祖先
                            neighbors[neighbor] = uf.getSize(neighbor); // 获取邻居的面积
                        }
                    }
                    int newSize = 1;
                    for (const auto& p : neighbors) {
                        newSize += p.second; // 统计人造岛的总面积
                    }
                    maxIslandSize = max(maxIslandSize, newSize);    // 更新
                }
            }
        }

        if (maxIslandSize == 0) {
            // 如果没有0存在，返回整个网格的大小
            maxIslandSize = n * n;
        }

        return maxIslandSize;
    }
};

```

#### 被围绕的区域

[题目链接](https://leetcode.cn/problems/surrounded-regions/)

* 在边沿出发，将所有的'O'替换成'A'
* 将'O'换成X，'A'换成'O'
* BFS 和DFS均可

```cpp
class Solution {
public:
    int dir[4][2] = {1,0,0,1,-1,0,0,-1};
    void bfs(vector<vector<char>>& board,int x,int y){
        queue<pair<int,int>> que;
        que.push({x,y});
        board[x][y] = 'A';
        while(!que.empty()){
            pair<int,int> cur = que.front();
            que.pop();
            int curx = cur.first;
            int cury = cur.second;
            for(int i = 0;i<4;i++){
                int nextx = curx + dir[i][0];
                int nexty = cury +  dir[i][1];
                if(nextx<0||nextx>=board.size()||nexty<0||nexty>=board[0].size()) continue;
                if(board[nextx][nexty]=='A'||board[nextx][nexty]=='X') continue;
                que.push({nextx,nexty});
                board[nextx][nexty] = 'A';
            }
        }
    }
    void dfs(vector<vector<char>>& board,int x,int y){
        board[x][y] = 'A';
        for(int i = 0;i<4;i++){
            int nextx = x+dir[i][0];
            int nexty = y+dir[i][1];

            if(nextx<0||nextx>=board.size()||nexty<0||nexty>=board[0].size()) continue;
            if(board[nextx][nexty]=='A'||board[nextx][nexty]=='X') continue;
            dfs(board,nextx,nexty);
        }

        return;
    }
    void solve(vector<vector<char>>& board) {
        int  m =   board.size();
        int  n = board[0].size();
        // 从左侧边和右侧边向中间遍历
        for(int i = 0;i<m;i++){
            if(board[i][0]=='O') bfs(board,i,0);
            if(board[i][n-1]=='O') bfs(board,i,n-1);
        }
        // 从上侧边和下侧边向中间遍历
        for(int i = 0;i<n;i++){
            if(board[0][i]=='O') bfs(board,0,i);
            if(board[m-1][i]=='O') bfs(board,m-1,i);
        }

        for(int i = 0;i<m;i++){
            for(int j = 0;j<n;j++){
                if(board[i][j]=='O') board[i][j]='X';
                if(board[i][j]=='A') board[i][j]='O';
                
            }
        }

    }
};
```

#### 太平洋大西洋水流问题

**不要从陆地出发，会超时**

**原因：**遍历每一个节点，是 m * n，遍历每一个节点的时候，都要做深搜，深搜的时间复杂度是： m * n

那么整体时间复杂度 就是 O(m^2 * n^2) ，这是一个四次方的时间复杂度。

```cpp
class Solution {
public:
    int  dir[4][2] = {0,1,1,0,-1,0,0,-1};
    void dfs(vector<vector<int>>& heights,vector<vector<bool>>&  vis,int x,int y){
        vis[x][y] = true;
        for(int i = 0;i<4;i++){
            int nextx = x+dir[i][0];
            int nexty = y+dir[i][1];
            if(nextx<0||nextx>=heights.size()||nexty<0||nexty>=heights[0].size()) continue;
            if(heights[x][y]>heights[nextx][nexty]) continue;
            if(!vis[nextx][nexty])
                dfs(heights,vis,nextx,nexty);
            
               
        }
        return;
    }
    void bfs(vector<vector<int>>& heights,vector<vector<bool>>& vis,int x,int y){
       
        queue<pair<int,int>> que;
        que.push({x,y});
         vis[x][y] = true;
        while(!que.empty()){
            pair<int,int> cur = que.front();
            que.pop();
            int curx = cur.first;
            int cury = cur.second;
            for(int k = 0;k<4;k++){
                int nextx = curx + dir[k][0];
                int nexty = cury + dir[k][1];
                if(nextx<0||nextx>=heights.size()||nexty<0||nexty>=heights[0].size()) continue;
                if(heights[curx][cury]>heights[nextx][nexty]) continue;
                if(!vis[nextx][nexty]){
                    que.push({nextx,nexty});
                    vis[nextx][nexty] = true;
                }
            }
        }
        return;
    }
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        vector<vector<int>> res;

        int m = heights.size(),n = heights[0].size();
        // 记录从太平洋边出发，可以遍历的节点
        vector<vector<bool>> pacific = vector<vector<bool>>(m, vector<bool>(n, false));

        // 记录从大西洋出发，可以遍历的节点
        vector<vector<bool>> atlantic = vector<vector<bool>>(m, vector<bool>(n, false));
        for(int i = 0;i<m;i++){
            bfs(heights,pacific,i,0);
            bfs(heights,atlantic,i,n-1);
        }
        for(int j = 0;j<n;j++){
            bfs(heights,pacific,0,j);
            bfs(heights,atlantic,m-1,j);
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 如果这个节点，从太平洋和大西洋出发都遍历过，就是结果
                if (pacific[i][j] && atlantic[i][j]) res.push_back({i, j});
            }
        }
        return res;
    }
};
```

#### 钥匙和房间

[题目地址](https://leetcode.cn/problems/keys-and-rooms/description/)

* 有向图的搜索

```cpp
class Solution {
public:
    void dfs(vector<vector<int>>& rooms,int key, vector<bool>& visiited){
        if(visiited[key]) return;

        visiited[key] = true;
        vector<int> keys = rooms[key];
        for(int key:keys){
            dfs(rooms,key,visiited);
        }
    }
    bool canVisitAllRooms(vector<vector<int>>& rooms) {
        vector<bool> visiited(rooms.size(),false);
        dfs(rooms,0,visiited);

        for(int i = 0;i<rooms.size();i++){
            if(visiited[i]==false) return false;
        }

        return true;
    }
};
```
