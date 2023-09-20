### 一、关于字符串

### 二、字符串的操作

#### 1、翻转字符串

[题目链接](https://leetcode.cn/problems/reverse-string/description/)

**双指针法**

* 定义两个整形变量分别指向串首和串尾，交换两者值，`i++,j--`

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        for(int i = 0,j = s.size()-1;i<s.size()/2;i++,j--){
            swap(s[i],s[j]);
        }
    }
};
```

**时间复杂度：** $O(n)$

#### 2、翻转字符串Ⅱ

[题目链接](https://leetcode.cn/problems/reverse-string-ii/)

* 将原字符串划分成多个长度为2k的小区间
* 利用C++库函数reverse（）对小区间的前k个值翻转，不足k个则翻转剩余

```cpp
class Solution {
public:
    string reverseStr(string s, int k) {
        int len = s.length();
        for(int i =0;i<len;i+=2*k)  // 每间隔2k取一次i
        {
            if(i+k<=len){
                // 1. 每隔 2k 个字符的前 k 个字符进行反转
                // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
                reverse(s.begin()+i,s.begin()+i+k);
            }else{
                // 剩余字符不足k个，翻转剩余字符
                reverse(s.begin()+i,s.end());
            }
        }
        return s;
    }
};
```

**自定义reverse函数**

```cpp
class Solution {
public:
    void reverse(string &s,int start,int end){
        for(int i = start,j=end;i<j;i++,j--){
            swap(s[i],s[j]);
        }
    }
    string reverseStr(string s, int k) {
        int len = s.length();
        for(int i =0;i<len;i+=2*k)  // 每间隔2k取一次i
        {
            if(i+k<=len){
                // 1. 每隔 2k 个字符的前 k 个字符进行反转
                // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
                reverse(s,i,i+k-1);
            }else{
                // 剩余字符不足k个，翻转剩余字符
                reverse(s,i,len-1);
            }
        }
        return s;
    }
};
```

**时间复杂度:**$O(n)$

#### 3、替换空格

[题目链接](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

```cpp
class Solution {
public:
    string replaceSpace(string s) {
        int count =  0;
        int oldSize = s.length();
        // 找出字符串中的空格数目
        for(int i = 0;i<oldSize;i++){
            if(s[i]==' ')
                count++;
        }
        s.resize(oldSize+2*count);  // 扩容，新容量等于旧容量+空格数*2
        int newSize = oldSize+2*count;
        for(int i = newSize-1,j = oldSize-1;j<i;i--,j--){
            if(s[j]!=' '){  
                s[i ] = s[j];
            }else{  
                s[i] = '0';
                s[i-1] = '2';
                s[i-2] = '%';
                i-=2;
            }
        }
        return s;
    }
};
```

#### 4、翻转串内单词

[题目链接](https://leetcode.cn/problems/reverse-words-in-a-string/)

* 先去除多余的空格
* 将串整体翻转一次，此时单词是倒序的
* 再将单词翻转一次，得到题述字符串

```cpp
class Solution {
public:
    void removeExtraSpaces(string &s){
        int slow = 0;
        for(int i =0;i<s.size();i++){
            if(s[i]!=' '){
                if(slow!=0) s[slow++] = ' ';
                while(i<s.size()&&s[i]!=' '){
                    s[slow++] = s[i++];
                }
            }
        }
        s.resize(slow);
    }
    void reverse(string& s, int start, int end){ //翻转，区间写法：左闭右闭 []
        for (int i = start, j = end; i < j; i++, j--) {
            swap(s[i], s[j]);
        }
    }
    string reverseWords(string s) {
        removeExtraSpaces(s);
        reverse(s,0,s.length()-1);
        int start = 0;
        for(int i = 0;i<=s.length();++i){
            if(s[i]==' '|| i==s.length())
            {
                reverse(s,start,i-1);
                start = i+1;
            }
        }
        return s;
    }
};
```

**时间复杂度** $O(n)$