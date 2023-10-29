### 概念

从代码层面，我们如何将两个元素添加到同一个集合中呢。

此时有会想到：可以把他放到同一个数组里或者set 或者 map 中，这样就表述两个元素在同一个集合。

那么问题来了，对这些元素分门别类，可不止一个集合，可能是很多集合，成百上千，那么要定义这么多个数组吗？

有录友想，那可以定义一个二维数组。

但如果我们要判断两个元素是否在同一个集合里的时候 我们又能怎么办？ 只能把而二维数组都遍历一遍。

而且每当想添加一个元素到某集合的时候，依然需要把把二维数组组都遍历一遍，才知道要放在哪个集合里。

这仅仅是一个粗略的思路，如果沿着这个思路去实现代码，非常复杂，因为管理集合还需要很多逻辑。

那么我们来换一个思路来看看。

我们将三个元素A，B，C （分别是数字）放在同一个集合，其实就是将三个元素连通在一起，如何连通呢。

只需要用一个一维数组来表示，即：father[A] = B，father[B] = C 这样就表述 A 与 B 与 C连通了（有向连通图）。

### 模板

**C++类模板如下**

```cpp
class UnionFind{
private:
    vector<int> parent;
public:
    UnionFind(int n){
        parent = vector<int>(n,0);
        for(int i = 0;i<n;i++){
            parent[i] = i;
        }
    }

    int find(int x){
        if(parent[x]==x) return x;

        return parent[x] = find(parent[x]);
    }

    void merge(int x,int y){
        int fx = find(x);
        int fy = find(y);
        if(fx!=fy){
            parent[fx] = fy;
        }
    }
    bool connect(int x,int y){
        return find(x)==find(y);
    }
};
```

**C++非类模板**

```cpp
   int n = 1005; // 节点数根据题目来定
    vector<int> parent = vector<int> (n, 0); // C++里的一种数组结构

    // 并查集初始化
    void init() {
        for (int i = 0; i < n; ++i) {
            parent[i] = i;
        }
    }
    // 并查集里寻根的过程
    int find(int u) {
        return u == parent[u] ? u : parent[u] = find(parent[u]);
    }
    // 判断 u 和 v是否找到同一个根
    bool isSame(int u, int v) {
        u = find(u);
        v = find(v);
        return u == v;
    }
    // 将v->u 这条边加入并查集
    void merge(int u, int v) {
        u = find(u); // 寻找u的根
        v = find(v); // 寻找v的根
        if (u == v) return ; // 如果发现根相同，则说明在一个集合，不用两个节点相连直接返回
        parent[v] = u;
}
```



### 相关操作

#### 冗余链接

[题目链接](https://leetcode.cn/problems/redundant-connection/description/)

* 每回在合并之前判断当前边链接的两个顶点是否已经在同一个集合里面
* 如果在同一个集合里面说明，当前边是冗余边
* 如果不在则不是冗余边
* 采用第二类模板时注意数组下标越界

```cpp
class UnionFind{
private:
    vector<int> parent;
public:
    UnionFind(int n){
        parent = vector<int>(n,0);
        for(int i = 0;i<n;i++){
            parent[i] = i;
        }
    }

    int find(int x){
        if(parent[x]==x) return x;

        return parent[x] = find(parent[x]);
    }

    void merge(int x,int y){
        int fx = find(x);
        int fy = find(y);
        if(fx!=fy){
            parent[fx] = fy;
        }
    }
    bool connect(int x,int y){
        return find(x)==find(y);
    }
};
class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        UnionFind uf(n);
        for(int i = 0;i<n;i++){
            // 注意数组越界，应为vector的下标是到n-1
            if(uf.connect(edges[i][0]-1,edges[i][1]-1)) return edges[i];
            else uf.merge(edges[i][0]-1,edges[i][1]-1);
        }
        return {};
    }
};
```

#### 冗余链接Ⅱ

[题目链接](https://leetcode.cn/problems/redundant-connection-ii/description/)

可以分为三种情况，前两种情况是出现入度为2的点，如图：

![img](https://code-thinking.cdn.bcebos.com/pics/685.%E5%86%97%E4%BD%99%E8%BF%9E%E6%8E%A5II1.png)

且只有一个节点入度为2，为什么不看出度呢，出度没有意义，一棵树中随便一个父节点就有多个出度。

第三种情况是没有入度为2的点，那么图中一定出现了有向环（**注意这里强调是有向环！**）

如图：

![img](https://code-thinking.cdn.bcebos.com/pics/685.%E5%86%97%E4%BD%99%E8%BF%9E%E6%8E%A5II2.png)

```cpp
class UnionFind{
private:
    vector<int> parent;
public:
    UnionFind(int n){
        parent = vector<int>(n+1);
        for(int i = 1;i<=n;i++){
            parent[i] = i;
        }
    }

    int find(int x){
        if(x==parent[x]) return x;
        return parent[x] = find(parent[x]);
    }
    void merge(int x,int y){
        int fx = find(x);
        int fy = find(y);
        if(fx!=fy){
            parent[fx] = fy;
        }
    }
    // 用于判断两者是否属于同一个连通分量
    bool isSame(int x,int y){
        return find(x)==find(y);
    }
};
    vector<int> getMoveEdge(const vector<vector<int>>& edges){
        UnionFind uf(edges.size());
        for(int i = 0;i<edges.size();i++){
            if (uf.isSame(edges[i][0], edges[i][1])) { // 构成有向环了，就是要删除的边
                return edges[i];
            }
            uf.merge(edges[i][0], edges[i][1]);
        }
        return {};
    }
    // 删一条边之后判断是不是树
    bool isTreeAfterRemoveEdge(const vector<vector<int>>& edges, int deleteEdge) {
        UnionFind uf(edges.size()); // 初始化并查集
        for (int i = 0; i < edges.size(); i++) {
            if (i == deleteEdge) continue;
            if (uf.isSame(edges[i][0], edges[i][1])) { // 构成有向环了，一定不是树
                return false;
            }
            uf.merge(edges[i][0], edges[i][1]);
        }
        return true;
    }
class Solution {
public:
    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        
        int n = edges.size(); // 边的数量
        // int inDegree[n+1] = {0}; // 记录节点入度
        vector<int> inDegree = vector<int>(n+1,0);
        for (int i = 0; i < n; i++) {
            inDegree[edges[i][1]]++; // 统计入度
        }
        vector<int> vec; // 记录入度为2的边（如果有的话就两条边）
        // 找入度为2的节点所对应的边，注意要倒序，因为优先返回最后出现在二维数组中的答案
        for (int i = n - 1; i >= 0; i--) {
            if (inDegree[edges[i][1]] == 2) {
                vec.push_back(i);	// 记录edges中边的下标
            }
        }
        // 处理图中情况1 和 情况2
        // 如果有入度为2的节点，那么一定是两条边里删一个，看删哪个可以构成树
        if (vec.size() > 0) {
            if (isTreeAfterRemoveEdge(edges, vec[0])) {
                return edges[vec[0]];
            } else {
                return edges[vec[1]];
            }
        }
        // 处理图中情况3
        // 明确没有入度为2的情况，那么一定有有向环，找到构成环的边返回就可以了
        return getMoveEdge(edges);
    }
};
```