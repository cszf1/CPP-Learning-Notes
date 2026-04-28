# 第4章 数据结构

> 📊 **实用章节** - 程序设计的基石

---

## 📋 目录

1. [数据结构概述](#1-数据结构概述)
2. [线性表](#2-线性表)
3. [单链表](#3-单链表)
   - [3.1 链表 vs 顺序表](#31-链表-vs-顺序表)
   - [3.2 单链表结点结构](#32-单链表结点结构)
   - [3.3 单链表基本操作](#33-单链表基本操作)
   - [3.4 带表头的单链表](#34-带表头的单链表)
   - [3.5 链表的生成算法](#35-链表的生成算法)
   - [3.6 单链表模板类](#36-单链表模板类)
   - [3.7 综合应用示例](#37-综合应用示例)
4. [查找算法](#4-查找算法)
5. [排序算法](#5-排序算法)
   - [4.1 什么是排序？](#41-什么是排序)
   - [4.2 插入排序](#42-插入排序)
   - [4.3 冒泡排序](#43-冒泡排序)
   - [4.4 选择排序](#44-选择排序)
   - [4.5 对半插入排序](#45-对半插入排序二分插入排序)
   - [4.6 希尔排序](#46-希尔排序缩小增量排序)
   - [4.7 快速排序](#47-快速排序)
   - [4.8 归并排序](#48-归并排序分而治之)
   - [4.9 排序算法对比](#49-排序算法对比)
5. [动态内存分配](#5-动态内存分配)
6. [综合应用示例](#6-综合应用示例)
7. [常见问题与优化](#7-常见问题与优化)

---

## 1. 数据结构概述

### 1.1 什么是数据结构？

**数据结构**：数据之间的关系和组织方式。

就像图书馆的书架：
- **数组**：固定大小的书架，每层放固定位置的书
- **链表**：用绳子串联的书，可以灵活插入删除
- **树**：按照分类组织的书架（小说区、计算机区...）

### 1.2 为什么要学习数据结构？

```cpp
// 场景：在10000个学生中找到某个学生
// 方法1：数组，顺序查找 → 最坏情况：查10000次
// 方法2：二分查找树 → 最多查14次
```

**好的数据结构能让算法效率提升千百倍！**

### 1.3 线性关系

线性表是数据结构中最基础的概念：

- **有限序列**：元素个数有限
- **前驱后继**：除了首尾，每个元素有且仅有一个直接前驱和一个直接后继
- **第一个元素**没有前驱
- **最后一个元素**没有后继

```
线性表示例：[ 1 | 3 | 5 | 7 | 9 ]
              ↑  ↑  ↑  ↑  ↑
             首      任意位置     尾
             前驱←元素→后继
```

---

## 2. 线性表

### 2.1 顺序表 vs 链表

| 特性 | 顺序表 | 链表 |
|------|--------|------|
| 存储方式 | 连续内存 | 分散内存+指针 |
| 访问速度 | O(1) 随机访问 | O(n) 顺序访问 |
| 插入删除 | O(n) 需要移动 | O(1) 修改指针 |
| 内存利用率 | 固定大小，可能浪费 | 按需分配 |

**通俗理解**：
- 顺序表就像电影院的座位，固定位置，插入要"挪人"
- 链表就像游乐场的排队手环，随时可以插队

### 2.2 顺序表类模板实现

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

// 顺序表类模板
template <typename T, int MAXSIZE>
class SeqList {
private:
    T data[MAXSIZE];    // 存放数据的数组
    int last;            // 最后一个元素的下标，-1表示空表

public:
    // 构造函数：初始化为空表
    SeqList() { last = -1; }
    
    // 获取表长度
    int Length() const { return last + 1; }
    
    // 判断表是否为空
    bool IsEmpty() const { return last == -1; }
    
    // 判断表是否已满
    bool IsFull() const { return last == MAXSIZE - 1; }
    
    // 查找元素x的位置，返回下标，未找到返回-1
    int Find(const T& x) const {
        int i = 0;
        while (i <= last && data[i] != x) {
            i++;
        }
        return (i > last) ? -1 : i;
    }
    
    // 判断元素x是否在表中
    bool IsIn(const T& x) const {
        return Find(x) != -1;
    }
    
    // 在第i个位置插入元素x（下标从0开始）
    bool Insert(const T& x, int i) {
        // 检查位置是否合法、表是否已满
        if (i < 0 || i > last + 1 || IsFull()) {
            return false;
        }
        // 从后往前移动元素，空出第i个位置
        for (int j = last; j >= i; j--) {
            data[j + 1] = data[j];
        }
        data[i] = x;
        last++;
        return true;
    }
    
    // 删除第一个值为x的元素
    bool Remove(const T& x) {
        int pos = Find(x);
        if (pos == -1) return false;  // 没找到
        
        // 从pos开始，后面的元素往前移动
        for (int j = pos; j < last; j++) {
            data[j] = data[j + 1];
        }
        last--;
        return true;
    }
    
    // 查找元素x的后继位置
    int Next(const T& x) const {
        int pos = Find(x);
        if (pos >= 0 && pos < last) {
            return pos + 1;
        }
        return -1;
    }
    
    // 查找元素x的前驱位置
    int Prior(const T& x) const {
        int pos = Find(x);
        if (pos > 0) {
            return pos - 1;
        }
        return -1;
    }
    
    // 重载下标运算符[]
    T& operator[](int i) {
        if (i < 0 || i > last) {
            cout << "下标越界！" << endl;
            exit(1);
        }
        return data[i];
    }
    
    const T& operator[](int i) const {
        if (i < 0 || i > last) {
            cout << "下标越界！" << endl;
            exit(1);
        }
        return data[i];
    }
    
    // 清空表
    void Clear() { last = -1; }
};
```

### 2.3 顺序表使用示例

```cpp
int main() {
    // 创建一个能存100个整数的顺序表
    SeqList<int, 100> primes;
    
    // 插入素数
    int primes_arr[] = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29};
    for (int i = 0; i < 10; i++) {
        primes.Insert(primes_arr[i], i);
    }
    
    // 使用下标访问
    cout << "第5个素数: " << primes[4] << endl;
    cout << "表长度: " << primes.Length() << endl;
    
    // 查找
    int key = 17;
    if (primes.IsIn(key)) {
        cout << key << " 在表中，下标: " << primes.Find(key) << endl;
    }
    
    // 找后继和前驱
    cout << "17的后继: " << primes[primes.Next(key)] << endl;
    cout << "17的前驱: " << primes[primes.Prior(key)] << endl;
    
    // 删除
    primes.Remove(17);
    cout << "删除17后表长度: " << primes.Length() << endl;
    
    // 遍历
    cout << "表中所有元素: ";
    for (int i = 0; i < primes.Length(); i++) {
        cout << primes[i] << " ";
    }
    cout << endl;
    
    return 0;
}
```

**运行结果**：
```
第5个素数: 11
表长度: 10
17 在表中，下标: 6
17的后继: 19
17的前驱: 13
删除17后表长度: 9
表中所有元素: 2 3 5 7 11 13 19 23 29 
```

### 2.4 顺序表操作复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| 访问元素 `[]` | O(1) | 直接通过下标访问 |
| 查找元素 | O(n) | 最坏情况遍历整个表 |
| 插入元素 | O(n) | 需要移动元素 |
| 删除元素 | O(n) | 需要移动元素 |

---

## 3. 查找算法

### 3.1 什么是查找？

**查找（Search）**：在数据集合中寻找满足条件的数据。

**关键字（Key）**：用于识别数据的字段，如学号、身份证号等。

### 3.2 顺序查找

**原理**：从第一个元素开始，依次向后查找，直到找到或查完所有元素。

```cpp
#include <iostream>
using namespace std;

// 顺序查找函数模板
template<typename T>
int SeqSearch(const T arr[], int n, const T& key) {
    for (int i = 0; i < n; i++) {
        if (arr[i] == key) {
            return i;  // 找到，返回下标
        }
    }
    return -1;  // 未找到
}

int main() {
    int arr[] = {64, 34, 25, 12, 22, 11, 90};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    int key = 25;
    int pos = SeqSearch(arr, n, key);
    
    if (pos != -1) {
        cout << key << " 在数组中，下标为 " << pos << endl;
    } else {
        cout << key << " 不在数组中" << endl;
    }
    
    return 0;
}
```

**特点**：
- 优点：对数据无要求，简单
- 缺点：速度慢，平均要查 n/2 次

### 3.3 折半查找（二分查找）

**前提**：数据必须有序排列！

**原理**：
1. 取中间位置的元素与目标比较
2. 如果相等，找到
3. 如果目标较小，在左半部分继续查找
4. 如果目标较大，在右半部分继续查找
5. 重复直到找到或范围为空

```cpp
#include <iostream>
using namespace std;

// 折半查找（递归版本）
template<typename T>
int BinarySearch(const T arr[], int low, int high, const T& key) {
    if (low > high) {
        return -1;  // 未找到
    }
    
    int mid = (low + high) / 2;
    
    if (arr[mid] == key) {
        return mid;  // 找到
    } else if (arr[mid] > key) {
        return BinarySearch(arr, low, mid - 1, key);  // 在左半部分找
    } else {
        return BinarySearch(arr, mid + 1, high, key);  // 在右半部分找
    }
}

// 折半查找（迭代版本，更高效）
template<typename T>
int BinarySearch2(const T arr[], int n, const T& key) {
    int low = 0, high = n - 1;
    
    while (low <= high) {
        int mid = (low + high) / 2;
        
        if (arr[mid] == key) {
            return mid;
        } else if (arr[mid] > key) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    
    return -1;
}

int main() {
    // 必须是有序数组！
    int arr[] = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    int key = 17;
    
    cout << "递归版: " << BinarySearch(arr, 0, n - 1, key) << endl;
    cout << "迭代版: " << BinarySearch2(arr, n, key) << endl;
    
    return 0;
}
```

**查找过程演示**（找17）：
```
数组: [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
         L                M                H
第1轮:  low=0   mid=4   high=9   arr[4]=11 < 17 → 在右半部分
第2轮:  low=5   mid=7   high=9   arr[7]=19 > 17 → 在左半部分
第3轮:  low=5   mid=6   high=7   arr[6]=17 = 17 → 找到！
```

**复杂度分析**：
| 查找方法 | 时间复杂度 | 空间复杂度 |
|----------|------------|------------|
| 顺序查找 | O(n) | O(1) |
| 折半查找 | O(log₂n) | O(1) |

**效率对比**：
| 数据规模 n | 顺序查找最多 | 折半查找最多 |
|------------|-------------|--------------|
| 100 | 100次 | 7次 |
| 1000 | 1000次 | 10次 |
| 1000000 | 1000000次 | 20次 |

### 3.4 分块查找（索引查找）

**思想**：数据分成若干块，块内无序，块间有序。建立索引表加速查找。

```
原始数据: [18, 10, 25, 39, 26, 42, 51, 30]
分块:
  块1: [18, 10, 25]     最大值=25
  块2: [39, 26, 42]     最大值=42
  块3: [51, 30]         最大值=51
  
索引表: [(25, 0), (42, 3), (51, 6)]
        ↑块最大值  ↑块起始下标
```

```cpp
#include <iostream>
using namespace std;

// 分块查找
struct IndexItem {
    int maxValue;   // 块内最大值
    int startPos;   // 块起始位置
};

int BlockSearch(int arr[], int n, int key, IndexItem index[], int blockCount) {
    // 1. 在索引表中确定可能存在的块
    int i = 0;
    while (i < blockCount && index[i].maxValue < key) {
        i++;
    }
    
    if (i >= blockCount) return -1;  // 不在表中
    
    // 2. 在确定的块内顺序查找
    int start = index[i].startPos;
    int end = (i + 1 < blockCount) ? index[i + 1].startPos - 1 : n - 1;
    
    for (int j = start; j <= end; j++) {
        if (arr[j] == key) {
            return j;
        }
    }
    
    return -1;  // 未找到
}

int main() {
    int arr[] = {18, 10, 25, 39, 26, 42, 51, 30};
    int n = 8;
    
    IndexItem index[] = {
        {25, 0},   // 第一块: 最大值25, 起始位置0
        {42, 3},   // 第二块: 最大值42, 起始位置3
        {51, 6}    // 第三块: 最大值51, 起始位置6
    };
    
    cout << "30的位置: " << BlockSearch(arr, n, 30, index, 3) << endl;
    cout << "39的位置: " << BlockSearch(arr, n, 39, index, 3) << endl;
    
    return 0;
}
```

---

## 3. 单链表

### 3.1 链表 vs 顺序表

**为什么需要链表？**

想象一下，在顺序表中间插入一个元素：

```
顺序表插入前: [ A | B | C | D | E ]
                 ↑ 插入X

顺序表插入后: [ A | B | X | C | D | E ]
              需要移动C、D、E三个元素！
```

**链表的优势**：插入/删除只需要修改几个指针，不需要移动元素。

```
链表插入前:  A → B → C → D → E
                 ↑ 插入X

链表插入后:  A → B → X → C → D → E
              只需要改两个指针！
```

**顺序表 vs 链表对比**：

| 特性 | 顺序表 | 链表 |
|------|--------|------|
| 存储方式 | 连续内存 | 分散内存+指针连接 |
| 访问速度 | O(1) 随机访问 | O(n) 顺序访问 |
| 插入删除 | O(n) 需移动元素 | O(1) 只改指针 |
| 内存利用 | 固定大小 | 按需分配 |
| 内存布局 | 紧密排列 | 可能碎片化 |

**通俗理解**：
- 顺序表就像电影院的固定座位，买票后位置就定了，插入要"挪人"
- 链表就像游乐场的排队手环，随时可以插入到任意位置

### 3.2 单链表结点结构

**单链表的组成**：每个元素占用一个**结点（Node）**，结点包含：
- **数据域**：存放数据元素
- **指针域**：存放指向下一个结点的指针

```cpp
#include <iostream>
using namespace std;

// 定义数据类型
typedef int Datatype;

// 定义链表结点结构
struct Node {
    Datatype info;    // 数据域：存放数据
    Node *link;       // 指针域：指向下一个结点
};
```

**单链表示意图**：

```
head
  ↓
┌────┬───┐    ┌────┬───┐    ┌────┬───┐         ┌────┬────┐
│info0│ ──┼──► │info1│ ──┼──► │info2│ ──┼──► ... ──► │infoN│ NULL│
└────┴───┘    └────┴───┘    └────┴───┘         └────┴────┘
   数据域      指针域        数据域
   (首结点)                   (尾结点)
```

**⚠️ 重要概念**：
- `head` 是表头指针，存放首结点的地址
- `head` 丢失则链表无法访问，会造成内存泄漏！
- 尾结点的 `link` 必须为 `NULL`（表示链表结束）

### 3.3 单链表基本操作

#### 3.3.1 插入操作

**三种插入情况**：

**Case a) 插在链首**：
```cpp
// 在链表头部插入新结点
newnode->link = head;  // 新结点指向原来的首结点
head = newnode;        // head指向新结点
```

```
插入前: head → [A] → [B] → [C] → NULL
插入X:        newnode → X

插入后: head → [X] → [A] → [B] → [C] → NULL
```

**Case b) 插在中间**：
```cpp
// 在p结点后插入新结点
newnode->link = p->link;  // 新结点指向p的后继
p->link = newnode;         // p指向新结点
```

```
插入前: ... → [P] → [Q] → [R] → ...
                 ↑
               插入X

插入后: ... → [P] → [X] → [Q] → [R] → ...
```

**Case c) 插在队尾**：
```cpp
// 在链表尾部插入新结点
newnode->link = p->link;  // 新结点指向NULL
p->link = newnode;        // 尾结点指向新结点
```

```
插入前: ... → [P] → NULL
插入X:              newnode → X

插入后: ... → [P] → [X] → NULL
```

**⚠️ 注意事项**：
- 链表操作**顺序非常重要**！
- 必须先处理新结点的指针，再修改原链表
- 顺序搞反会导致链表断开

#### 3.3.2 一般插入算法

```cpp
// 将数据x插入到单链表p结点之后
void Insert(Node *p, Datatype x) {
    Node *newnode = new Node;      // 创建新结点
    newnode->info = x;              // 存入数据
    newnode->link = p->link;        // 新结点指向p的后继
    p->link = newnode;              // p指向新结点
}
```

#### 3.3.3 遍历链表

```cpp
// 打印链表中的所有数据
void PrintList(Node *head) {
    Node *p = head->link;  // 从首结点开始（跳过表头）
    
    while (p != NULL) {
        cout << p->info << '\t';
        p = p->link;  // 移动到下一个结点
    }
    cout << endl;
}
```

**遍历过程图解**：
```
p = head->link (指向首结点)
     ↓
┌────┐     ┌────┐     ┌────┐
│ A  │ ──► │ B  │ ──► │ C  │ ──► NULL
└────┘     └────┘     └────┘
  p↑       打印A后p移动到这里
```

#### 3.3.4 查找操作

```cpp
// 在链表中查找数据为x的结点，返回结点地址
Node* Find(Node *head, Datatype x) {
    Node *p = head->link;  // 从首结点开始
    
    while (p != NULL && p->info != x) {
        p = p->link;  // 移动到下一个结点
    }
    
    return p;  // 找到返回地址，未找到返回NULL
}
```

#### 3.3.5 删除操作

```cpp
// 删除p结点后面的一个结点
void Delete(Node *p) {
    Node *q;  // q指向要删除的结点
    
    q = p->link;  // q指向p后面的结点
    
    if (q != NULL) {  // 如果q存在
        p->link = q->link;  // p指向q的后继（跳过q）
        delete q;            // 释放q的内存
    }
}
```

**删除操作图解**：
```
删除前: ... → [P] → [Q] → [R] → ...
删除Q:       q指向这里

删除后: ... → [P] → [R] → ...
```

### 3.4 带表头的单链表

**为什么需要表头结点？**

- 统一操作：链首、链中、链尾的操作方式一致
- 方便处理：不用单独处理空链表或链首的特殊情况

**带表头的单链表结构**：

```
head
  ↓
┌────┬───┐    ┌────┬───┐    ┌────┬───┐         ┌────┬────┐
│ 空  │ ──┼──► │info0│ ──┼──► │info1│ ──┼──► ... ──► │infoN│ NULL│
└────┴───┘    └────┴───┘    └────┴───┘         └────┴────┘
  表头结点      首结点        尾结点
```

**带表头的空链表**：
```
head
  ↓
┌────┬────┐
│ 空  │NULL│
└────┴────┘
```

**带表头的插入**：
```cpp
// 在表头结点后插入（相当于插在链首）
newnode->link = head->link;  // 新结点指向原首结点
head->link = newnode;         // 表头指向新结点
```

### 3.5 链表的生成算法

#### 3.5.1 向后生成法

**思想**：新结点添加到链表尾部

```cpp
// 向后生成链表：输入数据，创建链表
Node* CreateDown() {
    Datatype data;
    Node *head, *tail, *p;
    
    // 1. 建立头结点
    head = new Node;
    head->link = NULL;  // 空链表
    tail = head;        // tail指向头结点（也是尾结点）
    
    // 2. 输入数据，创建新结点，添加到尾部
    while (cin >> data) {  // 输入EOF结束
        p = new Node;      // 创建新结点
        p->info = data;    // 存入数据
        p->link = tail->link;  // 新结点指向NULL
        tail->link = p;        // 尾结点指向新结点
        tail = p;               // tail移动到新结点
    }
    
    return head;
}
```

**向后生成法图解**：
```
初始:  head → NULL
       tail
       
输入A: head → [A] → NULL
              tail

输入B: head → [A] → [B] → NULL
                     tail

输入C: head → [A] → [B] → [C] → NULL
                            tail
```

**特点**：
- 数据顺序与输入顺序相同
- 适合顺序存储的场景

#### 3.5.2 向前生成法

**思想**：新结点添加到链表头部

```cpp
// 向前生成链表：输入数据，创建链表
Node* CreateUp() {
    Datatype data;
    Node *head, *p;
    
    // 1. 建立头结点
    head = new Node;
    head->link = NULL;  // 空链表
    
    // 2. 输入数据，创建新结点，添加到头部
    while (cin >> data) {
        p = new Node;           // 创建新结点
        p->info = data;         // 存入数据
        p->link = head->link;  // 新结点指向原首结点
        head->link = p;         // 头结点指向新结点
    }
    
    return head;
}
```

**向前生成法图解**：
```
初始:  head → NULL

输入A: head → [A] → NULL

输入B: head → [B] → [A] → NULL

输入C: head → [C] → [B] → [A] → NULL
```

**特点**：
- 数据顺序与输入顺序相反
- 适合逆序存储的场景

### 3.6 单链表模板类

为了通用性，使用模板实现单链表类：

```cpp
#include <iostream>
using namespace std;

// 前向声明
template<typename T>
class List;

// ==================== 结点类模板 ====================
template<typename T>
class Node {
private:
    T info;              // 数据域
    Node<T> *link;       // 指针域
    
public:
    Node();                      // 构造函数（生成头结点）
    Node(const T &data);        // 构造函数（生成数据结点）
    void InsertAfter(Node<T>* p);  // 在当前结点后插入
    Node<T>* RemoveAfter();       // 删除当前结点的后继
    friend class List<T>;         // List是友元类
};

// 构造函数：生成头结点
template<typename T>
Node<T>::Node() {
    link = NULL;  // 杜绝野指针！
}

// 构造函数：生成数据结点
template<typename T>
Node<T>::Node(const T &data) {
    info = data;
    link = NULL;
}

// 在当前结点后插入p
template<typename T>
void Node<T>::InsertAfter(Node<T>* p) {
    p->link = link;  // p指向当前结点的后继
    link = p;        // 当前结点指向p
}

// 删除当前结点的后继结点，返回被删结点
template<typename T>
Node<T>* Node<T>::RemoveAfter() {
    Node<T> *tempP = link;  // 暂存后继结点
    
    if (link == NULL) {
        tempP = NULL;
    } else {
        link = tempP->link;  // 跳过tempP
    }
    
    return tempP;
}
```

**链表类模板**：

```cpp
// ==================== 链表类模板 ====================
template<typename T>
class List {
private:
    Node<T> *head;  // 头指针
    Node<T> *tail;  // 尾指针
    
public:
    List();                 // 构造函数：生成头结点(空链表)
    ~List();                // 析构函数
    void MakeEmpty();       // 清空链表，只余表头结点
    Node<T>* Find(const T &data) const;  // 搜索结点，返回地址
    int Length() const;     // 计算单链表长度
    void PrintList() const;  // 打印链表的数据域
    void InsertFront(Node<T>* p);   // 向前生成链表
    void InsertRear(Node<T>* p);    // 向后生成链表
    void InsertOrder(Node<T>* p);   // 按升序生成链表
    Node<T>* CreatNode(const T &data);  // 创建结点(孤立结点)
    Node<T>* DeleteNode(Node<T>* p);     // 删除指定结点
};

// 构造函数
template<typename T>
List<T>::List() {
    head = tail = new Node<T>();  // 创建头结点
}

// 析构函数
template<typename T>
List<T>::~List() {
    MakeEmpty();      // 删除所有数据结点
    delete head;      // 删除头结点
}

// 清空链表
template<typename T>
void List<T>::MakeEmpty() {
    Node<T> *tempP;
    
    while (head->link != NULL) {
        tempP = head->link;
        head->link = tempP->link;  // 跳过tempP
        delete tempP;              // 释放
    }
    
    tail = head;  // tail回到头结点
}

// 搜索结点
template<typename T>
Node<T>* List<T>::Find(const T &data) const {
    Node<T> *tempP = head->link;  // 从首结点开始
    
    while (tempP != NULL && tempP->info != data) {
        tempP = tempP->link;
    }
    
    return tempP;  // 找到返回地址，未找到返回NULL
}

// 计算长度
template<typename T>
int List<T>::Length() const {
    int count = 0;
    Node<T> *tempP = head->link;
    
    while (tempP != NULL) {
        tempP = tempP->link;
        count++;
    }
    
    return count;
}

// 打印链表
template<typename T>
void List<T>::PrintList() const {
    Node<T> *tempP = head->link;
    
    while (tempP != NULL) {
        cout << tempP->info << "\t";
        tempP = tempP->link;
    }
    cout << endl;
}

// 向前生成链表（头插法）
template<typename T>
void List<T>::InsertFront(Node<T> *p) {
    p->link = head->link;  // 新结点指向原首结点
    head->link = p;        // 头结点指向新结点
    
    if (tail == head) {    // 如果是空链表
        tail = p;          // tail指向第一个数据结点
    }
}

// 向后生成链表（尾插法）
template<typename T>
void List<T>::InsertRear(Node<T> *p) {
    p->link = tail->link;  // 新结点指向NULL
    tail->link = p;        // 尾结点指向新结点
    tail = p;              // tail移动到新结点
}

// 按升序生成链表
template<typename T>
void List<T>::InsertOrder(Node<T> *p) {
    Node<T> *tempP = head->link;
    Node<T> *tempQ = head;
    
    // 查找正确的插入位置
    while (tempP != NULL) {
        if (p->info < tempP->info) {
            break;  // 找到插入位置
        }
        tempQ = tempP;
        tempP = tempP->link;
    }
    
    tempQ->InsertAfter(p);  // 在tempQ后插入p
    
    if (tail == tempQ) {     // 如果插入到尾结点之后
        tail = tempQ->link;
    }
}

// 创建孤立结点
template<typename T>
Node<T>* List<T>::CreatNode(const T &data) {
    Node<T> *tempP = new Node<T>(data);
    return tempP;
}

// 删除指定结点
template<typename T>
Node<T>* List<T>::DeleteNode(Node<T> *p) {
    Node<T> *tempP = head;
    
    // 查找p的前驱
    while (tempP->link != NULL && tempP->link != p) {
        tempP = tempP->link;
    }
    
    if (tempP->link == tail) {  // 如果删除尾结点
        tail = tempP;
    }
    
    return tempP->RemoveAfter();  // 删除并返回p
}
```

### 3.7 综合应用示例

```cpp
#include "singlelinklist.h"
#include <cstdlib>
#include <ctime>

int main() {
    Node<int>* P1, *P2;
    List<int> list1, list2;
    int *a, i, j, N;
    
    // 1. 输入链表结点个数
    cout << "请输入链表结点个数: ";
    cin >> N;
    
    // 2. 随机产生N个不同的整数
    a = new int[N];
    srand(0);
    
    for (i = 0; i < N; i++) {
        a[i] = rand() % 100 + 1;  // 1~100的随机数
        
        // 确保不重复
        for (j = 0; j < i; j++) {
            if (a[i] == a[j]) {
                i--;  // 重新生成
                break;
            }
        }
    }
    
    // 3. 生成两个链表
    for (i = 0; i < N; i++) {
        P1 = list1.CreatNode(a[i]);
        list1.InsertFront(P1);  // 向前生成list1
        
        P1 = list2.CreatNode(a[i]);
        list2.InsertRear(P1);   // 向后生成list2
    }
    
    // 4. 输出链表
    cout << "list1 (向前生成): ";
    list1.PrintList();
    cout << "list1长度: " << list1.Length() << endl;
    
    cout << "list2 (向后生成): ";
    list2.PrintList();
    cout << "list2长度: " << list2.Length() << endl;
    
    // 5. 查找并删除
    cout << "请输入一个要求删除的整数: ";
    cin >> j;
    
    P1 = list1.Find(j);
    if (P1 != NULL) {
        list1.DeleteNode(P1);
        cout << "删除后的list1: ";
        list1.PrintList();
        cout << "list1长度: " << list1.Length() << endl;
    } else {
        cout << "list1中未找到" << j << endl;
    }
    
    P2 = list2.Find(j);
    if (P2 != NULL) {
        list2.DeleteNode(P2);
        delete P2;  // 释放被删除结点的空间
        cout << "删除后的list2: ";
        list2.PrintList();
        cout << "list2长度: " << list2.Length() << endl;
    } else {
        cout << "list2中未找到" << j << endl;
    }
    
    // 6. 将删除的结点插入list2尾部
    cout << "将list1中删除的结点插入list2尾部: " << endl;
    list2.InsertRear(P1);
    list2.PrintList();
    
    // 7. 按升序生成链表
    list1.MakeEmpty();  // 清空list1
    for (i = 0; i < 15; i++) {
        P1 = list1.CreatNode(a[i]);
        list1.InsertOrder(P1);  // 升序插入
    }
    cout << "list1按升序排列: ";
    list1.PrintList();
    
    delete[] a;
    return 0;
}
```

**运行结果示例**：
```
请输入链表结点个数: 5
请输入一个要求删除的整数: 30
list1 (向前生成): 87    45    30    92    18    
list1长度: 5
list2 (向后生成): 18    92    30    45    87    
list2长度: 5
删除后的list1: 87    45    92    18    
list1长度: 4
删除后的list2: 18    92    45    87    
list2长度: 4
将list1中删除的结点插入list2尾部: 
18    92    45    87    30    
list1按升序排列: 18    30    45    87    92    
```

### 3.8 ⚠️ 链表使用注意事项

1. **不要忘记初始化指针**
   ```cpp
   Node() { link = NULL; }  // 杜绝野指针！
   ```

2. **插入顺序很重要**
   ```cpp
   // ✅ 正确顺序
   newnode->link = p->link;
   p->link = newnode;
   
   // ❌ 错误顺序（会导致链表断开）
   p->link = newnode;
   newnode->link = p->link;
   ```

3. **删除后要释放内存**
   ```cpp
   Node *temp = p->link;
   p->link = temp->link;
   delete temp;  // 释放内存
   ```

4. **链表为空时的处理**
   ```cpp
   if (head->link == NULL) {
       cout << "链表为空" << endl;
   }
   ```

5. **使用智能指针更安全**（C++11+）
   ```cpp
   struct Node {
       int info;
       std::unique_ptr<Node> link;
   };
   // 自动管理内存，避免内存泄漏
   ```

