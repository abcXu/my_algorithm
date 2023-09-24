## 栈与队列概念

* 队列是先进先出
* 栈是先进后出

如图所示：

![栈与队列理论1](https://code-thinking-1253855093.file.myqcloud.com/pics/20210104235346563.png)

C++标准库是有多个版本的，主要有以下三个最为普遍的STL版本：

1. HP STL 其他版本的C++ STL，一般是以HP STL为蓝本实现出来的，HP STL是C++ STL的第一个实现版本，而且开放源代码。
2. `P.J.Plauger `STL 由`P.J.Plauger`参照HP STL实现出来的，被Visual C++编译器所采用，不是开源的。
3. SGI STL 由Silicon Graphics Computer Systems公司参照HP STL实现，被Linux的C++编译器GCC所采用，SGI STL是开源软件，源码可读性甚高。

**栈**

![栈与队列理论2](https://code-thinking-1253855093.file.myqcloud.com/pics/20210104235434905.png)

* 栈提供push 和 pop 等等接口，所有元素必须符合先进后出规则，所以栈不提供走访功能，也不提供迭代器(iterator)。 不像是set 或者map 提供迭代器iterator来遍历所有元素。
* **栈是以底层容器完成其所有的工作，对外提供统一的接口，底层容器是可插拔的（也就是说我们可以控制使用哪种容器来实现栈的功能）。**
* **我们常用的SGI STL，如果没有指定底层实现的话，默认是以deque为缺省情况下栈的底层结构。**

```cpp
std::stack<int, std::vector<int> > third;  // 使用vector为底层容器的栈
```

```cpp
std::queue<int, std::list<int>> third; // 定义以list为底层容器的队列
```

## 栈与队列的相关操作

### 栈

#### 1、用栈实现队列

![232.用栈实现队列版本2](https://code-thinking.cdn.bcebos.com/gifs/232.%E7%94%A8%E6%A0%88%E5%AE%9E%E7%8E%B0%E9%98%9F%E5%88%97%E7%89%88%E6%9C%AC2.gif)

```cpp
class MyQueue {
public:
    stack<int> stIn;
    stack<int> stOut;
    MyQueue() {

    }
    
    void push(int x) {
        stIn.push(x);
    }
    
    int pop() {
        if(stOut.empty()){
            // 只有当stOut为空的时候，再从stIn里导入数据（导入stIn全部数据）
            while(!stIn.empty()){
                stOut.push(stIn.top());
                stIn.pop();
            }      
            
        }
        int result = stOut.top();
        stOut.pop();
        return result;
    }
    
    int peek() {
        int res = this->pop(); // 直接使用已有的pop函数
        stOut.push(res); // 因为pop函数弹出了元素res，所以再添加回去
        return res;
    }
    
    bool empty() {
         return stIn.empty() && stOut.empty();
    }
};
```

#### 2、用队列实现栈

![225.用队列实现栈](https://code-thinking.cdn.bcebos.com/gifs/225.%E7%94%A8%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E6%A0%88.gif)

```cpp
class MyStack {
public:
    queue<int> que1;
    queue<int> que2;	// 用于暂存que1（其实并不需要）
    MyStack() {

    }
    
    void push(int x) {
        que1.push(x);
    }
    
    int pop() {
        int size = que1.size();
        size--;
        while(size--){
            que2.push(que1.front());
            que1.pop();
        }
        int result = que1.front();
        que1 = que2;
        while(!que2.empty()){
            que2.pop();
        }
        return result;
    }
    
    int top() {
        return que1.back();
    }
    
    bool empty() {
        return que1.empty();
    }
};
```

**简化版**

```cpp
class MyStack {
public:
    queue<int> que;
    /** Initialize your data structure here. */
    MyStack() {

    }
    /** Push element x onto stack. */
    void push(int x) {
        que.push(x);
    }
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int size = que.size();
        size--;
        while (size--) { // 将队列头部的元素（除了最后一个元素外） 重新添加到队列尾部
            que.push(que.front());
            que.pop();
        }
        int result = que.front(); // 此时弹出的元素顺序就是栈的顺序了
        que.pop();
        return result;
    }

    /** Get the top element. */
    int top() {
        return que.back();
    }

    /** Returns whether the stack is empty. */
    bool empty() {
        return que.empty();
    }
};
```



#### 3、无法吃午餐的学生数量

[题目链接](https://leetcode.cn/problems/number-of-students-unable-to-eat-lunch/description/)

* 推出循环时一定有喜欢吃某种三明治的人都吃到了

```cpp
class Solution {
public:
    int countStudents(vector<int>& students, vector<int>& sandwiches) {
        int s1 = accumulate(students.begin(),students.end(),0); // 圆形人数
        int s0 = students.size()-s1;    // 方形人数
        for(int i = 0;i<sandwiches.size();i++){
            if(sandwiches[i]==0&&s0>0){
                s0--;
            }else if(sandwiches[i]==1&&s1>0){
                s1--;
            }else{
                break;
            }
        }
        return s0+s1;
    }
};
```

**时间复杂度:$O(n)$**

#### 4、 删除字符串中的所有相邻重复项

[题目链接](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/)

**和一种卡牌游戏类似（找乌龟）**

<img src="https://code-thinking.cdn.bcebos.com/gifs/1047.%E5%88%A0%E9%99%A4%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%AD%E7%9A%84%E6%89%80%E6%9C%89%E7%9B%B8%E9%82%BB%E9%87%8D%E5%A4%8D%E9%A1%B9.gif" alt="1047.删除字符串中的所有相邻重复项" style="zoom:80%;" />

```cpp
class Solution {
public:
    string removeDuplicates(string S) {
        stack<char> st;	
        for (char s : S) {
            if (st.empty() || s != st.top()) {
                st.push(s);
            } else {
                st.pop(); // s 与 st.top()相等的情况
            }
        }
        string result = "";
        while (!st.empty()) { // 将栈中元素放到result字符串汇总
            result += st.top();
            st.pop();
        }
        reverse (result.begin(), result.end()); // 此时字符串需要反转一下
        // 实际上栈中存储的就是最终结果
        return result;

    }
};
```

**时间复杂度O(n)**

**直接用string做栈**

```cpp
class Solution {
public:
    string removeDuplicates(string S) {
        string result;	//当栈使用
        for(char s : S) {
            if(result.empty() || result.back() != s) {
                result.push_back(s);
            }
            else {
                result.pop_back();
            }
        }
        return result;
    }
};
```

#### 5、车队

**思路**

* 求出每个位置的车子到达终点需要的时间，并建立一个位置到时间的map，注意时间是float
* 按照位置进行升序排序
* 利用单调栈思路进行求解
* 两量行驶在单行道上的车是否相遇关键是看，距离远的车是否用时更短
* 最后留在栈里的车子数量就是车队数目，因为他们的右边（position大）但是用时快

```cpp
class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        // 距离目的地远用时短的和近的用时长的会形成车队
        int n = position.size();
        float time[n];
        unordered_map<int,float> mp;
        for(int i = 0;i<n;i++){
            time[i] = float(target-position[i])/speed[i];    // 计算到达目的地需要的时间,注意转一下float
            mp[position[i]] = time[i];  // 形成位置和时间的映射
        }
        sort(position.begin(),position.end());  // 按照距离排序
        stack<float> stk;
        for(int i = 0;i<n;i++){     // 单调递增栈，比较的是时间
            while(!stk.empty()&&mp[position[i]]>=stk.top()){
                stk.pop();
            }
            stk.push(mp[position[i]]);
        }

        return stk.size();
    }
};
```

**时间复杂度$O(n)$

#### 6、柱状图中最大面积

**思路**

* 理想情况下：高度一直增大的三角块，这样的情况下，假设有n个柱子，最后一个柱子起倒推计算n次面积，取最大值

* 每个面积都是以最后一个柱子为终点，如图返回面积的最大值

* 非理想情况下：如果当前小于栈顶，则将之前的三角块的面积计算

  <img src="C:\Users\xu5375\AppData\Roaming\Typora\typora-user-images\image-20230924200825258.png" alt="image-20230924200825258" style="zoom:68%;" />

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int res = 0;
        stack<int> st;
        heights.insert(heights.begin(),0);  // 使第一个三角块的面积能够被计算
        heights.push_back(0);   // 使最后一个三角块能被计算面积
        st.push(0);
        for(int i = 1;i<heights.size();i++){
            if(heights[i]>heights[st.top()]){
                st.push(i);
            }else{
                while(!st.empty()&&heights[i]<heights[st.top()]){
                    int mid = st.top();
                    st.pop();
                    if(!st.empty()){
                        int l = st.top();
                        int r = i;
                        int w = r-l-1;
                        int h = heights[mid];
                        res = max(res,w*h);
                    }
                }
            }
            st.push(i);
        }
        return res;
    }
};
```



### 队列

