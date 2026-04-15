# 第4章 数据结构

> 📊 **实用章节** - 程序设计的基石

---

## 📋 目录

1. [数据结构概述](#1-数据结构概述)
2. [线性表](#2-线性表)
3. [查找算法](#3-查找算法)
4. [排序算法](#4-排序算法)
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

### 4.5 对半插入排序（二分插入排序）

**概念**：对半插入排序是对直接插入排序的优化。在插入元素时，使用**折半查找（二分查找）**来寻找插入位置，而不是逐个比较。

**与直接插入排序的区别**：
- **直接插入排序**：从已排好序的部分从后往前逐个比较，最坏情况比较 i 次
- **对半插入排序**：使用折半查找定位，最多比较 log₂i 次，但元素移动次数不变

**通俗理解**：就像在有序数组中查字典，用折半法快速定位插入位置，而不是从第一页开始一页一页翻。

```cpp
#include <iostream>
using namespace std;

// 对半插入排序（Binary Insertion Sort）
template<typename T>
void BinaryInsertSort(T arr[], int n) {
    T temp;
    int low, high, mid, i, j;
    
    // 从第二个元素开始（假设第一个已经有序）
    for (i = 1; i < n; i++) {
        temp = arr[i];      // 暂存待插入元素
        low = 0;
        high = i - 1;       // 已排序部分的范围是 [0, i-1]
        
        // === 折半查找：找到插入位置 ===
        while (low <= high) {
            mid = (low + high) / 2;
            if (temp < arr[mid]) {
                high = mid - 1;  // 目标在左半部分
            } else {
                low = mid + 1;   // 目标在右半部分
            }
        }
        // 循环结束后，low 就是插入位置
        
        // === 元素后移，空出插入位置 ===
        for (j = i - 1; j >= low; j--) {
            arr[j + 1] = arr[j];
        }
        
        arr[low] = temp;   // 插入元素
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
    
    BinaryInsertSort(arr, n);
    
    cout << "排序后: ";
    PrintArray(arr, n);
    
    return 0;
}
```

**排序过程演示**（将25插入已排序部分 [34, 64]）：
```
已排序: [34, 64]
待插入: 25

折半查找过程:
  low=0, high=1, mid=0 → arr[0]=34 > 25 → high=mid-1=-1
  low=0, high=-1 → 退出循环，插入位置=0

后移并插入:
  [34, 64] → [34, 34, 64] → [25, 34, 64]
```

**注意与折半查找的区别**：
- 折半查找：在**整个有序数组**中找目标，返回"是否存在"
- 对半插入排序中的查找：在**已排序前缀**中找插入位置，找到第一个比它小的位置之后

**时间复杂度分析**：
| 步骤 | 直接插入排序 | 对半插入排序 |
|------|-------------|-------------|
| 比较次数 | O(n²)（最坏） | O(n log n)（查找）+ O(n²)（移动）|
| 移动次数 | O(n²) | O(n²)（不变）|
| 总复杂度 | O(n²) | O(n²)（整体仍是）|

**为什么比较次数减少了，整体复杂度还是 O(n²)？**
因为元素**移动**（后移）次数仍然是 O(n²)，这才是瓶颈所在。对半插入排序的优势在于**减少了比较次数**，特别是在数据量较大时。

**什么时候用？**
- 数据量较大、移动操作成本高时（如磁盘排序、大对象排序）
- 对比较操作敏感的场景

### 4.6 排序算法对比

| 算法 | 时间复杂度 | 空间复杂度 | 稳定性 |
|------|------------|------------|--------|
| 直接插入排序 | O(n²) | O(1) | 稳定 |
| 对半插入排序 | O(n²) | O(1) | 稳定 |
| 冒泡排序 | O(n²) | O(1) | 稳定 |
| 选择排序 | O(n²) | O(1) | 不稳定 |
| 快速排序 | O(n log n) | O(log n) | 不稳定 |
| 归并排序 | O(n log n) | O(n) | 稳定 |
| 堆排序 | O(n log n) | O(1) | 不稳定 |

**对于初学者**：重点掌握冒泡、插入、选择三种简单排序，理解排序的思想即可。

---

## 5. 动态内存分配

### 5.1 为什么要动态内存分配？

在介绍动态内存分配之前，我们先回顾一下C++的内存布局：

| 内存区域 | 存储内容 | 特点 |
|----------|----------|------|
| **代码区** | 程序代码 | 只读 |
| **全局数据区** | 全局变量、静态变量 | 程序启动时分配，程序结束时释放 |
| **栈区** | 函数局部变量 | 自动分配、自动释放 |
| **堆区（自由存储区）** | 动态分配的内存 | 手动分配、手动释放 |

**通俗理解**：
- **栈区**就像自助餐厅的餐盘，用完自动归还，大小固定
- **堆区**就像租房子，可以随时申请和退租，大小可以灵活变化

### 5.2 为什么需要动态内存？

```cpp
// 问题：数组大小必须是编译时确定的常量
int arr[100];  // OK，大小固定

// 问题：编译时不知道需要多大
int n;
cin >> n;  // 用户输入大小
int arr[n];  // C++中这是变长数组(VLA)，不是标准用法！

// 解决方案：使用动态内存分配
int n;
cin >> n;
int* arr = new int[n];  // 运行时根据用户输入决定大小
```

**动态内存的优势**：
1. **大小可变**：根据运行时需求分配
2. **生命周期可控**：手动管理内存的创建和销毁
3. **避免浪费**：按需分配，不需要的就不分配

### 5.3 new运算符 - 申请内存

`new` 运算符用于在堆区（自由存储区）申请内存。

**基本语法**：
```cpp
// 分配单个变量
指针变量 = new 类型名;
指针变量 = new 类型名(初始值);

// 分配数组
指针变量 = new 类型名[元素个数];
```

**代码示例**：
```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 动态分配单个int变量（不初始化）
    int* p1 = new int;
    cout << "p1初始值: " << *p1 << endl;  // 未初始化，可能是随机值

    // 2. 动态分配单个int变量（初始化为4）
    int* p2 = new int(4);
    cout << "p2值: " << *p2 << endl;  // 输出: 4

    // 3. 动态分配4个int的数组
    int* p3 = new int[4];
    cout << "p3数组: ";
    for (int i = 0; i < 4; i++) {
        p3[i] = i * 10;  // 初始化数组
        cout << p3[i] << " ";
    }
    cout << endl;

    // 使用完后必须释放内存！
    delete p1;
    delete p2;
    delete[] p3;  // 数组用 delete[]

    return 0;
}
```

**运行结果**：
```
p1初始值: 0（或随机值）
p2值: 4
p3数组: 0 10 20 30
```

### 5.4 delete运算符 - 释放内存

`delete` 运算符用于释放动态分配的内存。

**语法规则**：
| 分配方式 | 释放语法 |
|----------|----------|
| `new 类型名` | `delete 指针` |
| `new 类型名[个数]` | `delete[] 指针` |

**重要原则**：
- **配对使用**：`new` 和 `delete` 配对，`new[]` 和 `delete[]` 配对
- **只释放一次**：同一块内存不能释放两次
- **释放后置空**：释放后将指针设为 `nullptr`，避免空悬指针

### 5.5 内存泄漏 - 最常见的问题

**内存泄漏（Memory Leak）**：申请了内存但忘记释放，导致内存浪费。

```cpp
#include <iostream>
using namespace std;

void badFunction() {
    int* p = new int[100];  // 申请内存
    // ... 使用p做各种操作
    // 忘记释放！函数结束后p被销毁，但内存没释放
}  // 内存泄漏！

void goodFunction() {
    int* p = new int[100];
    // ... 使用p做各种操作
    delete[] p;  // 释放内存
    p = nullptr;  // 置空，避免空悬指针
}  // 安全！

int main() {
    // 模拟内存泄漏
    for (int i = 0; i < 1000000; i++) {
        badFunction();  // 每次调用泄漏 100*4=400字节
    }
    cout << "程序结束，泄漏了约400MB内存！" << endl;
    return 0;
}
```

**内存泄漏的常见原因**：
1. 忘记写 `delete`
2. 指针被重新赋值，原内存无法访问
3. 异常导致 `delete` 未执行

```cpp
// 常见错误：指针被重新赋值
int* p = new int[10];
p = new int[20];  // 错误！第一个数组泄漏了
```

### 5.6 空悬指针 - 危险的错误

**空悬指针（Dangling Pointer）**：指向已被释放的内存的指针。

```cpp
#include <iostream>
using namespace std;

int main() {
    int* p = new int(42);
    cout << "p的值: " << *p << endl;

    delete p;  // 释放内存
    // p现在是指向"空"的指针，称为空悬指针

    // cout << *p << endl;  // 危险！访问已释放的内存

    p = nullptr;  // 安全做法：释放后将指针置空
    // if (p != nullptr) cout << *p << endl;  // 这样更安全

    return 0;
}
```

### 5.7 动态数组 - 实战应用

**场景**：读取用户输入数量的成绩并计算平均分。

```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cout << "请输入学生人数: ";
    cin >> n;

    // 动态分配数组，大小由运行时决定
    double* scores = new double[n];

    // 输入成绩
    for (int i = 0; i < n; i++) {
        cout << "第" << i + 1 << "个学生成绩: ";
        cin >> scores[i];
    }

    // 计算平均分
    double sum = 0;
    for (int i = 0; i < n; i++) {
        sum += scores[i];
    }
    cout << "平均分: " << sum / n << endl;

    // 重要：释放内存！
    delete[] scores;
    scores = nullptr;

    return 0;
}
```

**运行示例**：
```
请输入学生人数: 5
第1个学生成绩: 85
第2个学生成绩: 92
第3个学生成绩: 78
第4个学生成绩: 88
第5个学生成绩: 95
平均分: 87.6
```

### 5.8 二维动态数组

**分配和释放方法**：

```cpp
#include <iostream>
using namespace std;

int main() {
    int m, n;
    cout << "请输入矩阵行数和列数: ";
    cin >> m >> n;

    // 第一步：分配指向每一行的指针数组
    double** matrix = new double*[m];

    // 第二步：为每一行分配列
    for (int i = 0; i < m; i++) {
        matrix[i] = new double[n];
    }

    // 使用矩阵
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            matrix[i][j] = i * n + j;
        }
    }

    // 打印矩阵
    cout << "矩阵内容:" << endl;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            cout << matrix[i][j] << "\t";
        }
        cout << endl;
    }

    // 释放：先释放每一行
    for (int i = 0; i < m; i++) {
        delete[] matrix[i];
    }
    // 再释放指针数组本身
    delete[] matrix;
    matrix = nullptr;

    return 0;
}
```

**内存分配图解**：
```
matrix (二级指针)
    │
    ├── matrix[0] ──> [col0] [col1] [col2] ... (new double[n])
    ├── matrix[1] ──> [col0] [col1] [col2] ... (new double[n])
    ├── matrix[2] ──> [col0] [col1] [col2] ... (new double[n])
    ...
```

### 5.9 动态内存分配与构造函数/析构函数

对于类的动态分配，会自动调用构造函数和析构函数：

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Student {
private:
    char* name;
    int age;

public:
    // 构造函数
    Student(const char* n, int a) {
        age = a;
        name = new char[strlen(n) + 1];  // 动态分配name
        strcpy(name, n);
        cout << "构造函数: 创建学生 " << name << endl;
    }

    // 析构函数
    ~Student() {
        cout << "析构函数: 释放学生 " << name << endl;
        delete[] name;  // 释放动态分配的内存
        name = nullptr;
    }

    void show() {
        cout << "姓名: " << name << ", 年龄: " << age << endl;
    }
};

int main() {
    // 动态创建对象
    Student* s1 = new Student("张三", 20);
    Student* s2 = new Student("李四", 21);

    s1->show();
    s2->show();

    // 动态销毁对象
    delete s1;
    delete s2;

    return 0;
}
```

**运行结果**：
```
构造函数: 创建学生 张三
构造函数: 创建学生 李四
姓名: 张三, 年龄: 20
姓名: 李四, 年龄: 21
析构函数: 释放学生 张三
析构函数: 释放学生 李四
```

### 5.10 常见错误与注意事项

| 错误类型 | 示例 | 后果 |
|----------|------|------|
| **忘记delete** | `int* p = new int[100];` | 内存泄漏 |
| **重复delete** | `delete p; delete p;` | 程序崩溃 |
| **数组用delete** | `int* p = new int[10]; delete p;` | 未定义行为 |
| **空悬指针** | `delete p; cout << *p;` | 访问已释放内存 |
| **指针赋值覆盖** | `p = new int[10]; p = new int[20];` | 第一个数组泄漏 |

**最佳实践**：
```cpp
// 推荐写法
#include <memory>  // C++智能指针

int* p = new int[100];
// ... 使用p
delete[] p;
p = nullptr;  // 置空

// 更推荐：C++11智能指针（自动管理内存）
#include <memory>
auto p = make_unique<int[]>(100);  // 自动释放
// 不需要手动delete
```

---

## 6. 综合应用示例

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

## 7. 常见问题与优化

### 7.1 选择合适的数据结构

| 场景 | 推荐结构 |
|------|----------|
| 需要快速随机访问 | 数组/顺序表 |
| 频繁插入删除 | 链表 |
| 需要保持有序 | 有序数组 |
| 需要快速查找 | 二叉搜索树/哈希表 |

### 7.2 排序算法选择建议

| 数据规模 | 推荐算法 |
|----------|----------|
| n ≤ 100 | 插入排序、选择排序 |
| n 较大 | 快速排序、归并排序 |
| 有特殊要求 | 堆排序（空间受限） |

### 7.3 时间复杂度速查表

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
