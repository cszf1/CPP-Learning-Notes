# 第4章 数据结构

> 📊 **实用章节** - 程序设计的基石

---

## 📋 目录

1. [数据结构概述](#1-数据结构概述)
2. [线性表](#2-线性表)
3. [查找算法](#3-查找算法)
4. [排序算法](#4-排序算法)
5. [综合应用示例](#5-综合应用示例)
6. [常见问题与优化](#6-常见问题与优化)

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

## 4. 排序算法

### 4.1 什么是排序？

**排序（Sorting）**：将一组数据按照关键字有序排列。

**稳定排序 vs 不稳定排序**：
- 稳定：相等的元素排序后相对位置不变
- 不稳定：相等的元素排序后相对位置可能改变

### 4.2 插入排序

**思想**：就像打牌时整理手牌，每次将一张牌插入到已排好序的牌中。

```cpp
#include <iostream>
using namespace std;

// 直接插入排序
template<typename T>
void InsertSort(T arr[], int n) {
    T temp;
    int i, j;
    
    // 从第二个元素开始（假设第一个已经有序）
    for (i = 1; i < n; i++) {
        temp = arr[i];          // 暂存待插入元素
        j = i;
        
        // 从后往前找插入位置
        while (j > 0 && temp < arr[j - 1]) {
            arr[j] = arr[j - 1];  // 元素后移
            j--;
        }
        
        arr[j] = temp;          // 插入元素
    }
}

// 打印数组
template<typename T>
void PrintArray(T arr[], int n) {
    for (int i = 0; i < n; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;
}

int main() {
    int arr[] = {64, 34, 25, 12, 22, 11, 90};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    cout << "排序前: ";
    PrintArray(arr, n);
    
    InsertSort(arr, n);
    
    cout << "排序后: ";
    PrintArray(arr, n);
    
    return 0;
}
```

**排序过程演示**：
```
初始: [64, 34, 25, 12, 22, 11, 90]
第1轮: [64, 34, 25, 12, 22, 11, 90] → 34插入64前 → [34, 64, 25, 12, 22, 11, 90]
第2轮: [34, 64, 25, 12, 22, 11, 90] → 25插入34前 → [25, 34, 64, 12, 22, 11, 90]
第3轮: [25, 34, 64, 12, 22, 11, 90] → 12插入25前 → [12, 25, 34, 64, 22, 11, 90]
...（继续）
结果: [11, 12, 22, 25, 34, 64, 90]
```

**复杂度**：O(n²)，但对小规模数据效率不错。

### 4.3 冒泡排序

**思想**：相邻元素两两比较，像气泡一样把大的（或小的）往上冒。

```cpp
#include <iostream>
using namespace std;

// 冒泡排序
template<typename T>
void BubbleSort(T arr[], int n) {
    bool swapped;  // 标记是否发生过交换
    T temp;
    
    for (int i = 0; i < n - 1; i++) {
        swapped = false;
        
        // 从后往前两两比较，把小的冒到前面
        for (int j = n - 1; j > i; j--) {
            if (arr[j] < arr[j - 1]) {
                // 交换
                temp = arr[j];
                arr[j] = arr[j - 1];
                arr[j - 1] = temp;
                swapped = true;
            }
        }
        
        // 如果没有发生交换，说明已经有序，可以提前结束
        if (!swapped) break;
        
        cout << "第" << i + 1 << "轮: ";
        for (int k = 0; k < n; k++) cout << arr[k] << " ";
        cout << endl;
    }
}

int main() {
    int arr[] = {64, 34, 25, 12, 22, 11, 90};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    cout << "排序过程:\n";
    BubbleSort(arr, n);
    
    cout << "最终结果: ";
    for (int i = 0; i < n; i++) cout << arr[i] << " ";
    cout << endl;
    
    return 0;
}
```

**排序过程演示**（从小到大）：
```
初始: [64, 34, 25, 12, 22, 11, 90]

第1轮比较:
  90 vs 11 → 交换 → [..., 11, 90]
  22 vs 11 → 交换 → [..., 11, 22, 90]
  ...
  64 vs 34 → 交换 → [34, 64, 25, 12, 22, 11, 90]
第1轮结果: [34, 25, 12, 22, 11, 64, 90]

第2轮比较: [25, 12, 22, 11, 34, 64, 90]
第3轮比较: [12, 22, 11, 25, 34, 64, 90]
第4轮比较: [12, 11, 22, 25, 34, 64, 90]
第5轮比较: [11, 12, 22, 25, 34, 64, 90] ✓ 已有序

结果: [11, 12, 22, 25, 34, 64, 90]
```

**优化**：增加 `swapped` 标记，如果某轮没有交换，说明已经有序，可以提前结束！

### 4.4 选择排序

**思想**：每一轮从未排序部分选出最小（或最大）的元素，放到已排序部分的末尾。

```cpp
#include <iostream>
using namespace std;

// 选择排序
template<typename T>
void SelectSort(T arr[], int n) {
    int minPos;  // 最小元素的位置
    T temp;
    
    for (int i = 0; i < n - 1; i++) {
        minPos = i;
        
        // 在未排序部分找最小元素
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minPos]) {
                minPos = j;
            }
        }
        
        // 把最小元素换到位置i
        if (minPos != i) {
            temp = arr[i];
            arr[i] = arr[minPos];
            arr[minPos] = temp;
        }
        
        cout << "第" << i + 1 << "轮: ";
        for (int k = 0; k < n; k++) cout << arr[k] << " ";
        cout << endl;
    }
}

int main() {
    int arr[] = {45, 64, 24, 18, 8};
    int n = 5;
    
    cout << "排序过程:\n";
    SelectSort(arr, n);
    
    cout << "最终结果: ";
    for (int i = 0; i < n; i++) cout << arr[i] << " ";
    cout << endl;
    
    return 0;
}
```

**排序过程演示**：
```
初始: [45, 64, 24, 18, 8]
       ↑最小8换到位置0
第1轮: [8, 64, 24, 18, 45]
           ↑最小18换到位置1
第2轮: [8, 18, 24, 64, 45]
               ↑最小24不用换
第3轮: [8, 18, 24, 64, 45]
                   ↑最小45换到位置3
第4轮: [8, 18, 24, 45, 64]
结果: [8, 18, 24, 45, 64]
```

### 4.5 排序算法对比

| 算法 | 时间复杂度 | 空间复杂度 | 稳定性 |
|------|------------|------------|--------|
| 直接插入排序 | O(n²) | O(1) | 稳定 |
| 冒泡排序 | O(n²) | O(1) | 稳定 |
| 选择排序 | O(n²) | O(1) | 不稳定 |
| 快速排序 | O(n log n) | O(log n) | 不稳定 |
| 归并排序 | O(n log n) | O(n) | 稳定 |
| 堆排序 | O(n log n) | O(1) | 不稳定 |

**对于初学者**：重点掌握冒泡、插入、选择三种简单排序，理解排序的思想即可。

---

## 5. 综合应用示例

### 5.1 有序表类模板

结合折半查找的有序顺序表：

```cpp
#include <iostream>
using namespace std;

// 有序表类模板
template<typename T, int MAXSIZE>
class OrderedList {
private:
    T data[MAXSIZE];
    int last;

public:
    OrderedList() { last = -1; }
    
    int Length() const { return last + 1; }
    bool IsEmpty() const { return last == -1; }
    bool IsFull() const { return last == MAXSIZE - 1; }
    
    // 折半查找（迭代版本）
    int BinarySearch(const T& key) const {
        int low = 0, high = last, mid;
        
        while (low <= high) {
            mid = (low + high) / 2;
            if (data[mid] == key) {
                return mid;
            } else if (data[mid] > key) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return -1;
    }
    
    // 插入（保持有序）
    bool Insert(const T& x) {
        if (IsFull()) return false;
        
        int i = 0;
        while (i <= last && data[i] < x) {
            i++;
        }
        
        // 从后往前移动
        for (int j = last; j >= i; j--) {
            data[j + 1] = data[j];
        }
        data[i] = x;
        last++;
        return true;
    }
    
    // 删除
    bool Remove(const T& x) {
        int pos = BinarySearch(x);
        if (pos == -1) return false;
        
        for (int i = pos; i < last; i++) {
            data[i] = data[i + 1];
        }
        last--;
        return true;
    }
    
    // 打印
    void Print() const {
        for (int i = 0; i <= last; i++) {
            cout << data[i] << " ";
        }
        cout << endl;
    }
};

// 学生类
class Student {
public:
    int id;
    string name;
    float score;
    
    Student(int i = 0, string n = "", float s = 0) 
        : id(i), name(n), score(s) {}
};

int main() {
    // 整数有序表
    OrderedList<int, 100> intList;
    int arr[] = {3, 7, 2, 9, 1, 5, 8, 4};
    
    for (int i = 0; i < 8; i++) {
        intList.Insert(arr[i]);
    }
    
    cout << "有序数组: ";
    intList.Print();
    
    cout << "查找7: " << intList.BinarySearch(7) << endl;
    cout << "查找6: " << intList.BinarySearch(6) << endl;
    
    intList.Remove(5);
    cout << "删除5后: ";
    intList.Print();
    
    return 0;
}
```

### 5.2 通讯录管理系统（简化版）

```cpp
#include <iostream>
#include <string>
using namespace std;

// 联系人结构
struct Contact {
    string name;
    string phone;
    string email;
};

// 通讯录类
class AddressBook {
private:
    Contact contacts[100];
    int count;

public:
    AddressBook() { count = 0; }
    
    // 添加联系人
    bool Add(const string& name, const string& phone, const string& email) {
        if (count >= 100) return false;
        contacts[count].name = name;
        contacts[count].phone = phone;
        contacts[count].email = email;
        count++;
        return true;
    }
    
    // 按姓名查找
    int FindByName(const string& name) {
        for (int i = 0; i < count; i++) {
            if (contacts[i].name == name) {
                return i;
            }
        }
        return -1;
    }
    
    // 显示联系人
    void Show(int index) {
        if (index < 0 || index >= count) return;
        cout << "姓名: " << contacts[index].name << endl;
        cout << "电话: " << contacts[index].phone << endl;
        cout << "邮箱: " << contacts[index].email << endl;
    }
    
    // 显示所有
    void ShowAll() {
        for (int i = 0; i < count; i++) {
            Show(i);
            cout << "---" << endl;
        }
    }
    
    int GetCount() { return count; }
};

int main() {
    AddressBook book;
    
    book.Add("张三", "13800138000", "zhangsan@example.com");
    book.Add("李四", "13900139000", "lisi@example.com");
    book.Add("王五", "13700137000", "wangwu@example.com");
    
    cout << "通讯录共有 " << book.GetCount() << " 个联系人\n" << endl;
    
    int pos = book.FindByName("李四");
    if (pos != -1) {
        cout << "找到李四:" << endl;
        book.Show(pos);
    }
    
    return 0;
}
```

---

## 6. 常见问题与优化

### 6.1 选择合适的数据结构

| 场景 | 推荐结构 |
|------|----------|
| 需要快速随机访问 | 数组/顺序表 |
| 频繁插入删除 | 链表 |
| 需要保持有序 | 有序数组 |
| 需要快速查找 | 二叉搜索树/哈希表 |

### 6.2 排序算法选择建议

| 数据规模 | 推荐算法 |
|----------|----------|
| n ≤ 100 | 插入排序、选择排序 |
| n 较大 | 快速排序、归并排序 |
| 有特殊要求 | 堆排序（空间受限） |

### 6.3 时间复杂度速查表

| n | log₂n | n log₂n | n² |
|---|-------|---------|-----|
| 10 | 3 | 33 | 100 |
| 100 | 7 | 664 | 10,000 |
| 1000 | 10 | 10,000 | 1,000,000 |
| 10000 | 13 | 130,000 | 100,000,000 |

---

## 📝 本章小结

1. **线性表**是最基础的数据结构，顺序表用数组实现，支持随机访问。

2. **查找算法**：顺序查找简单但慢O(n)，折半查找快O(log n)但要求数据有序。

3. **排序算法**：插入、冒泡、选择三种基础排序都是O(n²)，适合小规模数据。

4. **数据结构选择**：根据实际需求（访问、插入、删除的频率）选择合适的数据结构。

5. **模板让数据结构更通用**：使用类模板可以创建支持多种类型的"通用"数据结构。

---

## 📚 练习建议

### 基础练习
1. 实现顺序表的 `逆置` 操作
2. 实现折半查找的递归版本
3. 用冒泡排序对数组排序
4. 实现选择排序

### 进阶练习
5. 实现两个有序表的合并
6. 实现链表的基本操作（插入、删除、查找）
7. 实现栈（后进先出）的类模板

### 综合练习
8. 实现一个学生成绩管理系统（包含添加、查找、排序功能）
9. 比较不同排序算法的执行时间

---

*返回 [主目录](../README.md)*
