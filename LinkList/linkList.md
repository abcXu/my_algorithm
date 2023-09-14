### 一、链表理论

* 链表是一种通过指针串联在一起的线性结构，每一个节点由两部分组成，一个是数据域一个是指针域（存放指向下一个节点的指针），最后一个节点的指针域指向null（空指针的意思）。

* 链表的入口节点称为链表的头结点也就是head。

  #### 1、链表的类型

  * 单链表

    一个指针域、一个值域，每一个指针域指向下一个指针，末尾指针指向NULL

    ![singleList](pic\singleList.png) 

  * 双向链表

    两个指针域，一个值域

    ![](pic\doubleList.png)

  * 循环链表

    最后一个指针指向头节点

    ![](pic\cycleList.png)

### 二、链表的操作

#### 1、移除链表元素

递归

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
       if(head==NULL) return head;
       head->next = removeElements(head->next,val);
       return head->val==val?head->next:head;
     }
};
```

**常规迭代**

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head==NULL) return NULL;
       
        // 删除头结点
        while (head != NULL && head->val == val) { // 注意这里不是if
            ListNode* tmp = head;
            head = head->next;
            delete tmp;
        }
        // 删除非头节点
        ListNode* cur = head;
        while(cur!=NULL&&cur->next!=NULL){
            if(cur->next->val==val){
                ListNode* tmp = cur->next;
                cur->next = tmp->next;
                delete tmp;
            }else{
                cur = cur->next;
            }
        }
        return head;
    }
};
```



**添加哨兵**

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head==NULL) return NULL;
       
        ListNode* dummyNode = new ListNode(0);
        dummyNode->next = head;
        // 删除非头节点
        ListNode* cur = dummyNode;
        ListNode* tmp = dummyNode;
        while(cur!=NULL&&cur->next!=NULL){
            if(cur->next->val==val){
                tmp = cur->next;
                cur->next = tmp->next;
                delete tmp;
            }else{
                cur = cur->next;
            }
        }
        return dummyNode->next;
    }
};
```



**双指针**	前后指针，前指针做判断，后指针有记录待删除节点的前一个节点

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        while(head&&head->val==val) head = head->next;
        if(head==NULL) return head;

        ListNode* front = head->next;     // 定义前指针,用于做判断
        ListNode* behind = head;  // 定义后指针，相当于front指针的前一个节点
        while(front!=NULL){
            if(front->val==val){
                behind->next = front->next;
                delete front;
                front = behind->next;
            }else{
                front = front->next;
                behind = behind->next;
            }
        }
        return head;
       
     }
};
```



#### 2、设计链表

```cpp
// 定义链表结构
struct LinkeNode{
    int val;
    LinkeNode* next;
    LinkeNode(int val):val(val),next(NULL){}
};
class MyLinkedList {

public:
    // 定义构造方法
    MyLinkedList() {
        m_dummyNode =new LinkeNode(0);
        m_size = 0;
    }
    // 获取指定索引节点的下标
    int get(int index) {
        if(index<0||index>m_size-1) return -1;

        LinkeNode* cur = m_dummyNode->next;
        while(index--) cur = cur->next;

        return cur->val;
    }
    // 在头节点前添加一个节点
    void addAtHead(int val) {
        LinkeNode* newNode = new LinkeNode(val);
        newNode->next = m_dummyNode->next;
        m_dummyNode->next = newNode;
        m_size++;
    }
    // 在链表尾部添加一个节点
    void addAtTail(int val) {
        LinkeNode* cur = m_dummyNode;
        while(cur->next){
            cur = cur->next;
        }
        LinkeNode* newNode = new LinkeNode(val);
        cur->next = newNode;
        m_size++;
    }
    // 在第index个节点之前插入一个新节点，例如index为0，那么新插入的节点为链表的新头节点。
    // 如果index 等于链表的长度，则说明是新插入的节点为链表的尾结点
    // 如果index大于链表的长度，则返回空
    // 如果index小于0，则在头部插入节点
    void addAtIndex(int index, int val) {
        if(index>m_size) return;
        if(index<0) index=0;
        LinkeNode* newNode = new LinkeNode(val);
        LinkeNode* cur = m_dummyNode;
        while(index--){
            cur = cur->next;
        }
        newNode->next = cur->next;
        cur->next = newNode;
        m_size++;
    }
     // 删除第index个节点，如果index 大于等于链表的长度，直接return，注意index是从0开始的
    void deleteAtIndex(int index) {
        if(index<0||index>m_size-1){
            return;
        }
        LinkeNode* cur = m_dummyNode;
        while(index--){
            cur = cur->next;
        }
        LinkeNode* tmp = cur->next;
        cur->next = tmp->next;
        delete tmp;
        tmp = NULL; //防止tmp成为野指针
        m_size--;


    }
private:
    int m_size; 
    LinkeNode* m_dummyNode;  // 哨兵
};
```

#### 3、翻转链表

**迭代法**

**实质：**顺序遍历原始链表，然后采用前插法构造翻转链表

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head==NULL||head->next==NULL) return head;

        ListNode* cur = head,*pre = NULL;
        while(cur)  //通过顺序遍历原始链表，然后采用前插法构造翻转链表
        {
            ListNode* temp = cur->next; // 临时指针记录当前节点的下一个节点

            cur->next = pre;    // 当前节点指向pre
            pre = cur;      // pre前移
            cur = temp;     // cur 指向原链表的下一个节点
        }

    return pre;
    }
};
```

**递归**

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
       if(head==NULL) return head;

       ListNode* newHead = head;
       if(head->next){
           newHead = reverseList(head->next);
           head->next->next = head;
           head->next = NULL;
        }
       return newHead;
    }
};
```

#### 4、两两交换链表中的节点

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
       if(head==NULL||head->next==NULL) return head;    // 只有一个节点和空节点无法交换

       ListNode* tmp1 = head, *tmp2 = head->next->next;    // 记录头节点和第三个节点

       head = head ->next;  // 对前两个节点进行交换
       head->next = tmp1;

       tmp1->next = swapPairs(tmp2);    // 对以第三个节点为头节点的子链表进行递归处理

       return head;

    }
};
```

**迭代**

* 每次交换都会影响4个节点

![](pic\24.两两交换链表中的节点1.png)

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head==NULL||head->next==NULL) return head;
        ListNode* dummyNode = new ListNode(0);  // 定义一个哨兵节点
        dummyNode->next = head;
        ListNode* cur = dummyNode;
        while(cur->next&&cur->next->next)   // 循环推出条件为cur节点后面无节点或者只有一个节点
        {
            // 每一轮交换的过程中都会影响四个节点
            ListNode* tmp = cur->next;  // 记录第二个节点
            ListNode* tmp1 = cur->next->next->next; // 记录第三个节点

            cur->next = cur->next->next;	// 步骤一
            cur->next->next = tmp;			// 步骤二
            cur->next->next->next = tmp1;	// 步骤三

            cur = cur->next->next;   // cur移动两位，准备下一轮交换
        }
        return dummyNode->next;

    }
};
```

#### 5、删除链表中第N个节点

**前后指针**，双指针法

* 创建一个哨兵节点
* 创建左右指针，左指针指向哨兵节点，右指针指向head，节点距离为1
* 将右指针向右移动n步，此时左右节点相差n+1步
* 左右指针同时移动，直至右指针指向了NULL，此时，左指针指向了倒数n+1个节点，也就是待删除节点的前一个节点
* 进行删除操作，在这一个过程中需要考虑删除的节点是否为倒数第一个的情况

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummyNode = new ListNode(0);   // 创建哨兵节点
        dummyNode->next=  head;
        // 创建前后指针;
        ListNode* left = dummyNode;
        ListNode* right = head;
        // 将right向右先移动n步，使得right和left之间的差距为n+1
        int k = 0;
        while(right!=NULL&&k<n){
            right = right->next;
            k++;
        }
        // left节点指向了第待删除节点的前一个节点
        while(right!=NULL){
            left = left->next;
            right = right->next;
        }
        // 删除左节点的下一个节点
        if(n==1){
            left->next = right; // 删除最后一个节点，此时left->next =NULL
        }else{
            left->next = left->next->next;
        }
        return dummyNode->next;
    }
};
```

#### 6、链表相交

* 定义curA和curB分别指向链表的头节点
* 求链表A和链表B的长度
* 让指向长的链表指针移动两链表的长度差个单位
* 同时移动两指针并比较指针值，如果值相等则返回当前指针，否则继续移动，最后返回NULL

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* curA = headA;
        ListNode* curB = headB;
        int lenA =0,lenB =0;
        while(curA){
            curA = curA->next;
            lenA++;
        }
        while(curB){
            curB = curB->next;
            lenB++;
        }
        curA = headA;
        curB = headB;
        if(lenB>lenA){  // 让curA指向最长的链表
            swap(lenA,lenB);
            swap(curA,curB);
        }
        int gap = lenA-lenB;    // 求长度差
        while(gap--){   
            curA = curA->next;
        }
        while(curA){
            if(curA==curB){
                return curA;
            }
            curA = curA->next;
            curB = curB->next;
        }
        return NULL;
    }
};
```

#### 7、环形链表

**常规解法**

* 使用一个vector数组记录遍历过程中的指针

* 循环的过程中判断当前节点是否在vector数组中，是则返回当前节点，否则将当前节点加入vector数组

  ```cpp
  class Solution {
  public:
      vector<ListNode*> vec;
      ListNode *detectCycle(ListNode *head) {
          if(head==NULL) return head;
          //vec.push_back(head);
          ListNode* cur = head;
          while(cur!=NULL){
              if(find(vec.begin(),vec.end(),cur)!=vec.end())
              {
                  return cur;
              }else{
                  vec.push_back(cur);
                  cur = cur->next;
              }
          }
          return NULL;
      }
  };
  ```

  

**快慢指针解法**

* 设两指针指向head节点

* 令fast指针每轮走两步，slow指针每轮走一步

* 存在循环，fast指针在循环中一定会与slow相遇

* 设链表长度为a+b，a:环外，b:环内，慢指针走过的路径长为s,快指针为f

* `f = 2s,s = nb,f = 2nb`

* `f-s = nb`即`f=2nb，s=nb`

* 第一次相遇，快指针比慢指针多走圈的n倍

* 如果让指针从链表头部一直向前走并统计步数k，**那么所有 走到链表入口节点时的步数** 是：`k=a+nb`，即先走 `a` 步到入口节点，之后每绕 1 圈环（ `b` 步）都会再次到入口节点。而目前 slow 指针走了`nb` 步。因此，我们只要想办法让 slow 再走 `a` 步停下来，就可以到环的入口。

  

  

```cpp
class Solution {
public:
    vector<ListNode*> vec;
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (true) {
            if (fast == nullptr || fast->next == nullptr) return nullptr;
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow) break;
        }
        fast = head;
        while (slow != fast) {
            slow = slow->next;
            fast = fast->next;
        }
        return fast;
    }

};
```

