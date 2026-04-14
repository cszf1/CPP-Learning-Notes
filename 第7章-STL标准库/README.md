# 第7章 STL标准库

> ⭐ **重点章节** - C++程序员必须掌握的工具库

---

## 📋 目录

1. [STL概述](#1-stl概述)
2. [string类](#2-string类)
3. [容器详解](#3-容器详解)
   - [vector向量](#31-vector向量)
   - [list链表](#32-list链表)
   - [deque双端队列](#33-deque双端队列)
   - [set与multiset](#34-set与multiset)
   - [map与multimap](#35-map与multimap)
4. [迭代器](#4-迭代器)
5. [算法](#5-算法)

---

## 1. STL概述

### 1.1 什么是STL？

**STL = Standard Template Library（标准模板库）**

> 想象你要装修房子：
> - **容器** = 各种家具（衣柜、书架、鞋柜...）
> - **迭代器** = 家具的把手/开关
> - **算法** = 装修工具（锤子、螺丝刀...）
> - **分配器** = 物流/搬运工

STL是C++标准库的一部分，提供了通用的数据结构和算法，让我们不用重复造轮子。

### 1.2 STL的六大组件

```
┌─────────────────────────────────────────────────────────────────┐
│                          STL 六大组件                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐                  │
│   │  容器     │    │  迭代器   │    │   算法    │                  │
│   │ Container│    │ Iterator │    │  Algorithm│                  │
│   │ 存储数据  │◄──►│ 遍历数据  │◄──►│ 操作数据  │                  │
│   └──────────┘    └──────────┘    └──────────┘                  │
│                                                                  │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐                  │
│   │  适配器   │    │  分配器   │    │  函数对象 │                  │
│   │ Adaptor  │    │ Allocator│    │ Functor  │                  │
│   │ 接口转换  │    │ 内存管理  │    │ 行为封装  │                  │
│   └──────────┘    └──────────┘    └──────────┘                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 1.3 为什么要学习STL？

**不用STL的代码**：
```cpp
// 自己实现动态数组
class MyVector {
    int* data;
    size_t size, capacity;
public:
    void push_back(int x) { /* 手动扩容逻辑 */ }
    void erase(size_t i) { /* 手动移动元素 */ }
    // ...几百行代码
};
```

**使用STL的代码**：
```cpp
#include <vector>
std::vector<int> v;
v.push_back(10);  // 一行搞定
v.erase(v.begin() + 2);  // 一行搞定
```

**好处**：
- ✅ 代码简洁易读
- ✅ 经过严格测试，稳定可靠
- ✅ 性能经过优化
- ✅ 面试必问！

### 1.4 STL头文件一览

| 功能 | 头文件 |
|:---|:---|
| 通用容器 | `<vector>`, `<list>`, `<deque>`, `<queue>`, `<stack>` |
| 关联容器 | `<set>`, `<map>`, `<unordered_set>`, `<unordered_map>` |
| 迭代器 | `<iterator>` |
| 算法 | `<algorithm>` |
| 字符串 | `<string>` |
| 数值 | `<numeric>` |

---

## 2. string类

### 2.1 为什么需要string？

C语言的字符数组很麻烦：
```cpp
// C风格 - 麻烦！
char name[100] = "Hello";
strcat(name, " World");  // 要小心缓冲区
printf("%s", name);

// C++风格 - 简单！
string name = "Hello";
name += " World";  // 自动处理
cout << name;
```

### 2.2 string的基本操作

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 1. 构造方式
    string s1;                    // 空字符串
    string s2("Hello");          // C风格字符串构造
    string s3 = "World";
    string s4(s2);               // 拷贝构造
    string s5(5, 'A');           // "AAAAA"
    string s6(s2, 1, 3);          // "ell"（从位置1开始，长度3）
    
    // 2. 赋值操作
    s1 = "Hello";
    s1 = s2;                      // 用另一个string赋值
    
    // 3. 连接操作
    s1 = "Hello";
    s1 += " World";               // s1 = "Hello World"
    s1.append("!");              // s1 = "Hello World!"
    
    // 4. 访问字符
    s1 = "Hello";
    char c = s1[0];               // 'H'
    char c2 = s1.at(1);           // 'e'（带边界检查）
    
    // 5. 长度相关
    cout << s1.length() << endl;   // 5
    cout << s1.size() << endl;    // 5（与length等价）
    cout << s1.capacity() << endl;// 当前容量
    cout << s1.empty() << endl;   // false
    
    // 6. 截取子串
    s1 = "Hello World";
    string sub1 = s1.substr(0, 5);    // "Hello"
    string sub2 = s1.substr(6);      // "World"
    
    // 7. 查找
    s1 = "Hello World";
    cout << s1.find("World") << endl;  // 6（首次出现位置）
    cout << s1.find("C++") << endl;     // string::npos（未找到）
    cout << s1.rfind("o") << endl;     // 7（反向查找）
    
    // 8. 替换
    s1 = "Hello World";
    s1.replace(6, 5, "C++");         // "Hello C++"
    
    // 9. 插入删除
    s1 = "Hello";
    s1.insert(5, " World");          // "Hello World"
    s1.erase(5, 6);                  // "Hello"
    
    // 10. 字符串比较
    string a = "Apple";
    string b = "Banana";
    cout << (a < b) << endl;        // true（字典序）
    
    // 11. 输入输出
    string input;
    cin >> input;                    // 读取一个单词（遇空格停止）
    getline(cin, input);             // 读取一整行
    
    return 0;
}
```

### 2.3 string与C字符串转换

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // string → const char*
    string s = "Hello";
    const char* cstr = s.c_str();    // 返回C风格字符串
    const char* cstr2 = s.data();    // 同样返回C风格字符串
    
    // const char* → string（直接赋值即可）
    const char* c = "World";
    string s2 = c;                   // 自动转换
    
    // string → char[]
    string s3 = "Hello";
    char arr[100];
    strcpy(arr, s3.c_str());          // 需要复制到数组
    
    return 0;
}
```

### 2.4 💡 通俗理解

> string就像一条可伸缩的传送带：
> - 不用预先告诉它多长，它会自动调整
> - 想加字符就加（`+=`），会自动变长
> - 想取字符就取（`[]`），会自动检查边界
> - 想找字符就找（`find`），会告诉你位置

### 2.5 常见错误

```cpp
// ❌ 错误1：字符串字面量类型搞混
string s = "Hello";          // 正确：const char[6]
char* p = "Hello";           // ❌ 危险！字符串字面量不能赋值给char*

// ❌ 错误2：下标越界不检查
string s = "Hi";
char c = s[100];             // ❌ 危险！未定义行为
char c2 = s.at(100);         // ✅ 会抛出异常

// ❌ 错误3：中文字符串长度问题
string zh = "你好";
cout << zh.length() << endl;  // 6（UTF-8编码），不是2！
```

### 2.6 练习

```cpp
// 练习1：统计字符串中单词数量
string text = "Hello world C++ programming";
// 提示：按空格分割
// 预期结果：4

// 练习2：反转字符串
string s = "abcdef";
reverse(s.begin(), s.end());  // "fedcba"

// 练习3：判断回文
string t = "上海自来水来自海上";
// 提示：去掉空格后正反读相同
```

---

## 3. 容器详解

### 3.1 vector向量

#### 3.1.1 概念

> vector就像**动态数组**，可以自动扩容，尾部操作效率高。

```
┌─────────────────────────────────────────────────────────┐
│                      vector 示意图                        │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   ┌───┬───┬───┬───┬───┬───┬───┐                         │
│   │ 1 │ 3 │ 5 │ 7 │ 9 │11 │13 │  ← 连续内存存储          │
│   └───┴───┴───┴───┴───┴───┴───┘                         │
│     │                           │                        │
│   front()                    back()                      │
│                             push_back() 从这加           │
│                                                          │
│   特点：                                                   │
│   ✅ 随机访问快 O(1)                                     │
│   ✅ 尾部插入/删除快 O(1)                                │
│   ❌ 中间插入/删除慢 O(n)                                │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### 3.1.2 基本操作

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 1. 构造
    vector<int> v1;                 // 空vector
    vector<int> v2(5);              // 5个元素，默认值为0
    vector<int> v3(5, 10);          // 5个元素，值都是10
    vector<int> v4 = {1, 2, 3, 4, 5};  // 列表初始化
    
    // 2. 尾部操作
    v1.push_back(10);               // 添加元素
    v1.push_back(20);
    v1.push_back(30);               // v1 = {10, 20, 30}
    
    int last = v1.back();           // 30（最后一个元素）
    v1.pop_back();                  // 删除最后一个，v1 = {10, 20}
    
    // 3. 访问元素
    vector<int> v = {10, 20, 30};
    cout << v[0] << endl;           // 10（下标访问，不检查边界）
    cout << v.at(1) << endl;        // 20（at()会检查边界）
    cout << v.front() << endl;      // 10（第一个元素）
    cout << v.back() << endl;       // 30（最后一个元素）
    
    // 4. 容量相关
    vector<int> v5;
    cout << v5.empty() << endl;    // true（是否为空）
    cout << v5.size() << endl;      // 0（元素个数）
    v5.resize(5);                   // 调整为5个元素，多的删掉，少的补0
    v5.resize(10, 99);             // 调整为10个，不足的补99
    v5.clear();                     // 清空所有元素（但容量保留）
    v5.shrink_to_fit();             // 释放多余容量
    
    // 5. 判断容量
    vector<int> v6 = {1, 2, 3};
    v6.reserve(100);                // 预先分配100个元素的空间
    // 注意：reserve只管容量，不管大小
    
    // 6. 迭代器
    vector<int>::iterator it = v.begin();
    cout << *it << endl;            // 1（第一个元素）
    ++it;
    cout << *it << endl;            // 2
    
    // 7. 遍历方式
    vector<int> v7 = {1, 2, 3, 4, 5};
    
    // 方式1：下标
    for (size_t i = 0; i < v7.size(); ++i) {
        cout << v7[i] << " ";
    }
    cout << endl;
    
    // 方式2：迭代器
    for (auto it = v7.begin(); it != v7.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // 方式3：范围for（C++11）
    for (int x : v7) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

#### 3.1.3 插入与删除

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    
    // insert：在指定位置插入
    v.insert(v.begin() + 2, 99);    // 在位置2插入99
    // v = {1, 2, 99, 3, 4, 5}
    
    v.insert(v.end(), 100);        // 在末尾插入100
    // v = {1, 2, 99, 3, 4, 5, 100}
    
    // erase：删除指定位置
    v.erase(v.begin() + 2);        // 删除位置2的元素
    // v = {1, 2, 3, 4, 5, 100}
    
    v.erase(v.begin(), v.begin() + 3);  // 删除[0,3)区间
    // v = {4, 5, 100}
    
    // 删除所有值为x的元素
    auto removeValue = [](vector<int>& vec, int val) {
        vec.erase(remove(vec.begin(), vec.end(), val), vec.end());
    };
    
    return 0;
}
```

#### 3.1.4 二维vector

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 3行4列的二维矩阵
    vector<vector<int>> matrix(3, vector<int>(4, 0));
    
    // 赋值
    for (int i = 0; i < 3; ++i) {
        for (int j = 0; j < 4; ++j) {
            matrix[i][j] = i * 4 + j + 1;
        }
    }
    
    // 打印
    for (int i = 0; i < matrix.size(); ++i) {
        for (int j = 0; j < matrix[i].size(); ++j) {
            cout << matrix[i][j] << "\t";
        }
        cout << endl;
    }
    
    // 不规则二维数组（每行长度不同）
    vector<vector<int>> jagged = {
        {1, 2},
        {3, 4, 5, 6},
        {7, 8, 9}
    };
    
    return 0;
}
```

#### 3.1.5 常见错误

```cpp
// ❌ 错误1：迭代器失效
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin() + 2;    // 指向3
v.push_back(6);             // ⚠️ 可能导致迭代器失效！
cout << *it << endl;        // ❌ 未定义行为

// ✅ 正确做法：insert/erase后重新获取迭代器
vector<int> v2 = {1, 2, 3, 4, 5};
auto it2 = v2.begin() + 2;
v2.push_back(6);
it2 = v2.begin() + 2;       // 重新获取
cout << *it2 << endl;       // ✅ 正确

// ❌ 错误2：访问越界
vector<int> v3 = {1, 2, 3};
cout << v3[10] << endl;     // ❌ 未定义行为（可能崩溃）
cout << v3.at(10) << endl;  // ✅ 抛出out_of_range异常

// ❌ 错误3：空容器调用front/back
vector<int> v4;
cout << v4.front() << endl; // ❌ 未定义行为！
if (!v4.empty()) {
    cout << v4.front() << endl;  // ✅ 先检查
}
```

#### 3.1.6 练习

```cpp
// 练习1：移除vector中的重复元素
vector<int> nums = {1, 2, 2, 3, 3, 3, 4, 5, 5};
// 预期结果：{1, 2, 3, 4, 5}

// 练习2：实现两个有序vector合并
vector<int> a = {1, 3, 5, 7};
vector<int> b = {2, 4, 6, 8};
// 预期结果：{1, 2, 3, 4, 5, 6, 7, 8}

// 练习3：找出vector中的第二大数
vector<int> v = {5, 1, 4, 2, 3};
// 预期结果：4
```

---

### 3.2 list链表

#### 3.2.1 概念

> list就像**火车车厢**，每节车厢独立，但用钩子相连。

```
┌─────────────────────────────────────────────────────────┐
│                      list 示意图                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│    ┌───┐    ┌───┐    ┌───┐    ┌───┐                     │
│    │ 1 │◄──►│ 2 │◄──►│ 3 │◄──►│ 4 │                     │
│    └───┘    └───┘    └───┘    └───┘                     │
│      ↑                                      ↑             │
│    head                                tail (end)          │
│                                                          │
│   特点：                                                   │
│   ✅ 任意位置插入/删除快 O(1)                             │
│   ❌ 随机访问慢 O(n)                                      │
│   ❌ 不保证内存连续                                        │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### 3.2.2 基本操作

```cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
    // 1. 构造
    list<int> l1;                        // 空链表
    list<int> l2(5);                    // 5个节点，值为0
    list<int> l3(5, 10);                // 5个节点，值为10
    list<int> l4 = {1, 2, 3, 4, 5};     // 列表初始化
    
    // 2. 头部/尾部操作
    l1.push_front(10);                   // 头部插入
    l1.push_back(20);                   // 尾部插入
    l1.pop_front();                     // 头部删除
    l1.pop_back();                      // 尾部删除
    
    // 3. 访问（不能使用下标！）
    cout << l4.front() << endl;         // 1
    cout << l4.back() << endl;          // 5
    
    // 4. 迭代器（双向）
    list<int> l = {1, 2, 3, 4, 5};
    
    // 不能+n或-n，但可以++和--
    auto it = l.begin();
    ++it;                               // 前进
    --it;                               // 后退
    // it + 2;                          // ❌ 错误！list的迭代器不支持算术运算
    
    // 5. 插入与删除（list的优势）
    l.insert(l.begin(), 0);             // O(1)，头部插入
    l.erase(l.begin());                 // O(1)，头部删除
    
    // 6. splice：合并链表（list特有）
    list<int> l5 = {1, 2, 3};
    list<int> l6 = {4, 5, 6};
    l5.splice(l5.end(), l6);            // 把l6接到l5后面
    // l5 = {1, 2, 3, 4, 5, 6}
    // l6变为空
    
    // 7. remove：删除特定值
    list<int> l7 = {1, 2, 3, 2, 4, 2};
    l7.remove(2);                      // 删除所有值为2的节点
    // l7 = {1, 3, 4}
    
    // 8. unique：删除相邻重复元素
    list<int> l8 = {1, 1, 2, 2, 2, 3, 3};
    l8.unique();                        // 删除相邻重复
    // l8 = {1, 2, 3}
    
    // 9. sort：排序
    list<int> l9 = {3, 1, 4, 1, 5, 9, 2, 6};
    l9.sort();                          // 升序
    // l9 = {1, 1, 2, 3, 4, 5, 6, 9}
    
    // 10. reverse：反转
    l9.reverse();
    // l9 = {9, 6, 5, 4, 3, 2, 1, 1}
    
    // 11. merge：合并两个有序链表
    list<int> l10 = {1, 3, 5};
    list<int> l11 = {2, 4, 6};
    l10.merge(l11);                     // l11接到l10并保持有序
    // l10 = {1, 2, 3, 4, 5, 6}
    // l11变为空
    
    return 0;
}
```

#### 3.2.3 vector vs list 何时选择

| 操作 | vector | list |
|:---|:---:|:---:|
| 随机访问 `v[i]` | O(1) ✅ | O(n) ❌ |
| 尾部插入 `push_back()` | O(1) ✅ | O(1) ✅ |
| 中间插入/删除 | O(n) ❌ | O(1) ✅ |
| 内存占用 | 低（连续） | 高（额外指针） |
| CPU缓存 | 友好 ✅ | 不友好 ❌ |

**选择建议**：
- 需要频繁随机访问 → **vector**
- 需要频繁中间插入/删除 → **list**
- 不确定时，默认选 **vector**（缓存友好，性能通常更好）

#### 3.2.4 练习

```cpp
// 练习1：删除链表中的所有偶数
list<int> l = {1, 2, 3, 4, 5, 6, 7, 8, 9};
// 预期结果：1 3 5 7 9

// 练习2：实现冒泡排序（list版）
list<int> l2 = {64, 34, 25, 12, 22, 11, 90};
// 提示：list的迭代器是双向的，支持++和--

// 练习3：约瑟夫问题
// n个人围成一圈，从第k个人开始报数，数到m的人出圈
// 求最后剩下的人编号
```

---

### 3.3 deque双端队列

#### 3.3.1 概念

> deque就像**地铁车厢**，两端都可以进出，中间访问也快。

```
┌─────────────────────────────────────────────────────────┐
│                      deque 示意图                        │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   push_front()    []访问    push_back()                 │
│        ↓            ↓           ↓                        │
│    ┌───┬───┬───┬───┬───┬───┬───┬───┐                   │
│    │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │                     │
│    └───┴───┴───┴───┴───┴───┴───┴───┘                    │
│                                                          │
│   特点：                                                   │
│   ✅ 两端操作都快 O(1)                                    │
│   ✅ 随机访问 O(1)                                       │
│   ❌ 中间插入/删除较慢 O(n)                               │
│   ❌ 内存不连续（但比list好）                             │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### 3.3.2 基本操作

```cpp
#include <iostream>
#include <deque>
using namespace std;

int main() {
    // 1. 构造（与vector类似）
    deque<int> d1;
    deque<int> d2(5);
    deque<int> d3(5, 10);
    deque<int> d4 = {1, 2, 3, 4, 5};
    
    // 2. 两端操作
    d1.push_front(10);                  // 头部插入
    d1.push_back(20);                  // 尾部插入
    d1.pop_front();                    // 头部删除
    d1.pop_back();                    // 尾部删除
    
    // 3. 访问（支持下标！这是deque比list强的地方）
    deque<int> d = {10, 20, 30, 40, 50};
    cout << d[0] << endl;              // 10
    cout << d[2] << endl;              // 30
    cout << d.at(4) << endl;           // 50
    
    // 4. insert和erase（比vector稍慢，但比list快）
    d.insert(d.begin() + 2, 99);
    d.erase(d.begin() + 3);
    
    // 5. 典型应用：实现滑动窗口
    deque<int> window;
    int k = 3;  // 窗口大小
    vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    
    for (int i = 0; i < nums.size(); ++i) {
        // 窗口右移
        window.push_back(nums[i]);
        
        // 窗口满了，输出最大值
        if (window.size() >= k) {
            int maxVal = *max_element(window.begin(), window.end());
            cout << maxVal << " ";
            // 窗口左移
            window.pop_front();
        }
    }
    
    return 0;
}
```

#### 3.3.3 deque vs vector vs list

| 特性 | deque | vector | list |
|:---|:---:|:---:|:---:|
| 随机访问 | O(1) ✅ | O(1) ✅ | O(n) ❌ |
| 头部插入 | O(1) ✅ | O(n) ❌ | O(1) ✅ |
| 尾部插入 | O(1) ✅ | O(1) ✅ | O(1) ✅ |
| 中间插入 | O(n) | O(n) | O(1) |
| 内存连续 | 部分 ✅ | 完全 ✅ | ❌ |
| stack/queue底层 | - | - | - |

---

### 3.4 set与multiset

#### 3.4.1 概念

> set就像**宝石头盒**，每颗宝石都独一无二，按价值自动排序。

```
┌─────────────────────────────────────────────────────────┐
│                       set 示意图                         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   ┌───┐                                                 │
│   │ 3 │  ← 自动去重 + 自动排序                          │
│   ├─●─┤     （默认升序）                                 │
│   │ 5 │                                                 │
│   ├─●─┤                                                 │
│   │ 7 │                                                 │
│   └─●─┘                                                 │
│                                                          │
│   set<int> s = {5, 3, 7, 1, 9};                         │
│   // 实际存储：{1, 3, 5, 7, 9}                          │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### 3.4.2 基本操作

```cpp
#include <iostream>
#include <set>
using namespace std;

int main() {
    // 1. 构造
    set<int> s1;                           // 空集合
    set<int> s2 = {3, 1, 4, 1, 5, 9, 2};  // 自动去重排序
    // s2 = {1, 2, 3, 4, 5, 9}
    
    multiset<int> ms = {3, 1, 4, 1, 5};   // multiset允许重复
    // ms = {1, 1, 3, 4, 5}
    
    // 2. 插入
    s1.insert(5);
    s1.insert(3);
    s1.insert(5);                         // 重复值会被忽略（set）
    // s1 = {3, 5}
    
    ms.insert(3);                         // 重复值会被添加（multiset）
    // ms = {1, 1, 3, 3, 4, 5}
    
    // 3. 删除
    s1.erase(3);                          // 删除值为3的元素
    // s1 = {5}
    
    auto it = s1.find(5);                  // 查找
    if (it != s1.end()) {
        s1.erase(it);                      // 用迭代器删除
    }
    
    // 4. 查找
    set<int> s3 = {1, 3, 5, 7, 9};
    
    auto pos = s3.find(5);
    if (pos != s3.end()) {
        cout << "找到：" << *pos << endl;  // 5
    }
    
    // 5. 计数
    cout << s3.count(5) << endl;           // 1（存在）
    cout << s3.count(6) << endl;          // 0（不存在）
    
    // 6. 上下界
    set<int> s4 = {1, 3, 5, 7, 9};
    
    auto lower = s4.lower_bound(5);        // >= 5 的第一个
    // 指向5
    
    auto upper = s4.upper_bound(5);        // > 5 的第一个
    // 指向7
    
    // 7. 遍历
    cout << "s4内容：";
    for (int x : s4) {
        cout << x << " ";
    }
    cout << endl;
    
    // 8. 范围删除
    s4.erase(s4.lower_bound(3), s4.upper_bound(7));
    // 删除[3,7)区间，s4 = {1, 9}
    
    return 0;
}
```

#### 3.4.3 自定义比较函数

```cpp
#include <iostream>
#include <set>
using namespace std;

// 方法1：仿函数（函数对象）
class Student {
public:
    string name;
    int age;
    Student(string n, int a) : name(n), age(a) {}
};

// 降序比较器
class cmp {
public:
    bool operator()(const Student& a, const Student& b) const {
        return a.age > b.age;  // 按年龄降序
    }
};

int main() {
    // set自定义排序
    set<int, greater<int>> s1 = {5, 3, 7, 1, 9};  // 降序
    // s1 = {9, 7, 5, 3, 1}
    
    // 结构体set
    set<Student, cmp> students = {
        {"Alice", 18},
        {"Bob", 20},
        {"Charlie", 19}
    };
    // 按年龄降序：Bob(20), Charlie(19), Alice(18)
    
    // Lambda表达式（C++14+）
    auto cmp_lambda = [](const Student& a, const Student& b) {
        return a.name < b.name;  // 按名字升序
    };
    set<Student, decltype(cmp_lambda)> students2(cmp_lambda);
    
    return 0;
}
```

#### 3.4.4 常见错误

```cpp
// ❌ 错误1：修改set中的元素
set<int> s = {1, 2, 3};
// *s.begin() = 10;              // ❌ 编译错误！set元素是const

// ❌ 错误2：迭代器遍历时删除元素
set<int> s2 = {1, 2, 3, 4, 5};
for (auto it = s2.begin(); it != s2.end(); ++it) {
    if (*it == 3) {
        s2.erase(it);           // ⚠️ erase后迭代器失效
    }
}

// ✅ 正确做法：获取下一个迭代器后再删除
for (auto it = s2.begin(); it != s2.end(); ) {
    if (*it == 3) {
        it = s2.erase(it);      // erase返回下一个迭代器
    } else {
        ++it;
    }
}
```

#### 3.4.5 练习

```cpp
// 练习1：统计不重复的单词数量
vector<string> words = {"apple", "banana", "apple", "orange", "banana"};
set<string> uniqueWords(words.begin(), words.end());
cout << uniqueWords.size() << endl;  // 3

// 练习2：查找第一个大于x的元素
set<int> s = {1, 3, 5, 7, 9};
int x = 4;
auto it = s.upper_bound(x);
if (it != s.end()) {
    cout << "第一个大于4的数：" << *it << endl;  // 5
}

// 练习3：multiset统计出现次数
multiset<int> ms = {1, 2, 2, 2, 3, 3, 4};
cout << ms.count(2) << endl;  // 3
cout << ms.count(5) << endl; // 0
```

---

### 3.5 map与multimap

#### 3.5.1 概念

> map就像**字典**，每个单词（key）对应一个解释（value），自动按单词排序。

```
┌─────────────────────────────────────────────────────────┐
│                       map 示意图                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   ┌────────┬────────┐                                   │
│   │  key   │  value │                                   │
│   ├────────┼────────┤                                   │
│   │  "a"   │  "啊"  │                                   │
│   ├────────┼────────┤                                   │
│   │  "b"   │ "波"   │                                   │
│   ├────────┼────────┤                                   │
│   │  "c"   │ "次"   │                                   │
│   └────────┴────────┘                                   │
│                                                          │
│   map<string, string> dict = {                           │
│       {"apple", "苹果"},                                 │
│       {"banana", "香蕉"},                               │
│       {"cherry", "樱桃"}                                │
│   };                                                    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### 3.5.2 基本操作

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    // 1. 构造
    map<string, int> m1;                          // 空map
    map<string, int> m2 = {
        {"apple", 1},
        {"banana", 2},
        {"cherry", 3}
    };
    
    // 2. 插入
    m1["one"] = 1;                                 // 用[]插入（常用）
    m1.insert(pair<string, int>("two", 2));       // 用insert
    m1.insert(make_pair("three", 3));
    m1.insert({"four", 4});                        // C++11列表插入
    
    // [] vs insert 的区别
    map<string, int> m3;
    m3["key"] = 10;                               // 如果key存在，修改value
    m3["key"] = 20;                               // key="key"的value变成20
    
    m3.insert({"key", 30});                        // 如果key存在，不修改
    cout << m3["key"] << endl;                    // 仍然是20
    
    // 3. 访问
    map<string, int> m4 = {{"a", 1}, {"b", 2}};
    cout << m4["a"] << endl;                      // 1（不安全，key不存在会创建）
    cout << m4.at("a") << endl;                   // 1（安全，key不存在抛异常）
    
    // 4. 查找
    auto pos = m4.find("a");
    if (pos != m4.end()) {
        cout << "找到：" << pos->first << " = " << pos->second << endl;
    }
    
    // 5. 删除
    m4.erase("a");                                 // 按key删除
    m4.erase(m4.begin());                        // 按迭代器删除
    
    // 6. 遍历
    map<string, int> m5 = {{"z", 26}, {"a", 1}, {"m", 13}};
    cout << "\n遍历map：";
    for (auto& p : m5) {
        cout << p.first << ":" << p.second << " ";
    }
    // 输出：a:1 m:13 z:26（按键升序）
    
    // 7. 统计
    cout << m5.count("a") << endl;                 // 1（存在）
    cout << m5.count("x") << endl;                 // 0（不存在）
    
    // 8. lower_bound / upper_bound
    auto lb = m5.lower_bound("m");
    auto ub = m5.upper_bound("m");
    
    return 0;
}
```

#### 3.5.3 map统计词频

```cpp
#include <iostream>
#include <map>
#include <string>
#include <sstream>
using namespace std;

// 统计单词出现次数
int main() {
    string text = "apple banana apple orange banana apple";
    istringstream iss(text);
    string word;
    
    map<string, int> wordCount;
    while (iss >> word) {
        wordCount[word]++;                         // 自动计数
    }
    
    // 输出结果
    cout << "词频统计：" << endl;
    for (auto& p : wordCount) {
        cout << p.first << ": " << p.second << endl;
    }
    
    return 0;
}
/*
输出：
apple: 3
banana: 2
orange: 1
*/
```

#### 3.5.4 multimap一对多

```cpp
#include <iostream>
#include <map>
using namespace std;

int main() {
    // multimap：一本书的目录（一个章节可能有多页）
    multimap<string, int> bookIndex;
    
    bookIndex.insert({"Chapter 1", 1});
    bookIndex.insert({"Chapter 1", 5});
    bookIndex.insert({"Chapter 1", 10});
    bookIndex.insert({"Chapter 2", 15});
    bookIndex.insert({"Chapter 2", 20));
    
    // 查找Chapter 1的所有页码
    string target = "Chapter 1";
    auto range = bookIndex.equal_range(target);
    
    cout << target << " 出现在以下页面：" << endl;
    for (auto it = range.first; it != range.second; ++it) {
        cout << "第 " << it->second << " 页" << endl;
    }
    
    // ⚠️ 注意：multimap不支持operator[]
    // bookIndex["Chapter 1"]  // ❌ 错误！
    
    return 0;
}
```

#### 3.5.5 常见错误

```cpp
// ❌ 错误1：用[]访问不存在的key
map<string, int> m;
int val = m["nonexistent"];  // ⚠️ 会创建一个value为0的条目！

// ✅ 正确做法1：用find
auto it = m.find("nonexistent");
if (it != m.end()) {
    cout << it->second << endl;
}

// ✅ 正确做法2：用at（推荐）
try {
    cout << m.at("nonexistent") << endl;
} catch (out_of_range& e) {
    cout << "key不存在" << endl;
}

// ❌ 错误2：map的key不能修改
map<int, string> m2;
m2[1] = "one";
// m2.begin()->first = 10;  // ❌ 编译错误！key是const

// ❌ 错误3：map的迭代器遍历时删除
for (auto it = m2.begin(); it != m2.end(); ++it) {
    if (it->second == "one") {
        m2.erase(it);              // ⚠️ 迭代器失效
    }
}

// ✅ 正确做法
for (auto it = m2.begin(); it != m2.end(); ) {
    if (it->second == "one") {
        it = m2.erase(it);         // erase返回下一个迭代器
    } else {
        ++it;
    }
}
```

#### 3.5.6 练习

```cpp
// 练习1：统计成绩
map<string, vector<int>> studentScores;
studentScores["Alice"] = {90, 85, 92};
studentScores["Bob"] = {78, 88, 95};
// 计算每个人的平均分

// 练习2：反转map（值→键）
map<int, string> idName = {{1, "Alice"}, {2, "Bob"}, {3, "Charlie"}};
// 实现：如果有重复值，需要特殊处理

// 练习3：实现简易通讯录
map<string, string> contacts;
contacts["Alice"] = "123456";
contacts["Bob"] = "654321";
// 实现增删改查功能
```

---

## 4. 迭代器

### 4.1 迭代器是什么？

> **迭代器就像遥控器**，用来遍历容器（电视）而不需要了解内部结构。

```
┌─────────────────────────────────────────────────────────┐
│                     迭代器示意图                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   容器    ┌───┬───┬───┬───┬───┐                          │
│   数据    │ 1 │ 2 │ 3 │ 4 │ 5 │                          │
│           └───┴───┴───┴───┴───┘                          │
│             ↑     ↑     ↑                                 │
│             │     │     │                                │
│   迭代器  begin()  it   end()                             │
│                                                          │
│   ┌──────────────────────────────────────────┐           │
│   │  *it      → 获取当前元素                  │           │
│   │  ++it     → 移动到下一个元素              │           │
│   │  it == end → 是否到达末尾                │           │
│   └──────────────────────────────────────────┘           │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### 4.2 迭代器类型

| 类型 | 支持操作 | 容器示例 |
|:---|:---|:---|
| 输入迭代器 | `*`, `++` | - |
| 输出迭代器 | `*`, `++` | - |
| **前向迭代器** | `*`, `++`, `==`, `!=` | `forward_list` |
| **双向迭代器** | `*`, `++`, `--`, `==`, `!=` | `list`, `set`, `map` |
| **随机访问迭代器** | `*`, `++`, `--`, `+`, `-`, `[]`, `==`, `!=` | `vector`, `deque`, `string` |

### 4.3 迭代器基本操作

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <set>
#include <iterator>
using namespace std;

int main() {
    // 1. 获取迭代器
    vector<int> v = {1, 2, 3, 4, 5};
    
    auto it1 = v.begin();                  // 指向第一个元素
    auto it2 = v.end();                    // 指向"最后一个元素的后面"
    
    // 2. 解引用
    cout << *it1 << endl;                   // 1
    
    // 3. 移动
    ++it1;                                  // 前进一步
    cout << *it1 << endl;                   // 2
    
    // 4. 不同容器的迭代器
    list<int> lst = {1, 2, 3};
    auto lit = lst.begin();
    ++lit;                                  // ✅ 可以++
    --lit;                                 // ✅ 也可以--（双向）
    // lit + 2;                            // ❌ 不能+n（不是随机访问）
    
    set<int> s = {3, 1, 2};
    auto sit = s.begin();
    ++sit;                                  // ✅ 可以++
    // sit + 1;                            // ❌ 不能+n
    
    // 5. vector的随机访问
    vector<int>::iterator vit = v.begin();
    vit += 2;                               // ✅ 前进2步
    cout << vit[0] << endl;                 // ✅ 可以用[]访问
    cout << *(vit + 1) << endl;             // ✅ 可以+n
    
    // 6. 迭代器距离
    vector<int>::iterator a = v.begin();
    vector<int>::iterator b = v.end();
    cout << distance(a, b) << endl;         // 5
    
    // 7. 逆向迭代器
    vector<int> v2 = {1, 2, 3, 4, 5};
    
    cout << "正向遍历：";
    for (auto it = v2.begin(); it != v2.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    cout << "逆向遍历：";
    for (auto rit = v2.rbegin(); rit != v2.rend(); ++rit) {
        cout << *rit << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 4.4 迭代器失效

> ⚠️ **重要！迭代器就像贴在车上的便利贴，车开走了便利贴就失效了**

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 场景1：vector扩容导致迭代器失效
    vector<int> v = {1, 2, 3};
    auto it = v.begin() + 1;               // 指向2
    
    v.push_back(4);                         // ⚠️ 可能扩容
    // it现在指向的位置可能已经被释放！
    
    // ✅ 解决：扩容后重新获取
    it = v.begin() + 1;                     // 重新获取
    cout << *it << endl;                    // 现在安全了
    
    // 场景2：erase导致迭代器失效
    vector<int> v2 = {1, 2, 3, 4, 5};
    for (auto it = v2.begin(); it != v2.end(); ++it) {
        if (*it % 2 == 0) {
            it = v2.erase(it);             // erase返回下一个有效迭代器
        }
    }
    // v2 = {1, 3, 5}
    
    // 场景3：string的迭代器失效
    string s = "hello";
    auto sit = s.begin();
    s += " world";                         // ⚠️ 可能重新分配内存
    // sit失效了！
    
    return 0;
}
```

### 4.5 💡 通俗理解

> **迭代器分类记忆口诀**：
> - **单向链表**：只能往前走（`forward_list`）
> - **双向链表**：可以往前走、往后退（`list`, `set`, `map`）
> - **数组**：可以跳跃（`vector`, `deque`, `string`）
>
> **迭代器失效口诀**：
> - **增删改**：可能导致迭代器失效
> - **修改**：capacity变化时必失效
> - **删除**：删除点之后的迭代器都可能失效

### 4.6 练习

```cpp
// 练习1：逆序打印vector
vector<int> v = {1, 2, 3, 4, 5};
// 使用逆向迭代器输出：5 4 3 2 1

// 练习2：找两个迭代器之间的距离
vector<int> v2 = {10, 20, 30, 40, 50};
auto it1 = v2.begin() + 1;
auto it2 = v2.begin() + 4;
cout << distance(it1, it2) << endl;  // 3

// 练习3：实现二分查找（用迭代器）
vector<int> sorted = {1, 3, 5, 7, 9, 11, 13};
// 提示：middle = first + (last - first) / 2
```

---

## 5. 算法

### 5.1 算法概述

> STL算法就像**工具箱**，里面有很多现成的工具，能帮你完成查找、排序、修改等各种操作。

```
┌─────────────────────────────────────────────────────────┐
│                     STL算法分类                           │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   ┌─────────────────┐    ┌─────────────────┐            │
│   │   非修改序列算法   │    │   修改序列算法   │            │
│   │ for_each         │    │ transform       │            │
│   │ find/find_if     │    │ copy            │            │
│   │ count/count_if   │    │ replace         │            │
│   │ equal            │    │ remove          │            │
│   │ search           │    │ unique          │            │
│   └─────────────────┘    └─────────────────┘            │
│                                                          │
│   ┌─────────────────┐    ┌─────────────────┐            │
│   │    排序算法       │    │   数值算法       │            │
│   │ sort            │    │ accumulate      │            │
│   │ stable_sort     │    │ inner_product   │            │
│   │ partial_sort    │    │ iota            │            │
│   │ nth_element     │    │ adjacent_difference│          │
│   └─────────────────┘    └─────────────────┘            │
│                                                          │
│   ┌─────────────────┐    ┌─────────────────┐            │
│   │   集合算法       │    │   比较算法       │            │
│   │ set_union       │    │ equal           │            │
│   │ set_intersection│    │ mismatch        │            │
│   │ set_difference  │    │ lexicographical_compare │     │
│   └─────────────────┘    └─────────────────┘            │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### 5.2 查找算法

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <list>
#include <set>
using namespace std;

// 辅助函数
void print(const string& label, const vector<int>& v) {
    cout << label << ": ";
    for (int x : v) cout << x << " ";
    cout << endl;
}

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 1. find：查找第一个匹配的元素
    auto it1 = find(v.begin(), v.end(), 5);
    if (it1 != v.end()) {
        cout << "找到5，位置：" << distance(v.begin(), it1) << endl;
    }
    
    // 2. find_if：查找满足条件的第一个元素
    auto it2 = find_if(v.begin(), v.end(), [](int x) {
        return x > 6;
    });
    cout << "第一个大于6的数：" << *it2 << endl;  // 7
    
    // 3. find_if_not：查找不满足条件的第一个元素
    auto it3 = find_if_not(v.begin(), v.end(), [](int x) {
        return x < 5;
    });
    cout << "第一个不小于5的数：" << *it3 << endl;  // 5
    
    // 4. count：统计元素个数
    vector<int> v2 = {1, 2, 2, 3, 2, 4, 2};
    cout << "2出现的次数：" << count(v2.begin(), v2.end(), 2) << endl;  // 4
    
    // 5. count_if：统计满足条件的个数
    int evenCount = count_if(v2.begin(), v2.end(), [](int x) {
        return x % 2 == 0;
    });
    cout << "偶数个数：" << evenCount << endl;  // 4
    
    // 6. binary_search：二分查找（必须有序！）
    vector<int> sorted = {1, 3, 5, 7, 9, 11, 13};
    bool found = binary_search(sorted.begin(), sorted.end(), 7);
    cout << "7是否在集合中：" << (found ? "是" : "否") << endl;  // 是
    
    // 7. lower_bound：找第一个>=目标的位置
    auto lb = lower_bound(sorted.begin(), sorted.end(), 6);
    cout << "第一个>=6的数：" << *lb << endl;  // 7
    
    // 8. upper_bound：找第一个>目标的位置
    auto ub = upper_bound(sorted.begin(), sorted.end(), 6);
    cout << "第一个>6的数：" << *ub << endl;    // 7
    
    // 9. equal_range：同时获取lower和upper
    auto range = equal_range(sorted.begin(), sorted.end(), 5);
    cout << "5的位置范围：" << distance(sorted.begin(), range.first);
    cout << " - " << distance(sorted.begin(), range.second) << endl;
    
    // 10. search：查找子序列
    vector<int> haystack = {1, 2, 3, 4, 5, 3, 4, 5, 6};
    vector<int> needle = {3, 4, 5};
    auto subIt = search(haystack.begin(), haystack.end(), needle.begin(), needle.end());
    if (subIt != haystack.end()) {
        cout << "找到子序列，起点位置：" << distance(haystack.begin(), subIt) << endl;
    }
    
    return 0;
}
```

### 5.3 排序算法

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <list>
using namespace std;

void print(const string& label, const vector<int>& v) {
    cout << label << ": ";
    for (int x : v) cout << x << " ";
    cout << endl;
}

int main() {
    // 1. sort：快速排序（不稳定，但很快）
    vector<int> v1 = {5, 2, 8, 1, 9, 3, 7, 4, 6};
    print("原数组", v1);
    
    sort(v1.begin(), v1.end());               // 升序
    print("sort升序", v1);
    
    sort(v1.begin(), v1.end(), greater<int>());  // 降序
    print("sort降序", v1);
    
    // 自定义排序规则
    sort(v1.begin(), v1.end(), [](int a, int b) {
        return a > b;  // 仍然是降序
    });
    
    // 2. stable_sort：稳定排序（保持相等元素的相对顺序）
    vector<pair<int, char>> v2 = {
        {1, 'a'}, {2, 'b'}, {1, 'c'}, {2, 'd'}, {1, 'e'}
    };
    stable_sort(v2.begin(), v2.end(), [](auto a, auto b) {
        return a.first < b.first;  // 按数字排序
    });
    // 结果：相同数字的元素保持原来的相对顺序
    
    // 3. partial_sort：部分排序（只排前N个）
    vector<int> v3 = {9, 8, 7, 6, 5, 4, 3, 2, 1, 0};
    partial_sort(v3.begin(), v3.begin() + 5, v3.end());  // 只排序前5个
    print("partial_sort前5个", v3);
    // 前5个是0,1,2,3,4（最小的5个），后面是其他
    
    // 4. nth_element：找出第N小的元素
    vector<int> v4 = {5, 2, 8, 1, 9, 3, 7, 4, 6, 0};
    nth_element(v4.begin(), v4.begin() + 4, v4.end());  // 排序后第4个位置（0-indexed）
    cout << "第5小的数（排序后第4个位置）：" << v4[4] << endl;
    print("nth_element结果", v4);
    // v4[4]保证是第5小的数，但其他元素不一定有序
    
    // 5. is_sorted：检查是否已排序
    vector<int> sorted = {1, 2, 3, 4, 5};
    vector<int> unsorted = {1, 3, 2, 4, 5};
    cout << "sorted是否有序：" << is_sorted(sorted.begin(), sorted.end()) << endl;    // 1
    cout << "unsorted是否有序：" << is_sorted(unsorted.begin(), unsorted.end()) << endl;  // 0
    
    // 6. reverse：反转
    vector<int> v5 = {1, 2, 3, 4, 5};
    reverse(v5.begin(), v5.end());
    print("reverse", v5);  // 5 4 3 2 1
    
    // 7. random_shuffle：随机打乱
    vector<int> v6 = {1, 2, 3, 4, 5};
    random_shuffle(v6.begin(), v6.end());
    print("random_shuffle", v6);
    
    // 8. merge：合并两个有序序列
    vector<int> a = {1, 3, 5, 7, 9};
    vector<int> b = {2, 4, 6, 8, 10};
    vector<int> merged(a.size() + b.size());
    merge(a.begin(), a.end(), b.begin(), b.end(), merged.begin());
    print("merge", merged);  // 1 2 3 4 5 6 7 8 9 10
    
    // 9. inplace_merge：合并两个相邻的有序区间
    vector<int> v7 = {1, 3, 5, 2, 4, 6};
    inplace_merge(v7.begin(), v7.begin() + 3, v7.end());
    print("inplace_merge", v7);  // 1 2 3 4 5 6
    
    return 0;
}
```

### 5.4 修改算法

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <list>
#include <iterator>
using namespace std;

void print(const string& label, const vector<int>& v) {
    cout << label << ": ";
    for (int x : v) cout << x << " ";
    cout << endl;
}

int main() {
    // 1. for_each：对每个元素执行操作
    vector<int> v1 = {1, 2, 3, 4, 5};
    cout << "for_each: ";
    for_each(v1.begin(), v1.end(), [](int x) {
        cout << x * 2 << " ";
    });
    cout << endl;
    
    // 2. transform：转换（类似map）
    vector<int> v2 = {1, 2, 3, 4, 5};
    vector<int> result(v2.size());
    
    // transform一元操作
    transform(v2.begin(), v2.end(), result.begin(), [](int x) {
        return x * 2;
    });
    print("transform *2", result);  // 2 4 6 8 10
    
    // transform二元操作（两个序列合并）
    vector<int> a = {1, 2, 3};
    vector<int> b = {10, 20, 30};
    vector<int> c(3);
    transform(a.begin(), a.end(), b.begin(), c.begin(), [](int x, int y) {
        return x + y;
    });
    print("transform a+b", c);  // 11 22 33
    
    // 3. copy / copy_if
    vector<int> v3 = {1, 2, 3, 4, 5};
    vector<int> dest1(5);
    copy(v3.begin(), v3.end(), dest1.begin());
    print("copy", dest1);  // 1 2 3 4 5
    
    vector<int> dest2;
    copy_if(v3.begin(), v3.end(), back_inserter(dest2), [](int x) {
        return x % 2 == 1;
    });
    print("copy_if奇数", dest2);  // 1 3 5
    
    // 4. copy_backward：反向复制
    vector<int> v4 = {1, 2, 3, 4, 5};
    vector<int> dest3(7);
    copy_backward(v4.begin(), v4.end(), dest3.end());
    // dest3 = {?, ?, 1, 2, 3, 4, 5}
    
    // 5. move：移动（类似copy，但源会被置空）
    vector<string> src = {"a", "b", "c"};
    vector<string> dst(src.size());
    move(src.begin(), src.end(), dst.begin());
    // dst = {"a", "b", "c"}
    // src中的元素可能为空（取决于具体实现）
    
    // 6. fill：填充
    vector<int> v5(5);
    fill(v5.begin(), v5.end(), 99);
    print("fill 99", v5);  // 99 99 99 99 99
    
    // 7. generate：生成（使用生成器函数）
    vector<int> v6(5);
    int counter = 1;
    generate(v6.begin(), v6.end(), [&counter]() {
        return counter++;
    });
    print("generate", v6);  // 1 2 3 4 5
    
    // 8. iota：递增序列（C++11）
    vector<int> v7(5);
    iota(v7.begin(), v7.end(), 10);  // 从10开始递增
    print("iota from 10", v7);  // 10 11 12 13 14
    
    // 9. replace / replace_if
    vector<int> v8 = {1, 2, 3, 2, 1};
    replace(v8.begin(), v8.end(), 2, 99);  // 把2替换成99
    print("replace 2→99", v8);  // 1 99 3 99 1
    
    replace_if(v8.begin(), v8.end(), [](int x) {
        return x > 50;
    }, 0);  // 大于50的替换成0
    print("replace_if >50→0", v8);  // 1 0 3 0 1
    
    // 10. remove / remove_if（不是真正删除，是"挤过去"）
    vector<int> v9 = {1, 2, 3, 4, 5, 2, 6, 2, 7, 2};
    auto newEnd = remove(v9.begin(), v9.end(), 2);  // 删除所有2
    v9.erase(newEnd, v9.end());                     // 真正删除末尾元素
    print("remove 2", v9);  // 1 3 4 5 6 7
    
    // 11. unique：删除相邻重复（需要先排序或配合remove）
    vector<int> v10 = {1, 1, 2, 2, 2, 3, 3, 1, 1};
    auto uniqueEnd = unique(v10.begin(), v10.end());
    v10.erase(uniqueEnd, v10.end());
    print("unique", v10);  // 1 2 3 1
    
    return 0;
}
```

### 5.5 数值算法

```cpp
#include <iostream>
#include <numeric>
#include <vector>
using namespace std;

int main() {
    // 1. accumulate：累加/自定义运算
    vector<int> v = {1, 2, 3, 4, 5};
    
    int sum = accumulate(v.begin(), v.end(), 0);  // 0是初始值
    cout << "累加和：" << sum << endl;  // 15
    
    // 计算乘积
    int product = accumulate(v.begin(), v.end(), 1, [](int a, int b) {
        return a * b;
    });
    cout << "累乘：" << product << endl;  // 120
    
    // 字符串拼接
    vector<string> words = {"Hello", " ", "World", "!"};
    string str = accumulate(words.begin(), words.end(), string(""));
    cout << str << endl;  // "Hello World!"
    
    // 2. inner_product：内积
    vector<int> a = {1, 2, 3};
    vector<int> b = {4, 5, 6};
    int inner = inner_product(a.begin(), a.end(), b.begin(), 0);
    // = 1*4 + 2*5 + 3*6 = 32
    cout << "内积：" << inner << endl;
    
    // 3. adjacent_difference：邻差
    vector<int> v2 = {1, 3, 6, 10, 15};
    vector<int> diff(5);
    adjacent_difference(v2.begin(), v2.end(), diff.begin());
    // diff = {1, 2, 3, 4, 5}
    cout << "邻差：";
    for (int x : diff) cout << x << " ";
    cout << endl;
    
    // 4. partial_sum：部分和
    vector<int> v3 = {1, 2, 3, 4, 5};
    vector<int> prefix(5);
    partial_sum(v3.begin(), v3.end(), prefix.begin());
    // prefix = {1, 3, 6, 10, 15}
    cout << "部分和：";
    for (int x : prefix) cout << x << " ";
    cout << endl;
    
    // 5. iota：生成递增序列
    vector<int> v4(5);
    iota(v4.begin(), v4.end(), 100);  // 100, 101, 102, 103, 104
    cout << "iota: ";
    for (int x : v4) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

### 5.6 常用算法速查表

| 算法 | 功能 | 示例 |
|:---|:---|:---|
| `find` | 查找元素 | `find(v.begin(), v.end(), x)` |
| `find_if` | 按条件查找 | `find_if(v.begin(), v.end(), pred)` |
| `count` | 统计个数 | `count(v.begin(), v.end(), x)` |
| `sort` | 排序 | `sort(v.begin(), v.end())` |
| `reverse` | 反转 | `reverse(v.begin(), v.end())` |
| `unique` | 去重 | `unique(v.begin(), v.end())` |
| `binary_search` | 二分查找 | `binary_search(v.begin(), v.end(), x)` |
| `copy` | 复制 | `copy(src.begin(), src.end(), dest.begin())` |
| `transform` | 转换 | `transform(in.begin(), in.end(), out.begin(), func)` |
| `for_each` | 遍历执行 | `for_each(v.begin(), v.end(), func)` |
| `accumulate` | 累加/自定义运算 | `accumulate(v.begin(), v.end(), init)` |
| `max_element` | 最大值 | `max_element(v.begin(), v.end())` |
| `min_element` | 最小值 | `min_element(v.begin(), v.end())` |

### 5.7 常见错误

```cpp
// ❌ 错误1：在未排序的容器上使用二分查找
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};
binary_search(v.begin(), v.end(), 5);  // ❌ 未定义行为！
sort(v.begin(), v.end());              // ✅ 先排序
binary_search(v.begin(), v.end(), 5);  // ✅ 现在可以了

// ❌ 错误2：remove不传end迭代器
vector<int> v2 = {1, 2, 3, 2, 4, 2, 5};
remove(v2.begin(), v2.end(), 2);       // ⚠️ 返回新end
// ❌ 忘记删除多余元素
v2.erase(remove(v2.begin(), v2.end(), 2), v2.end());  // ✅ 正确做法

// ❌ 错误3：使用lambda捕获引用导致问题
vector<int> v3 = {1, 2, 3};
int x = 5;
sort(v3.begin(), v3.end(), [x](int a, int b) {  // x是值捕获
    return a + x < b + x;  // ⚠️ x可能被修改
});

// ✅ 正确做法
int& ref = x;  // 明确使用引用
// 或者
sort(v3.begin(), v3.end(), [&x](int a, int b) {
    return a < b;
});
```

### 5.8 练习

```cpp
// 练习1：实现去重并排序
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
// 预期结果：1 2 3 4 5 6 9

// 练习2：实现最大值和第二大值
vector<int> v2 = {5, 2, 8, 1, 9, 3, 7, 4, 6};
// 提示：可以用nth_element

// 练习3：统计字符串中各字符出现次数
string s = "abracadabra";
map<char, int> freq;
for (char c : s) {
    freq[c]++;  // 自动计数
}

// 练习4：实现自定义排序（stable_sort）
vector<pair<string, int>> students = {
    {"Alice", 18},
    {"Bob", 19},
    {"Charlie", 18},
    {"David", 19}
};
// 按年龄排序，年龄相同按名字排序
stable_sort(students.begin(), students.end(), [](auto a, auto b) {
    if (a.second != b.second) return a.second < b.second;
    return a.first < b.first;
});
```

---

## 📝 本章小结

### 核心要点

1. **STL六大组件**：容器、迭代器、算法、适配器、分配器、函数对象
2. **string**：字符串处理，比C字符串安全易用
3. **容器选择**：
   - 随机访问多 → `vector`
   - 频繁头部操作 → `deque`
   - 频繁中间插入删除 → `list`
   - 键值对存储 → `map`
   - 集合去重排序 → `set`
4. **迭代器**：遍历容器的通用方式，注意迭代器失效问题
5. **算法**：常用`find`, `sort`, `copy`, `transform`, `accumulate`等

### 面试常考点

- vector vs list vs deque 的区别
- set vs map 的底层实现（红黑树）
- 迭代器失效的场景
- sort的稳定性问题
- map的operator[] vs insert的区别

---

*返回 [主目录](../README.md)*
