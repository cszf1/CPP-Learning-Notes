# 第10章 现代 C++ 补充

> 本章目标：把常用现代 C++ 工具串起来。它们不是炫技，是为了少写重复代码、少手动踩坑。

## 目录

1. STL 容器
2. 迭代器与算法
3. string
4. 异常处理
5. 命名空间
6. 智能指针
7. auto、范围 for、lambda
8. 本章小结

## 1. STL 容器

### 1.1 vector向量

#### 1.1.1 概念

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

#### 1.1.2 基本操作

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

#### 1.1.3 插入与删除

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

#### 1.1.4 二维vector

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

#### 1.1.5 常见错误

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

#### 1.1.6 练习

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

### 1.2 list链表

#### 1.2.1 概念

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

#### 1.2.2 基本操作

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

#### 1.2.3 vector vs list 何时选择

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

#### 1.2.4 练习

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

### 1.3 deque双端队列

#### 1.3.1 概念

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

#### 1.3.2 基本操作

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

#### 1.3.3 deque vs vector vs list

| 特性 | deque | vector | list |
|:---|:---:|:---:|:---:|
| 随机访问 | O(1) ✅ | O(1) ✅ | O(n) ❌ |
| 头部插入 | O(1) ✅ | O(n) ❌ | O(1) ✅ |
| 尾部插入 | O(1) ✅ | O(1) ✅ | O(1) ✅ |
| 中间插入 | O(n) | O(n) | O(1) |
| 内存连续 | 部分 ✅ | 完全 ✅ | ❌ |
| stack/queue底层 | - | - | - |

---

### 1.4 set与multiset

#### 1.4.1 概念

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

#### 1.4.2 基本操作

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

#### 1.4.3 自定义比较函数

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

#### 1.4.4 常见错误

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

#### 1.4.5 练习

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

### 1.5 map与multimap

#### 1.5.1 概念

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

#### 1.5.2 基本操作

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

#### 1.5.3 map统计词频

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

#### 1.5.4 multimap一对多

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

#### 1.5.5 常见错误

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

#### 1.5.6 练习

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

## 2. 迭代器与算法

### 2.1 迭代器是什么？

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

### 2.2 迭代器类型

| 类型 | 支持操作 | 容器示例 |
|:---|:---|:---|
| 输入迭代器 | `*`, `++` | - |
| 输出迭代器 | `*`, `++` | - |
| **前向迭代器** | `*`, `++`, `==`, `!=` | `forward_list` |
| **双向迭代器** | `*`, `++`, `--`, `==`, `!=` | `list`, `set`, `map` |
| **随机访问迭代器** | `*`, `++`, `--`, `+`, `-`, `[]`, `==`, `!=` | `vector`, `deque`, `string` |

### 2.3 迭代器基本操作

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

### 2.4 迭代器失效

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

### 2.5 💡 通俗理解

> **迭代器分类记忆口诀**：
> - **单向链表**：只能往前走（`forward_list`）
> - **双向链表**：可以往前走、往后退（`list`, `set`, `map`）
> - **数组**：可以跳跃（`vector`, `deque`, `string`）
>
> **迭代器失效口诀**：
> - **增删改**：可能导致迭代器失效
> - **修改**：capacity变化时必失效
> - **删除**：删除点之后的迭代器都可能失效

### 2.6 练习

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

### 2.7 STL 算法

#### 2.7.1 算法概述

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

#### 2.7.2 查找算法

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

#### 2.7.3 排序算法

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

#### 2.7.4 修改算法

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

#### 2.7.5 数值算法

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

#### 2.7.6 常用算法速查表

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

#### 2.7.7 常见错误

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

#### 2.7.8 练习

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

## 3. string

### 3.1 为什么需要string？

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

### 3.2 string的基本操作

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

### 3.3 string与C字符串转换

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

### 3.4 💡 通俗理解

> string就像一条可伸缩的传送带：
> - 不用预先告诉它多长，它会自动调整
> - 想加字符就加（`+=`），会自动变长
> - 想取字符就取（`[]`），会自动检查边界
> - 想找字符就找（`find`），会告诉你位置

### 3.5 常见错误

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

### 3.6 练习

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

## 4. 异常处理

> ⭐ C++重要特性：让程序优雅地处理"意外情况"

---

### 4.1. 异常的概念和必要性

#### 4.4.1.1 什么是异常

**概念讲解：**

在现实生活中，"异常"就是**不正常的情况**。比如：
- 去医院看病，发现号已经挂完了（异常）
- 去银行取钱，发现卡被吞了（异常）
- 点外卖，发现送来的菜是辣的（异常，但你没点辣的）

在程序中，**异常**就是程序在运行过程中遇到的**意外情况**或**错误状态**。比如：
- 除法运算时，除数为0
- 访问数组时，下标越界
- 打开文件时，文件不存在
- 内存不足，无法分配

**通俗比喻：**

想象你在做饭，正常流程是：
1. 打开冰箱 → 拿出食材 → 切菜 → 炒菜 → 盛盘 → 吃饭

但如果冰箱是空的怎么办？如果你不会切菜怎么办？如果锅着火怎么办？

这些"意外情况"就是**异常**。C++的异常处理机制就是让我们能够优雅地应对这些意外。

#### 4.4.1.2 为什么要处理异常

**传统错误处理方式的缺点：**

```cpp
// 方式一：使用返回值表示错误
int divide(int a, int b) {
    if (b == 0) {
        return -1;  // -1表示错误
    }
    return a / b;
}

// 问题：调用者必须检查每个返回值，很容易忘记检查
int result = divide(10, 0);  // 返回-1，但如果你不检查...
cout << result << endl;      // 输出-1，程序继续运行，可能导致更大的问题
```

```cpp
// 方式二：使用全局错误码
int g_error_code = 0;

void do_something() {
    // 设置错误码
    g_error_code = 1;
    // ...
}

// 问题：全局变量容易被遗忘，多线程环境下不安全
// 调用者很容易忘记检查错误码
```

**异常处理的优势：**

```cpp
// 方式三：使用异常处理
double divide(int a, int b) {
    if (b == 0) {
        throw "除数不能为零！";  // 抛出异常
    }
    return static_cast<double>(a) / b;
}

// 调用者可以选择是否捕获异常
// 如果不捕获，程序会自动终止，并显示错误信息
// 如果捕获，程序可以优雅地处理错误
```

**异常处理的三大优势：**

1. **分离错误处理代码**：正常代码和错误处理代码分开，代码更清晰
2. **错误传播自然**：异常可以自动向上层传播，不需要逐层检查返回值
3. **强制处理**：如果调用者不捕获异常，程序会终止，这逼着你处理错误

#### 4.4.1.3 异常处理的基本思想

```
正常流程：                                异常流程：
                                          
  主函数                                    主函数
    ↓                                        ↓
  调用函数A                               调用函数A
    ↓                                        ↓
  调用函数B                               调用函数B
    ↓                                         ↓
  调用函数C                               调用函数C ──抛出异常──→ 
    ↓                                              ↓
  继续执行...                                 被捕获！
                                              ↓
                                         处理错误
                                              ↓
                                         继续执行...
```

**图解说明：**
- 正常情况下，程序从上往下顺序执行
- 当函数C发现问题时，可以"扔"出一个异常
- 异常会沿着调用链向上"飘"，直到被某个地方"接住"
- 如果没人接住，程序就会崩溃（但会显示错误信息）

#### 4.4.1.4 常见的程序异常情况

```cpp
// 1. 算术异常
int a = 10, b = 0;
int c = a / b;  // 除以0！程序可能崩溃或得到错误结果

// 2. 数组越界
int arr[5] = {1, 2, 3, 4, 5};
cout << arr[10];  // 访问不存在的元素！危险！

// 3. 空指针访问
int* ptr = nullptr;
*ptr = 100;  // 解引用空指针！程序崩溃

// 4. 文件操作失败
ifstream file("nonexistent.txt");
string content;
file >> content;  // 读取失败！

// 5. 内存分配失败
int* big_array = new int[1000000000000];  // 可能分配失败
```

#### 4.4.1.5 异常处理的基本框架

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 这是C++异常处理的三部曲：try、throw、catch
// 想象成：尝试(try)做某事 -> 扔出(throw)异常 -> 抓住(catch)处理

int main() {
    cout << "=== C++异常处理入门 ===" << endl;
    
    // 示例：模拟一个可能出错的场景
    int age = -5;  // 年龄不能是负数！
    
    try {
        // 在try块中，放可能出错的代码
        cout << "检查年龄..." << endl;
        
        if (age < 0) {
            // 如果发现问题，就throw（扔出）一个异常
            throw runtime_error("年龄不能为负数！");
        }
        
        // 如果没有抛出异常，继续执行这里
        cout << "年龄正常：" << age << endl;
    }
    catch (runtime_error& e) {
        // catch块负责"接住"异常并处理
        // e.what()会返回throw时传入的字符串
        cout << "出错了：" << e.what() << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== C++异常处理入门 ===
检查年龄...
出错了：年龄不能为负数！
程序结束
```

#### 4.4.1.6 概念总结

| 术语 | 解释 | 类比 |
|------|------|------|
| 异常 | 程序运行时的错误情况 | 现实中的意外事件 |
| throw | 抛出异常的关键字 | 发现问题后"喊出来" |
| try | 尝试执行的代码块 | "试着做这件事" |
| catch | 捕获并处理异常 | "我来处理这个问题" |
| 异常对象 | 携带错误信息的对象 | 错误的"身份证" |

#### 4.4.1.7 常见错误

**错误1：忘记处理异常**
```cpp
// 这个程序可能会崩溃
int main() {
    int* ptr = new int[1000000000000];
    *ptr = 100;  // 如果内存分配失败，ptr是nullptr，这里会崩溃
    return 0;
}
```

**错误2：异常被默默忽略**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (...) {
    // 空catch块！异常被默默忽略了
    // 这是非常不好的做法
}
```

**错误3：异常用于控制正常流程**
```cpp
// 非常糟糕的写法！
try {
    for (int i = 0; i < 100; i++) {
        if (i == 50) {
            throw 999;  // 用异常来退出循环！
        }
    }
}
catch (int e) {
    cout << "退出循环" << endl;
}
```

#### 4.4.1.8 练习题

**练习1.1：理解异常概念**
什么是异常？为什么要使用异常处理？它相比传统错误处理有什么优势？

**练习1.2：识别异常情况**
以下代码中，哪些地方可能发生异常？
```cpp
int main() {
    int a = 10, b = 0;
    cout << a / b << endl;
    
    int arr[3] = {1, 2, 3};
    cout << arr[5] << endl;
    
    int* p = nullptr;
    cout << *p << endl;
    
    return 0;
}
```

**练习1.3：异常处理流程**
描述try-throw-catch的执行流程：
1. 当程序执行到throw时，会发生什么？
2. catch块什么时候会被执行？
3. 如果没有匹配的catch块，会发生什么？

---

### 4.2. try-catch基本语法

#### 4.4.2.1 语法结构详解

**基本语法：**

```cpp
try {
    // 可能抛出异常的代码
    // 正常逻辑写在这里
}
catch (异常类型1 变量名) {
    // 处理异常类型1
}
catch (异常类型2 变量名) {
    // 处理异常类型2
}
// ... 可以有多个catch块
```

**简单记忆：**
- `try` = 尝试（做这件事）
- `catch` = 抓住（处理问题）
- 格式：`try { 可能出错 } catch { 处理错误 }`

#### 4.4.2.2 最简单的异常处理

```cpp
#include <iostream>
#include <string>
using namespace std;

// 示例：用户输入验证
int main() {
    cout << "=== 最简单的异常处理 ===" << endl;
    
    int age;
    cout << "请输入您的年龄：";
    cin >> age;
    
    try {
        // 在try块中检查输入
        if (age < 0) {
            // 抛出异常：年龄不能是负数
            throw "年龄不能是负数！";
        }
        if (age > 150) {
            // 抛出异常：年龄太大了
            throw "年龄数值不合理！";
        }
        
        // 如果没有抛出异常，执行到这里
        cout << "您的年龄是：" << age << endl;
    }
    catch (const char* error_msg) {
        // 捕获字符串类型的异常
        cout << "错误：" << error_msg << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行示例1（正常输入）：**
```
=== 最简单的异常处理 ===
请输入您的年龄：25
您的年龄是：25
程序结束
```

**运行示例2（异常输入）：**
```
=== 最简单的异常处理 ===
请输入您的年龄：-5
错误：年龄不能是负数！
程序结束
```

#### 4.4.2.3 try-catch的执行流程

```
开始
  ↓
进入try块
  ↓
┌─────────────────────────┐
│ 代码正常执行完毕？        │
└─────────────────────────┘
     ↓yes            ↓no（发生throw）
     ↓               ↓
离开try块        抛出异常（throw）
     ↓               ↓
  执行catch块      跳转到匹配的catch
  （如果有的话）        ↓
     ↓          执行对应的catch块
离开catch块
     ↓
程序继续
```

#### 4.4.2.4 try-catch的详细执行过程

```cpp
#include <iostream>
using namespace std;

void test() {
    cout << "函数开始" << endl;
    
    try {
        cout << "try块开始" << endl;
        
        int a = 10, b = 0;
        if (b == 0) {
            cout << "发现b为0，抛出异常" << endl;
            throw runtime_error("除数不能为零");
        }
        
        cout << "try块结束（不会执行到这里）" << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获到异常：" << e.what() << endl;
    }
    
    cout << "函数结束" << endl;
}

int main() {
    cout << "=== try-catch执行流程 ===" << endl;
    test();
    cout << "main函数结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== try-catch执行流程 ===
函数开始
try块开始
发现b为0，抛出异常
捕获到异常：除数不能为零
函数结束
main函数结束
```

**流程解读：**
1. 进入try块
2. 发现b为0，执行throw
3. 立即跳转到catch块
4. 执行catch块中的处理代码
5. 继续执行try-catch之后的代码

#### 4.4.2.5 try块的作用域

**关键点：try块创建一个作用域**
- 在try块中声明的变量，catch块中**不能直接访问**
- 异常处理后，程序继续从catch块之后执行

```cpp
#include <iostream>
using namespace std;

int main() {
    try {
        int x = 10;
        cout << "x = " << x << endl;
        throw runtime_error("测试异常");
    }
    catch (runtime_error& e) {
        // 注意：这里无法访问x变量！
        // cout << x << endl;  // 错误：x未声明
        cout << "捕获异常：" << e.what() << endl;
    }
    
    // x同样不能在这里访问
    return 0;
}
```

#### 4.4.2.6 多个连续try-catch

```cpp
#include <iostream>
using namespace std;

void process_age(int age) {
    if (age < 0) {
        throw runtime_error("年龄不能为负数");
    }
    if (age > 150) {
        throw runtime_error("年龄不合理");
    }
    cout << "年龄正常：" << age << endl;
}

void process_balance(double balance) {
    if (balance < 0) {
        throw runtime_error("余额不能为负数");
    }
    cout << "余额正常：" << balance << endl;
}

int main() {
    cout << "=== 多个独立的try-catch ===" << endl;
    
    // 第一个try-catch
    try {
        process_age(-5);
    }
    catch (runtime_error& e) {
        cout << "年龄处理错误：" << e.what() << endl;
    }
    
    cout << "--- 继续执行 ---\n" << endl;
    
    // 第二个try-catch
    try {
        process_balance(-100);
    }
    catch (runtime_error& e) {
        cout << "余额处理错误：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== 多个独立的try-catch ===
年龄处理错误：年龄不能为负数
--- 继续执行 ---

余额处理错误：余额不能为负数

程序结束
```

#### 4.4.2.7 try-catch与函数调用

```cpp
#include <iostream>
using namespace std;

// 函数可能抛出异常
double safe_divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return static_cast<double>(a) / b;
}

int main() {
    cout << "=== try-catch与函数 ===" << endl;
    
    try {
        // 调用可能抛出异常的函数
        double result = safe_divide(10, 2);
        cout << "10 / 2 = " << result << endl;
        
        // 再次调用，这次会触发异常
        result = safe_divide(10, 0);
        cout << "这里不会执行" << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    cout << "程序继续执行" << endl;
    return 0;
}
```

**运行结果：**
```
=== try-catch与函数 ===
10 / 2 = 5
捕获异常：除数不能为零
程序继续执行
```

**关键理解：**
- 异常可以跨越函数边界传播
- 如果函数内部抛出异常，会自动向外传播，直到被catch捕获
- 这大大简化了错误处理

#### 4.4.2.8 嵌套的try-catch

```cpp
#include <iostream>
using namespace std;

void inner_function() {
    cout << "inner_function: 准备抛出异常" << endl;
    throw runtime_error("内部异常");
}

void outer_function() {
    try {
        cout << "outer_function: 调用inner_function" << endl;
        inner_function();
        cout << "outer_function: inner_function执行完毕" << endl;
    }
    catch (runtime_error& e) {
        cout << "outer_function: 捕获到异常-" << e.what() << endl;
        // 可以选择重新抛出
        // throw;  
    }
}

int main() {
    cout << "=== 嵌套try-catch ===" << endl;
    
    try {
        outer_function();
        cout << "main: outer_function执行完毕" << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到异常-" << e.what() << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== 嵌套try-catch ===
outer_function: 调用inner_function
inner_function: 准备抛出异常
outer_function: 捕获到异常-内部异常
main: outer_function执行完毕
程序结束
```

**解读：**
- inner_function抛出的异常被outer_function的catch捕获
- main函数中的catch块不会被执行
- 这展示了异常的传播和捕获机制

#### 4.4.2.9 常见错误

**错误1：异常未被捕获，程序终止**
```cpp
#include <iostream>
using namespace std;

void cause_problem() {
    throw runtime_error("这个异常没被捕获！");
}

int main() {
    cause_problem();  // 没有try-catch包裹
    cout << "这行不会执行" << endl;  // 不会打印
    return 0;
}
```

**输出：**
```
terminate called after throwing an instance of 'std::runtime_error'
  what(): 这个异常没被捕获！
已放弃(吐核)
```

**错误2：在try块外抛出异常**
```cpp
// 这个异常不会被任何catch捕获
int x = 10;
if (x < 0) {
    throw runtime_error("x不能为负");  // 在try块外面
}

try {
    // ...
}
catch (...) {
    // 捕获不到上面的throw！
}
```

**错误3：忘记try块包裹**
```cpp
#include <iostream>
using namespace std;

int main() {
    // 错误：直接在函数中throw，但不在try块中
    throw runtime_error("直接在函数中抛出");
    // 上面的异常会导致程序崩溃
    return 0;
}
```

#### 4.4.2.10 练习题

**练习2.1：基本语法练习**
写一个程序，从键盘输入两个整数，如果第二个数为0，抛出异常并提示"除数不能为零"。

**练习2.2：执行流程分析**
下面程序的输出是什么？
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "1. 开始" << endl;
    
    try {
        cout << "2. try开始" << endl;
        if (true) {
            throw runtime_error("异常!");
        }
        cout << "3. try结束" << endl;
    }
    catch (runtime_error& e) {
        cout << "4. catch: " << e.what() << endl;
    }
    
    cout << "5. 结束" << endl;
    return 0;
}
```

**练习2.3：函数中的异常**
写一个函数`check_age(int age)`，如果年龄不在0-150之间，抛出异常。在main函数中调用并捕获异常。

---

### 4.3. throw抛出异常

#### 4.4.3.1 throw的基本语法

**语法格式：**
```cpp
throw 表达式;
// 或者
throw 异常对象;
```

**可以抛出的类型：**
- 整数：`throw 404;`
- 字符串：`throw "错误信息";`
- 标准异常对象：`throw runtime_error("错误信息");`
- 自定义异常对象
- 任何数据类型

#### 4.4.3.2 抛出不同类型的异常

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

int main() {
    cout << "=== 抛出不同类型的异常 ===" << endl;
    
    // 1. 抛出整数
    try {
        throw 404;
    }
    catch (int code) {
        cout << "捕获整数异常，代码：" << code << endl;
    }
    
    // 2. 抛出字符串
    try {
        throw "文件未找到";
    }
    catch (const char* msg) {
        cout << "捕获字符串异常：" << msg << endl;
    }
    
    // 3. 抛出string对象
    try {
        throw string("数据库连接失败");
    }
    catch (string& msg) {
        cout << "捕获string异常：" << msg << endl;
    }
    
    // 4. 抛出标准异常对象
    try {
        throw runtime_error("运行时错误示例");
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 抛出不同类型的异常 ===
捕获整数异常，代码：404
捕获字符串异常：文件未找到
捕获string异常：数据库连接失败
捕获runtime_error：运行时错误示例
```

#### 4.4.3.3 异常的传播机制

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 异常可以自动向上传播
void level3() {
    cout << "level3: 准备抛出异常" << endl;
    throw runtime_error("3层深度的异常");
}

void level2() {
    cout << "level2: 调用level3" << endl;
    level3();
    cout << "level2: level3返回（不会执行）" << endl;
}

void level1() {
    cout << "level1: 调用level2" << endl;
    level2();
    cout << "level1: level2返回（不会执行）" << endl;
}

int main() {
    cout << "=== 异常的传播 ===" << endl;
    
    try {
        level1();
        cout << "main: level1返回（不会执行）" << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到异常-" << e.what() << endl;
    }
    
    cout << "程序继续执行" << endl;
    return 0;
}
```

**运行结果：**
```
=== 异常的传播 ===
level1: 调用level2
level2: 调用level3
level3: 准备抛出异常
main: 捕获到异常-3层深度的异常
程序继续执行
```

**传播机制图解：**
```
调用栈（从上到下）：

main()
  ↓
level1()
  ↓
level2()
  ↓
level3() ──throw──→ 异常对象

异常沿着调用链向上传播：
level3() → level2() → level1() → main()
                                    ↓
                              被catch捕获！
```

#### 4.4.3.4 重新抛出异常

**场景：** 内层catch只能处理部分异常，剩下的交给外层处理

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void process_file(const string& filename) {
    try {
        if (filename.empty()) {
            throw runtime_error("文件名为空");
        }
        if (filename == "no_permission.txt") {
            throw runtime_error("没有文件访问权限");
        }
        // 正常处理文件...
        cout << "成功处理文件：" << filename << endl;
    }
    catch (runtime_error& e) {
        // 只能处理部分问题
        if (filename.empty()) {
            cout << "本地处理空文件名异常" << endl;
            // 空文件名问题已处理，不再重新抛出
        }
        else {
            // 其他问题交给上层处理
            cout << "本层无法处理，重新抛出" << endl;
            throw;  // 关键：重新抛出当前异常
        }
    }
}

int main() {
    cout << "=== 重新抛出异常 ===" << endl;
    
    // 测试1：空文件名
    cout << "\n--- 测试1：空文件名 ---" << endl;
    try {
        process_file("");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    // 测试2：权限问题
    cout << "\n--- 测试2：权限问题 ---" << endl;
    try {
        process_file("no_permission.txt");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    // 测试3：正常文件
    cout << "\n--- 测试3：正常文件 ---" << endl;
    try {
        process_file("data.txt");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 重新抛出异常 ===

--- 测试1：空文件名 ---
本地处理空文件名异常

--- 测试2：权限问题 ---
本层无法处理，重新抛出
main: 捕获-没有文件访问权限

--- 测试3：正常文件 ---
成功处理文件：data.txt
```

#### 4.4.3.5 throw的位置与时机

**何时抛出异常：**

1. **函数参数不合法时**
```cpp
void set_age(int age) {
    if (age < 0 || age > 150) {
        throw invalid_argument("年龄必须在0-150之间");
    }
    // ...
}
```

2. **操作失败时**
```cpp
int* allocate_memory(size_t size) {
    if (size == 0) {
        throw invalid_argument("分配内存大小不能为0");
    }
    int* ptr = new int[size];
    if (!ptr) {
        throw bad_alloc();
    }
    return ptr;
}
```

3. **业务逻辑违反时**
```cpp
void withdraw(double balance, double amount) {
    if (amount > balance) {
        throw runtime_error("余额不足，无法取款");
    }
    // ...
}
```

#### 4.4.3.6 异常抛出与返回值的对比

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 方式1：使用返回值表示错误
int divide_with_return(int a, int b, int& result) {
    if (b == 0) {
        return -1;  // -1表示失败
    }
    result = a / b;
    return 0;  // 0表示成功
}

// 方式2：使用异常
double divide_with_exception(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return static_cast<double>(a) / b;
}

int main() {
    cout << "=== 异常 vs 返回值 ===" << endl;
    
    // 使用返回值
    int result1;
    if (divide_with_return(10, 2, result1) == 0) {
        cout << "方式1成功：10 / 2 = " << result1 << endl;
    }
    
    if (divide_with_return(10, 0, result1) == -1) {
        cout << "方式1失败：除数为0" << endl;
    }
    
    // 使用异常
    try {
        double result2 = divide_with_exception(10, 2);
        cout << "方式2成功：10 / 2 = " << result2 << endl;
        
        result2 = divide_with_exception(10, 0);
    }
    catch (runtime_error& e) {
        cout << "方式2失败：" << e.what() << endl;
    }
    
    return 0;
}
```

**两种方式的对比：**

| 方面 | 返回值方式 | 异常方式 |
|------|-----------|----------|
| 调用者忘记检查 | 可能导致错误传播 | 程序会崩溃（强制检查） |
| 代码复杂度 | 需要逐层检查返回值 | 可以集中处理 |
| 适用场景 | 常见错误，可预期 | 异常情况，不可预期 |
| 性能影响 | 几乎没有 | 抛出时有一定开销 |

#### 4.4.3.7 throw的常见用法

**用法1：简单抛出字符串**
```cpp
throw "错误信息";
```

**用法2：抛出标准异常**
```cpp
throw runtime_error("运行时错误");
throw invalid_argument("参数无效");
throw out_of_range("越界访问");
throw length_error("长度错误");
```

**用法3：抛出自定义类型**
```cpp
class MyException {
public:
    string message;
    int code;
    MyException(const string& msg, int c) : message(msg), code(c) {}
};

throw MyException("自定义错误", 1001);
```

#### 4.4.3.8 常见错误

**错误1：内存泄漏（在抛出异常前没有清理）**
```cpp
void bad_function() {
    int* data = new int[1000];  // 分配内存
    // 如果这里抛出异常，data指向的内存永远不会释放！
    if (some_error) {
        throw runtime_error("错误");
    }
    delete[] data;  // 不会执行到
}
```

**错误2：抛出指针（而不是对象）**
```cpp
void bad_function() {
    int* p = new int(10);
    throw p;  // 错误！应该抛对象，不应该抛指针
    // 如果catch块没有正确delete，会导致内存泄漏
}
```

**正确做法：**
```cpp
void good_function() {
    try {
        // 分配资源
        int* data = new int[1000];
        // 使用资源
        if (error_condition) {
            delete[] data;  // 先清理
            throw runtime_error("错误");  // 再抛出
        }
        // 正常清理
        delete[] data;
    }
    catch (...) {
        // 或者使用智能指针
    }
}
```

#### 4.4.3.9 练习题

**练习3.1：抛出基本类型**
写一个函数`max_of_three(int a, int b, int c)`，返回三个数中的最大值。如果三个数都是负数，抛出异常提示"所有数都是负数"。

**练习3.2：异常传播**
写三个函数A→B→C，在C中抛出异常，观察异常如何传播到A，并在A中捕获。

**练习3.3：重新抛出**
写一个函数处理用户输入，在try块中捕获并处理部分异常，对无法处理的异常进行重新抛出。

**练习3.4：选择异常类型**
为以下场景选择合适的异常类型：
1. 数组下标越界
2. 文件不存在
3. 内存分配失败
4. 参数格式错误
5. 余额不足

---

### 4.4. 多个catch块和异常类型匹配

#### 4.4.1 多个catch块的概念

**为什么需要多个catch块？**

不同的错误需要不同的处理方式。比如：
- 文件找不到 → 提示用户选择其他文件
- 内存不足 → 尝试清理内存或退出程序
- 网络断开 → 尝试重新连接

```cpp
try {
    // 各种可能出错的操作
}
catch (文件错误) {
    // 处理文件相关错误
}
catch (内存错误) {
    // 处理内存相关错误
}
catch (网络错误) {
    // 处理网络相关错误
}
```

#### 4.4.2 基本用法示例

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

void handle_error(int error_type) {
    try {
        switch (error_type) {
            case 1:
                throw runtime_error("运行时错误");
            case 2:
                throw invalid_argument("参数错误");
            case 3:
                throw out_of_range("越界错误");
            case 4:
                throw string("字符串错误");
            default:
                cout << "没有错误" << endl;
        }
    }
    catch (runtime_error& e) {
        cout << "捕获运行时错误：" << e.what() << endl;
        cout << "建议：检查程序逻辑" << endl;
    }
    catch (invalid_argument& e) {
        cout << "捕获参数错误：" << e.what() << endl;
        cout << "建议：检查输入参数" << endl;
    }
    catch (out_of_range& e) {
        cout << "捕获越界错误：" << e.what() << endl;
        cout << "建议：检查数组或容器大小" << endl;
    }
    catch (string& e) {
        cout << "捕获字符串异常：" << e << endl;
        cout << "建议：检查字符串处理" << endl;
    }
}

int main() {
    cout << "=== 多个catch块 ===" << endl;
    
    for (int i = 0; i <= 4; i++) {
        cout << "\n--- 测试类型" << i << " ---" << endl;
        handle_error(i);
    }
    
    return 0;
}
```

**运行结果：**
```
=== 多个catch块 ===

--- 测试类型0 ---
没有错误

--- 测试类型1 ---
捕获运行时错误：运行时错误
建议：检查程序逻辑

--- 测试类型2 ---
捕获参数错误：参数错误
建议：检查输入参数

--- 测试类型3 ---
捕获越界错误：越界错误
建议：检查数组或容器大小

--- 测试类型4 ---
捕获字符串异常：字符串错误
建议：检查字符串处理
```

#### 4.4.3 异常类型匹配规则

**匹配规则（从上到下匹配）：**

1. **精确匹配**：类型完全相同
2. **公有继承匹配**：抛出的是catch声明类型的派生类
3. **类型转换匹配**：
   - 非const → const
   - 派生类 → 基类
   - 数组 → 指针（某些情况下）

**匹配示例：**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 异常类层次结构：
// exception (基类)
//   ├── runtime_error (派生类)
//   │     ├── overflow_error
//   │     └── underflow_error
//   └── logic_error (派生类)
//         ├── invalid_argument
//         └── out_of_range

void test_match(int type) {
    try {
        switch (type) {
            case 1:
                throw runtime_error("运行时错误");
            case 2:
                throw invalid_argument("无效参数");
            case 3:
                throw out_of_range("越界");
            case 4:
                throw 404;  // int类型
            default:
                throw exception();  // 基类
        }
    }
    // 匹配顺序很重要！
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (out_of_range& e) {
        cout << "捕获out_of_range：" << e.what() << endl;
    }
    catch (runtime_error& e) {  // 这个会匹配case 1
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    catch (logic_error& e) {  // 不会匹配到，因为上面的invalid_argument/out_of_range是更具体的
        cout << "捕获logic_error：" << e.what() << endl;
    }
    catch (exception& e) {
        cout << "捕获exception：" << e.what() << endl;
    }
    catch (...) {
        cout << "捕获其他异常" << endl;
    }
}
```

#### 4.4.4 异常匹配顺序的重要性

**错误示例：顺序不当**

```cpp
try {
    throw invalid_argument("测试");
}
catch (exception& e) {  // 这个会匹配所有派生类
    cout << "捕获exception" << endl;
}
catch (invalid_argument& e) {  // 永远不会执行！
    cout << "捕获invalid_argument" << endl;
}
```

**正确示例：从小范围到大范围**

```cpp
try {
    throw invalid_argument("测试");
}
// 正确顺序：先捕获具体的，再捕获一般的
catch (invalid_argument& e) {  // 先匹配这个
    cout << "捕获具体的invalid_argument" << endl;
}
catch (exception& e) {  // 最后匹配基类
    cout << "捕获通用的exception" << endl;
}
```

#### 4.4.5 catch(...)通配符

**`catch(...)`可以捕获所有异常**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void universal_catch_demo() {
    try {
        // 可能抛出任何类型的异常
        throw runtime_error("测试");
    }
    catch (...) {
        // 捕获所有异常，但不区分类型
        cout << "捕获到某种异常，但不知道是什么类型" << endl;
    }
}

void cleanup_demo() {
    try {
        // 需要清理资源的操作
        throw runtime_error("操作失败");
    }
    catch (...) {
        cout << "发生异常，执行清理工作" << endl;
        // 清理代码...
        throw;  // 通常会重新抛出
    }
}

int main() {
    cout << "=== catch(...)通配符 ===" << endl;
    
    universal_catch_demo();
    
    cout << "\n--- 清理示例 ---" << endl;
    try {
        cleanup_demo();
    }
    catch (...) {
        cout << "main: 再次捕获" << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== catch(...)通配符 ===
捕获到某种异常，但不知道是什么类型

--- 清理示例 ---
发生异常，执行清理工作
main: 再次捕获
```

#### 4.4.6 异常重新抛出与类型

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void process_with_logging() {
    try {
        // 模拟各种错误
        throw invalid_argument("参数错误");
        // throw runtime_error("运行时错误");
    }
    catch (invalid_argument& e) {
        cout << "记录日志：参数错误 - " << e.what() << endl;
        // 重新抛出，保持原类型
        throw;
    }
    catch (runtime_error& e) {
        cout << "记录日志：运行时错误 - " << e.what() << endl;
        throw;
    }
}

int main() {
    cout << "=== 重新抛出保持类型 ===" << endl;
    
    try {
        process_with_logging();
    }
    catch (invalid_argument& e) {
        cout << "main: 捕获到invalid_argument - " << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到runtime_error - " << e.what() << endl;
    }
    
    return 0;
}
```

#### 4.4.7 实际应用：不同错误不同处理

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>
#include <string>
using namespace std;

// 模拟文件操作，可能遇到各种错误
void process_file(const string& filename) {
    try {
        if (filename.empty()) {
            throw invalid_argument("文件名为空");
        }
        
        if (filename == "not_found.txt") {
            throw runtime_error("文件不存在");
        }
        
        if (filename == "no_permission.txt") {
            throw runtime_error("没有访问权限");
        }
        
        if (filename == "corrupted.txt") {
            throw runtime_error("文件已损坏");
        }
        
        cout << "成功处理文件：" << filename << endl;
    }
    catch (invalid_argument& e) {
        // 参数错误：让用户重新输入
        cout << "[参数错误] " << e.what() << endl;
        cout << "请重新输入有效的文件名" << endl;
    }
    catch (runtime_error& e) {
        // 运行时错误：记录并尝试恢复
        cout << "[运行时错误] " << e.what() << endl;
        if (string(e.what()).find("不存在") != string::npos) {
            cout << "建议：检查文件路径是否正确" << endl;
        }
        else if (string(e.what()).find("权限") != string::npos) {
            cout << "建议：联系管理员获取权限" << endl;
        }
        else if (string(e.what()).find("损坏") != string::npos) {
            cout << "建议：尝试恢复或使用备份文件" << endl;
        }
    }
}

int main() {
    cout << "=== 不同错误不同处理 ===" << endl;
    
    test_file("data.txt");  // 成功
    test_file("");          // 参数错误
    test_file("not_found.txt");    // 文件不存在
    test_file("no_permission.txt"); // 权限问题
    test_file("corrupted.txt");    // 文件损坏
    
    return 0;
}
```

#### 4.4.8 常见错误

**错误1：catch顺序不当**
```cpp
// 错误：基类放在前面
try {
    throw invalid_argument("测试");
}
catch (exception& e) {  // 所有派生类都会被这个捕获
    cout << "exception" << endl;
}
catch (invalid_argument& e) {  // 永远不会执行
    cout << "invalid_argument" << endl;
}

// 正确：派生类放在前面
try {
    throw invalid_argument("测试");
}
catch (invalid_argument& e) {  // 先匹配这个
    cout << "invalid_argument" << endl;
}
catch (exception& e) {  // 作为后备
    cout << "exception" << endl;
}
```

**错误2：空catch块**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (...) {
    // 空catch块！异常被完全忽略
    // 这是非常糟糕的做法
}
```

**正确做法：**
```cpp
try {
    throw runtime_error("错误");
}
catch (...) {
    cerr << "发生未知错误" << endl;  // 至少记录日志
    // 或者重新抛出
    throw;
}
```

**错误3：按值捕获基类**
```cpp
// 可能导致对象切片问题
try {
    throw runtime_error("错误");
}
catch (exception e) {  // 按值捕获，可能丢失派生类信息
    // ...
}
```

**正确做法：**
```cpp
try {
    throw runtime_error("错误");
}
catch (exception& e) {  // 按引用捕获
    // ...
}
```

#### 4.4.9 练习题

**练习4.1：异常匹配**
```cpp
try {
    throw out_of_range("越界");
}
catch (invalid_argument& e) {  // 会匹配吗？
    cout << "invalid_argument" << endl;
}
catch (out_of_range& e) {  // 会匹配吗？
    cout << "out_of_range" << endl;
}
catch (exception& e) {  // 会匹配吗？
    cout << "exception" << endl;
}
```
以上代码的输出是什么？

**练习4.2：异常处理顺序**
创建一个包含多个catch块的函数，分别捕获：invalid_argument、logic_error、runtime_error、exception。说明为什么要按这个顺序排列。

**练习4.3：实现错误处理系统**
写一个计算器程序，处理以下错误：
1. 除数为0
2. 平方根的负数参数
3. 对数的小于等于0参数
4. 超出范围的选项

---

### 4.5. 标准异常类

#### 4.5.1 标准异常类层次结构

```
std::exception (所有异常的基类)
    │
    ├── std::bad_alloc          // 内存分配失败
    ├── std::bad_cast           // 动态类型转换失败
    ├── std::bad_typeid         // typeid(nullptr)
    │
    ├── std::logic_error        // 程序逻辑错误（可预知）
    │       ├── std::invalid_argument    // 无效参数
    │       ├── std::domain_error         // 数学定义域错误
    │       ├── std::length_error         // 长度超出限制
    │       └── std::out_of_range          // 越界访问
    │
    └── std::runtime_error      // 运行时错误（不可预知）
            ├── std::range_error         // 数值范围错误
            ├── std::overflow_error      // 算术上溢
            ├── std::underflow_error     // 算术下溢
            └── std::regex_error         // 正则表达式错误
```

#### 4.5.2 exception基类

```cpp
#include <iostream>
#include <exception>
using namespace std;

int main() {
    cout << "=== exception基类 ===" << endl;
    
    try {
        throw exception();
    }
    catch (exception& e) {
        cout << "捕获exception" << endl;
        cout << "异常信息：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== exception基类 ===
捕获exception
异常信息：std::exception
```

#### 4.5.3 logic_error及其派生类

**logic_error：程序员的错误，可通过检查代码避免**

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

// 1. invalid_argument - 无效参数
void validate_age(int age) {
    if (age < 0 || age > 150) {
        throw invalid_argument("年龄必须在0-150之间");
    }
    cout << "年龄有效：" << age << endl;
}

// 2. domain_error - 数学定义域错误
double safe_sqrt(double x) {
    if (x < 0) {
        throw domain_error("平方根参数不能为负数");
    }
    return sqrt(x);
}

// 3. length_error - 长度超出限制
void add_to_vector(vector<int>& v) {
    if (v.size() >= 100) {
        throw length_error("向量已满，无法添加更多元素");
    }
    v.push_back(100);
}

// 4. out_of_range - 越界访问
int get_element(const vector<int>& v, size_t index) {
    if (index >= v.size()) {
        throw out_of_range("索引" + to_string(index) + "超出范围[0-" + to_string(v.size()-1) + "]");
    }
    return v[index];
}

int main() {
    cout << "=== logic_error及其派生类 ===" << endl;
    
    // 测试invalid_argument
    cout << "\n--- invalid_argument ---" << endl;
    try {
        validate_age(-5);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    
    // 测试domain_error
    cout << "\n--- domain_error ---" << endl;
    try {
        double result = safe_sqrt(-16);
    }
    catch (domain_error& e) {
        cout << "捕获domain_error：" << e.what() << endl;
    }
    
    // 测试length_error
    cout << "\n--- length_error ---" << endl;
    try {
        vector<int> v(100, 0);
        add_to_vector(v);
    }
    catch (length_error& e) {
        cout << "捕获length_error：" << e.what() << endl;
    }
    
    // 测试out_of_range
    cout << "\n--- out_of_range ---" << endl;
    try {
        vector<int> v = {1, 2, 3};
        int x = get_element(v, 10);
    }
    catch (out_of_range& e) {
        cout << "捕获out_of_range：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== logic_error及其派生类 ===

--- invalid_argument ---
捕获invalid_argument：年龄必须在0-150之间

--- domain_error ---
捕获domain_error：平方根参数不能为负数

--- length_error ---
捕获length_error：向量已满，无法添加更多元素

--- out_of_range ---
捕获out_of_range：索引10超出范围[0-2]
```

#### 4.5.4 runtime_error及其派生类

**runtime_error：运行时发生的错误，不可预知**

```cpp
#include <iostream>
#include <stdexcept>
#include <fstream>
#include <string>
using namespace std;

// 1. range_error - 数值范围错误
double calculate(double base, int exponent) {
    if (exponent > 1000) {
        throw range_error("指数太大，结果超出范围");
    }
    double result = 1;
    for (int i = 0; i < exponent; i++) {
        result *= base;
    }
    return result;
}

// 2. overflow_error - 算术上溢
int safe_multiply(int a, int b) {
    if (a > 1000000 || b > 1000000) {
        throw overflow_error("乘法可能导致溢出");
    }
    return a * b;
}

// 3. underflow_error - 算术下溢
double very_small_division() {
    throw underflow_error("结果太小，接近下溢");
}

// 4. 文件相关运行时错误
void read_file_content(const string& filename) {
    ifstream file(filename);
    if (!file.is_open()) {
        throw runtime_error("无法打开文件：" + filename);
    }
    string line;
    while (getline(file, line)) {
        cout << line << endl;
    }
}

int main() {
    cout << "=== runtime_error及其派生类 ===" << endl;
    
    // 测试range_error
    cout << "\n--- range_error ---" << endl;
    try {
        calculate(2, 2000);
    }
    catch (range_error& e) {
        cout << "捕获range_error：" << e.what() << endl;
    }
    
    // 测试overflow_error
    cout << "\n--- overflow_error ---" << endl;
    try {
        int result = safe_multiply(9999999, 9999999);
    }
    catch (overflow_error& e) {
        cout << "捕获overflow_error：" << e.what() << endl;
    }
    
    // 测试underflow_error
    cout << "\n--- underflow_error ---" << endl;
    try {
        very_small_division();
    }
    catch (underflow_error& e) {
        cout << "捕获underflow_error：" << e.what() << endl;
    }
    
    // 测试文件错误
    cout << "\n--- 文件运行时错误 ---" << endl;
    try {
        read_file_content("nonexistent_file.txt");
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== runtime_error及其派生类 ===

--- range_error ---
捕获range_error：指数太大，结果超出范围

--- overflow_error ---
捕获runtime_error：乘法可能导致溢出

--- underflow_error ---
捕获underflow_error：结果太小，接近下溢

--- 文件运行时错误 ---
捕获runtime_error：无法打开文件：nonexistent_file.txt
```

#### 4.5.5 bad_alloc异常

**内存分配失败时抛出**

```cpp
#include <iostream>
#include <new>
using namespace std;

int main() {
    cout << "=== bad_alloc异常 ===" << endl;
    
    try {
        // 尝试分配一个巨大的内存块
        // 可能会失败，抛出bad_alloc异常
        cout << "尝试分配超大内存..." << endl;
        size_t huge_size = SIZE_MAX / sizeof(int);
        int* arr = new int[huge_size];
        cout << "分配成功（不太可能）" << endl;
        delete[] arr;
    }
    catch (bad_alloc& e) {
        cout << "捕获bad_alloc：" << e.what() << endl;
        cout << "内存分配失败！建议：" << endl;
        cout << "1. 减少请求的内存大小" << endl;
        cout << "2. 释放不再使用的内存" << endl;
        cout << "3. 检查内存泄漏" << endl;
    }
    
    cout << "\n程序继续执行" << endl;
    return 0;
}
```

#### 4.5.6 标准异常使用场景总结

```cpp
#include <iostream>
#include <stdexcept>
#include <vector>
#include <string>
using namespace std;

// 综合示例：银行账户系统
class BankAccount {
private:
    string account_holder;
    double balance;
    static const double MIN_BALANCE = 0.0;
    static const double MAX_BALANCE = 1000000.0;
    
public:
    BankAccount(const string& name, double initial) 
        : account_holder(name), balance(0) {
        // 验证账户名
        if (name.empty()) {
            throw invalid_argument("账户名不能为空");
        }
        // 验证初始金额
        if (initial < MIN_BALANCE) {
            throw invalid_argument("初始金额不能为负数");
        }
        if (initial > MAX_BALANCE) {
            throw overflow_error("初始金额超出最大限制");
        }
        balance = initial;
    }
    
    void deposit(double amount) {
        if (amount < 0) {
            throw invalid_argument("存款金额不能为负数");
        }
        if (balance + amount > MAX_BALANCE) {
            throw overflow_error("存款后余额超出最大限制");
        }
        balance += amount;
    }
    
    void withdraw(double amount) {
        if (amount < 0) {
            throw invalid_argument("取款金额不能为负数");
        }
        if (amount > balance) {
            throw runtime_error("余额不足");
        }
        if (balance - amount < MIN_BALANCE) {
            throw runtime_error("取款后余额不能为负");
        }
        balance -= amount;
    }
    
    double get_balance() const {
        return balance;
    }
};

int main() {
    cout << "=== 银行账户异常处理示例 ===" << endl;
    
    BankAccount* account = nullptr;
    
    // 测试各种异常
    try {
        // 1. 无效账户名
        cout << "\n--- 测试1：无效账户名 ---" << endl;
        account = new BankAccount("", 1000);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    
    try {
        // 2. 有效账户
        cout << "\n--- 测试2：创建有效账户 ---" << endl;
        account = new BankAccount("张三", 1000);
        cout << "账户创建成功，余额：" << account->get_balance() << endl;
        
        // 3. 负数存款
        cout << "\n--- 测试3：负数存款 ---" << endl;
        account->deposit(-100);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    catch (overflow_error& e) {
        cout << "捕获overflow_error：" << e.what() << endl;
    }
    
    try {
        // 4. 余额不足
        cout << "\n--- 测试4：余额不足取款 ---" << endl;
        account->withdraw(5000);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    try {
        // 5. 成功操作
        cout << "\n--- 测试5：正常操作 ---" << endl;
        account->deposit(500);
        account->withdraw(300);
        cout << "当前余额：" << account->get_balance() << endl;
    }
    catch (exception& e) {
        cout << "捕获其他异常：" << e.what() << endl;
    }
    
    delete account;
    cout << "\n程序结束" << endl;
    
    return 0;
}
```

#### 4.5.7 常见错误

**错误1：捕获了异常但没有处理**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (runtime_error& e) {
    // 捕获了，但什么都没做
    // 异常被忽略了！
}
```

**错误2：捕获错误的异常类型**
```cpp
try {
    throw invalid_argument("参数错误");
}
catch (out_of_range& e) {  // 类型不匹配！不会捕获到
    // 这段代码不会执行
}
```

**错误3：异常被默默转换**
```cpp
try {
    throw runtime_error("运行时错误");
}
catch (exception& e) {  // 可以匹配，基类可以捕获派生类
    // 但e.what()返回的是派生类的消息
    cout << e.what() << endl;  // 仍然输出"运行时错误"
}
```

#### 4.5.8 练习题

**练习5.1：匹配异常类型**
为以下场景选择最合适的异常类型：
1. `vector<int> v; v[100]` 访问
2. `sqrt(-16)` 函数调用
3. `new int[999999999999]` 内存分配
4. 函数参数为nullptr
5. 整数相除结果溢出

**练习5.2：使用标准异常改写计算器**
用标准异常改写第4章的计算器程序：
- 无效选择 → invalid_argument
- 除数为0 → invalid_argument
- 越界 → out_of_range
- 结果溢出 → overflow_error

**练习5.3：异常层次结构**
画出标准异常类的层次结构，并说明logic_error和runtime_error的区别。

---

### 4.6. 自定义异常类

#### 4.6.1 为什么需要自定义异常

**标准异常的局限性：**
- 无法携带业务特定的错误信息
- 无法定义业务相关的错误码
- 异常类型不够精确

**自定义异常的优势：**
- 可以携带丰富的错误信息
- 可以定义业务相关的属性
- 可以建立应用程序特定的异常层次

#### 4.6.2 最简单的自定义异常

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 最简单的自定义异常：继承exception基类
class MyException : public exception {
public:
    // 重写what()方法，返回错误信息
    const char* what() const noexcept override {
        return "我的自定义异常";
    }
};

int main() {
    cout << "=== 简单的自定义异常 ===" << endl;
    
    try {
        throw MyException();
    }
    catch (exception& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    return 0;
}
```

#### 4.6.3 带有构造参数的自定义异常

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 带错误消息的自定义异常
class BusinessException : public exception {
private:
    string message;  // 存储错误消息
    int error_code;  // 存储错误码
    
public:
    // 构造函数
    BusinessException(const string& msg, int code = 0) 
        : message(msg), error_code(code) {}
    
    // 重写what()方法
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    // 获取错误码
    int get_error_code() const {
        return error_code;
    }
    
    // 获取错误消息
    string get_message() const {
        return message;
    }
};

int main() {
    cout << "=== 带参数的自定义异常 ===" << endl;
    
    try {
        int user_id = -1;
        if (user_id < 0) {
            throw BusinessException("用户ID不能为负数", 1001);
        }
    }
    catch (BusinessException& e) {
        cout << "错误码：" << e.get_error_code() << endl;
        cout << "错误信息：" << e.get_message() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 带参数的自定义异常 ===
错误码：1001
错误信息：用户ID不能为负数
```

#### 4.6.4 建立异常类层次

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <vector>
using namespace std;

// 基类：应用程序异常
class AppException : public exception {
protected:
    string message;
    string timestamp;
    
public:
    AppException(const string& msg) : message(msg) {
        // 简单的时间戳（实际应用中使用chrono）
        timestamp = "2024-01-01 12:00:00";
    }
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    string get_timestamp() const { return timestamp; }
    virtual string get_type() const { return "AppException"; }
};

// 业务异常基类
class BusinessException : public AppException {
protected:
    int error_code;
    
public:
    BusinessException(const string& msg, int code) 
        : AppException(msg), error_code(code) {}
    
    int get_error_code() const { return error_code; }
    string get_type() const override { return "BusinessException"; }
};

// 数据库异常
class DatabaseException : public AppException {
public:
    DatabaseException(const string& msg) : AppException(msg) {}
    string get_type() const override { return "DatabaseException"; }
};

// 用户相关异常
class UserException : public BusinessException {
public:
    UserException(const string& msg, int code) : BusinessException(msg, code) {}
    string get_type() const override { return "UserException"; }
};

// 订单相关异常
class OrderException : public BusinessException {
public:
    OrderException(const string& msg, int code) : BusinessException(msg, code) {}
    string get_type() const override { return "OrderException"; }
};

// 网络异常
class NetworkException : public AppException {
public:
    NetworkException(const string& msg) : AppException(msg) {}
    string get_type() const override { return "NetworkException"; }
};

// 测试函数
void test_user_operations() {
    try {
        throw UserException("用户不存在", 1001);
    }
    catch (UserException& e) {
        cout << "[" << e.get_type() << "] " << e.what() 
             << " (错误码: " << e.get_error_code() << ")" << endl;
    }
}

void test_order_operations() {
    try {
        throw OrderException("订单已过期", 2001);
    }
    catch (OrderException& e) {
        cout << "[" << e.get_type() << "] " << e.what() 
             << " (错误码: " << e.get_error_code() << ")" << endl;
    }
}

void test_database() {
    try {
        throw DatabaseException("无法连接到数据库");
    }
    catch (DatabaseException& e) {
        cout << "[" << e.get_type() << "] " << e.what() << endl;
    }
}

int main() {
    cout << "=== 自定义异常层次 ===" << endl;
    
    test_user_operations();
    test_order_operations();
    test_database();
    
    // 使用基类捕获所有派生异常
    cout << "\n--- 使用基类统一捕获 ---" << endl;
    try {
        throw OrderException("库存不足", 2002);
    }
    catch (AppException& e) {
        cout << "捕获到应用异常：" << e.get_type() << " - " << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 自定义异常层次 ===
[UserException] 用户不存在 (错误码: 1001)
[OrderException] 订单已过期 (错误码: 2001)
[DatabaseException] 无法连接到数据库
[AppException] 库存不足
```

#### 4.6.5 完整的异常类设计示例

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <vector>
#include <sstream>
using namespace std;

// ========== 异常错误码定义 ==========
namespace ErrorCode {
    const int SUCCESS = 0;
    
    // 用户相关错误码 (1000-1999)
    namespace User {
        const int NOT_FOUND = 1001;
        const int ALREADY_EXISTS = 1002;
        const int INVALID_PASSWORD = 1003;
        const int PERMISSION_DENIED = 1004;
    }
    
    // 订单相关错误码 (2000-2999)
    namespace Order {
        const int NOT_FOUND = 2001;
        const int ALREADY_PAID = 2002;
        const int OUT_OF_STOCK = 2003;
        const int PRICE_CHANGED = 2004;
    }
    
    // 系统相关错误码 (9000-9999)
    namespace System {
        const int DATABASE_ERROR = 9001;
        const int NETWORK_ERROR = 9002;
        const int UNKNOWN_ERROR = 9999;
    }
}

// ========== 自定义异常基类 ==========
class AppException : public exception {
protected:
    int code;
    string message;
    string details;
    
public:
    AppException(int c, const string& msg, const string& det = "")
        : code(c), message(msg), details(det) {}
    
    const char* what() const noexcept override {
        // 返回完整信息
        static string buffer;
        stringstream ss;
        ss << "[错误码:" << code << "] " << message;
        if (!details.empty()) {
            ss << " (" << details << ")";
        }
        buffer = ss.str();
        return buffer.c_str();
    }
    
    int get_code() const { return code; }
    string get_message() const { return message; }
    string get_details() const { return details; }
};

// ========== 用户异常 ==========
class UserException : public AppException {
public:
    UserException(int code, const string& msg, const string& details = "")
        : AppException(code, msg, details) {}
};

// ========== 订单异常 ==========
class OrderException : public AppException {
private:
    string order_id;
    
public:
    OrderException(int code, const string& msg, const string& oid = "",
                   const string& details = "")
        : AppException(code, msg, details), order_id(oid) {}
    
    string get_order_id() const { return order_id; }
};

// ========== 异常处理函数模板 ==========
void handle_exception(AppException& e) {
    cerr << "异常发生：" << e.what() << endl;
    
    // 根据错误码分类处理
    if (e.get_code() >= ErrorCode::User::NOT_FOUND && 
        e.get_code() < ErrorCode::User::NOT_FOUND + 1000) {
        cerr << "建议：检查用户相关操作" << endl;
    }
    else if (e.get_code() >= ErrorCode::Order::NOT_FOUND && 
             e.get_code() < ErrorCode::Order::NOT_FOUND + 1000) {
        cerr << "建议：检查订单相关操作" << endl;
    }
}

// ========== 测试 ==========
class User {
public:
    static User find_by_id(int id) {
        if (id <= 0) {
            throw UserException(ErrorCode::User::NOT_FOUND, 
                               "用户不存在", 
                               "用户ID: " + to_string(id));
        }
        // 模拟找到用户
        return User();
    }
};

class Order {
public:
    static void pay(const string& order_id, double amount) {
        if (order_id.empty()) {
            throw OrderException(ErrorCode::Order::NOT_FOUND,
                               "订单号不能为空");
        }
        if (amount <= 0) {
            throw OrderException(ErrorCode::Order::PRICE_CHANGED,
                               "支付金额无效",
                               "订单ID: " + order_id);
        }
        // 模拟支付
        cout << "订单 " << order_id << " 支付成功，金额：" << amount << endl;
    }
};

int main() {
    cout << "=== 完整的异常类设计 ===" << endl;
    
    // 测试用户异常
    cout << "\n--- 测试用户异常 ---" << endl;
    try {
        User::find_by_id(-1);
    }
    catch (UserException& e) {
        handle_exception(e);
    }
    
    // 测试订单异常
    cout << "\n--- 测试订单异常 ---" << endl;
    try {
        Order::pay("", 100);
    }
    catch (OrderException& e) {
        handle_exception(e);
    }
    
    try {
        Order::pay("ORDER123", -50);
    }
    catch (OrderException& e) {
        handle_exception(e);
    }
    
    return 0;
}
```

#### 4.6.6 自定义异常的构造函数设计

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 方法1：简单字符串构造函数
class SimpleException : public exception {
    string msg;
public:
    SimpleException(const char* s) : msg(s) {}
    const char* what() const noexcept override { return msg.c_str(); }
};

// 方法2：支持格式化消息
class FormattedException : public exception {
    string msg;
public:
    FormattedException(const string& fmt, int value) {
        // 简单的字符串格式化
        char buffer[256];
        snprintf(buffer, sizeof(buffer), fmt.c_str(), value);
        msg = buffer;
    }
    
    const char* what() const noexcept override { return msg.c_str(); }
};

// 方法3：链式异常（记录异常链）
class ExceptionWithContext : public exception {
    string current_message;
    const exception* nested_exception;  // 嵌套的异常
    
public:
    ExceptionWithContext(const string& msg, const exception* nested = nullptr)
        : current_message(msg), nested_exception(nested) {}
    
    const char* what() const noexcept override {
        return current_message.c_str();
    }
    
    const exception* get_nested() const { return nested_exception; }
};

int main() {
    cout << "=== 异常构造函数设计 ===" << endl;
    
    // 测试简单异常
    try {
        throw SimpleException("简单的错误消息");
    }
    catch (exception& e) {
        cout << "SimpleException: " << e.what() << endl;
    }
    
    // 测试格式化异常
    try {
        throw FormattedException("数组大小 %d 超出限制", 1000);
    }
    catch (exception& e) {
        cout << "FormattedException: " << e.what() << endl;
    }
    
    return 0;
}
```

#### 4.6.7 异常与类结合的最佳实践

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <memory>
using namespace std;

// 定义异常类型
class MathException : public exception {
public:
    enum class Type {
        DIVIDE_BY_ZERO,
        OVERFLOW,
        UNDERFLOW,
        INVALID_INPUT
    };
    
private:
    Type type;
    string message;
    
public:
    MathException(Type t, const string& msg) : type(t), message(msg) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    Type get_type() const { return type; }
};

// 使用异常安全的数学类
class SafeCalculator {
public:
    static double divide(double a, double b) {
        if (b == 0) {
            throw MathException(MathException::Type::DIVIDE_BY_ZERO,
                              "除数不能为零");
        }
        return a / b;
    }
    
    static double safe_sqrt(double x) {
        if (x < 0) {
            throw MathException(MathException::Type::INVALID_INPUT,
                              "平方根参数不能为负数");
        }
        return sqrt(x);
    }
    
    static int factorial(int n) {
        if (n < 0) {
            throw MathException(MathException::Type::INVALID_INPUT,
                              "阶乘参数不能为负数");
        }
        if (n > 20) {
            throw MathException(MathException::Type::OVERFLOW,
                              "阶乘结果超出int范围");
        }
        int result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    }
};

// 异常处理辅助函数
void handle_math_error(const MathException& e) {
    cout << "数学错误：" << e.what() << endl;
    
    switch (e.get_type()) {
        case MathException::Type::DIVIDE_BY_ZERO:
            cout << "  -> 建议：检查除数是否为零" << endl;
            break;
        case MathException::Type::OVERFLOW:
            cout << "  -> 建议：使用更大范围的类型" << endl;
            break;
        case MathException::Type::INVALID_INPUT:
            cout << "  -> 建议：检查输入参数" << endl;
            break;
        default:
            cout << "  -> 未知错误类型" << endl;
    }
}

int main() {
    cout << "=== 异常安全的计算器 ===" << endl;
    
    // 测试各种异常
    vector<pair<string, function<void()>>> tests = {
        {"除以零", [](){ SafeCalculator::divide(10, 0); }},
        {"负数平方根", [](){ SafeCalculator::safe_sqrt(-5); }},
        {"负数阶乘", [](){ SafeCalculator::factorial(-1); }},
        {"大数阶乘", [](){ SafeCalculator::factorial(25); }},
        {"正常计算", [](){ 
            cout << SafeCalculator::divide(10, 2) << endl;
            cout << SafeCalculator::safe_sqrt(16) << endl;
            cout << SafeCalculator::factorial(5) << endl;
        }}
    };
    
    for (size_t i = 0; i < tests.size(); i++) {
        cout << "\n--- 测试" << i+1 << ": " << tests[i].first << " ---" << endl;
        try {
            tests[i].second();
        }
        catch (MathException& e) {
            handle_math_error(e);
        }
        catch (exception& e) {
            cout << "其他异常：" << e.what() << endl;
        }
    }
    
    return 0;
}
```

#### 4.6.8 常见错误

**错误1：继承时忘记重写what()**
```cpp
// 错误：返回的是基类的消息
class BadException : public exception {
public:
    // 忘记重写what()
};

try {
    throw BadException();
}
catch (exception& e) {
    cout << e.what();  // 输出 "std::exception"，不是自定义消息
}
```

**正确做法：**
```cpp
class GoodException : public exception {
    string msg;
public:
    GoodException(const string& m) : msg(m) {}
    
    const char* what() const noexcept override {
        return msg.c_str();
    }
};
```

**错误2：对象切片问题**
```cpp
// 错误：按值传递会导致对象切片
try {
    throw DerivedException("错误");
}
catch (BaseException e) {  // 切片！只拷贝了Base部分
    // ...
}
```

**正确做法：**
```cpp
// 正确：按引用传递
try {
    throw DerivedException("错误");
}
catch (BaseException& e) {  // 保持完整对象
    // ...
}
```

**错误3：异常类中使用动态内存导致内存泄漏**
```cpp
// 不好：可能泄漏
class BadException : public exception {
    char* msg;  // 动态分配
public:
    BadException(const char* s) {
        msg = new char[strlen(s) + 1];
        strcpy(msg, s);
    }
    // 忘记析构函数
};

// 好：使用string
class GoodException : public exception {
    string msg;  // 自动管理内存
public:
    GoodException(const char* s) : msg(s) {}
    const char* what() const noexcept override {
        return msg.c_str();
    }
};
```

#### 4.6.9 练习题

**练习6.1：创建基本异常类**
创建一个`NetworkException`类，继承`std::exception`，包含：
- 错误消息
- 错误码（404、500、503等）
- 重写what()方法

**练习6.2：设计异常层次**
为一个学生信息管理系统设计异常层次：
- 基础异常类AppException
- 学生异常类StudentException
- 成绩异常类GradeException
- 课程异常类CourseException

**练习6.3：使用自定义异常**
使用练习6.2设计的异常类，实现一个简单的成绩计算程序，处理以下异常：
- 学生不存在
- 成绩超出范围(0-100)
- 课程已满

---

### 4.7. 异常规范（noexcept）

#### 4.7.1 什么是noexcept

**noexcept**是C++11引入的关键字，用于声明**不会抛出异常**的函数。

**语法：**
```cpp
void func() noexcept;           // 明确声明不抛出异常
void func();                    // 可能抛出异常
void func() noexcept(false);    // 可能抛出异常（与上面等价）
```

#### 4.7.2 基本用法

```cpp
#include <iostream>
#include <exception>
using namespace std;

// 声明不会抛出异常的函数
int add(int a, int b) noexcept {
    return a + b;
}

// 可能抛出异常的函数
int divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return a / b;
}

// 测试noexcept
void test() {
    cout << "add()是noexcept? " << noexcept(add(1, 2)) << endl;
    cout << "divide()是noexcept? " << noexcept(divide(1, 1)) << endl;
    cout << "throw()是noexcept? " << noexcept(throw 1) << endl;
}

int main() {
    cout << "=== noexcept基本用法 ===" << endl;
    test();
    return 0;
}
```

**运行结果：**
```
=== noexcept基本用法 ===
add()是noexcept? 1
divide()是noexcept? 0
throw()是noexcept? 0
```

#### 4.7.3 noexcept的作用

**作用1：优化机会**
- 编译器知道函数不抛异常，可以进行更多优化
- 不需要生成栈展开的代码

**作用2：明确意图**
- 告诉其他程序员这个函数不会抛异常
- 帮助静态分析工具检测错误

**作用3：容器优化**
- vector等容器在重新分配内存时，会优先选择noexcept的析构函数

#### 4.7.4 异常规范的历史

```cpp
// 旧式异常规范（已废弃）
void old_style() throw();           // 声明不抛出任何异常
void old_style2() throw(runtime_error);  // 只抛出特定异常（危险！）

// 新式（C++11起推荐）
void new_style() noexcept;         // 声明不抛出异常
void new_style2();                  // 可能抛出异常
```

**为什么旧式规范被废弃？**
```cpp
// 危险！如果函数实际抛出了其他类型，会调用unexpected()
void risky() throw(invalid_argument) {  // 只允许invalid_argument
    throw runtime_error("运行时错误"); // 但实际抛出了这个！
    // 导致程序调用unexpected()，默认会terminate()
}
```

#### 4.7.5 noexcept与移动语义

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class TestMove {
    string data;
public:
    TestMove() = default;
    TestMove(const string& s) : data(s) {}
    
    // 移动构造函数应该是noexcept
    // 否则vector在重新分配时会选择拷贝而不是移动
    TestMove(TestMove&& other) noexcept : data(move(other.data)) {
        cout << "移动构造" << endl;
    }
    
    TestMove& operator=(TestMove&& other) noexcept {
        if (this != &other) {
            data = move(other.data);
            cout << "移动赋值" << endl;
        }
        return *this;
    }
};

int main() {
    cout << "=== noexcept与移动语义 ===" << endl;
    
    vector<TestMove> v;
    v.reserve(2);  // 预分配空间
    
    cout << "\n插入第一个元素..." << endl;
    v.push_back(TestMove("first"));
    
    cout << "\n插入第二个元素（触发重新分配）..." << endl;
    v.push_back(TestMove("second"));
    
    // 移动语义应该被调用，因为移动构造函数是noexcept
    // 如果没有noexcept声明，vector可能选择拷贝
    
    return 0;
}
```

#### 4.7.6 noexcept运算符

**noexcept(expression)** 可以在编译时检查表达式是否可能抛出异常。

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

struct A {
    int data = 0;
};

struct B {
    B() noexcept(false) {}  // 可能抛出异常
};

int safe_func() noexcept {
    return 42;
}

int unsafe_func() {
    throw 1;
    return 0;
}

int main() {
    cout << "=== noexcept运算符 ===" << endl;
    
    // 检查类型
    cout << "int是noexcept? " << noexcept(int()) << endl;
    cout << "A是noexcept? " << noexcept(A()) << endl;
    cout << "B是noexcept? " << noexcept(B()) << endl;
    
    // 检查函数
    cout << "safe_func是noexcept? " << noexcept(safe_func()) << endl;
    cout << "unsafe_func是noexcept? " << noexcept(unsafe_func()) << endl;
    
    // 在条件中使用
    if constexpr (noexcept(safe_func())) {
        cout << "safe_func保证不抛异常" << endl;
    }
    else {
        cout << "safe_func可能抛异常" << endl;
    }
    
    return 0;
}
```

#### 4.7.7 析构函数与noexcept

**重要规则：C++11起，析构函数默认是noexcept的！**

```cpp
#include <iostream>
#include <exception>
using namespace std;

class GoodDestructor {
public:
    ~GoodDestructor() noexcept {
        cout << "GoodDestructor析构" << endl;
    }
};

class BadDestructor {
public:
    ~BadDestructor() noexcept(false) {  // 允许抛出异常
        cout << "BadDestructor析构" << endl;
        // 这里可以抛出异常
    }
};

int main() {
    cout << "=== 析构函数与noexcept ===" << endl;
    
    cout << "GoodDestructor的析构函数是noexcept? " 
         << noexcept(decltype(declval<GoodDestructor>())) << endl;
    cout << "BadDestructor的析构函数是noexcept? " 
         << noexcept(decltype(declval<BadDestructor>())) << endl;
    
    return 0;
}
```

#### 4.7.8 noexcept与异常传播

```cpp
#include <iostream>
#include <exception>
using namespace std;

void inner() noexcept {
    // 这个函数声明不抛异常
    // 但如果真的抛出异常，会调用terminate()
    cout << "inner开始" << endl;
    throw runtime_error("在noexcept函数中抛出！");
    cout << "inner结束（不会执行）" << endl;
}

void outer() {
    try {
        inner();
    }
    catch (...) {
        cout << "outer捕获到异常（不会执行到）" << endl;
    }
}

int main() {
    cout << "=== noexcept与异常传播 ===" << endl;
    
    try {
        outer();
    }
    catch (...) {
        cout << "main捕获到异常" << endl;
    }
    
    return 0;
}
```

**输出：**
```
=== noexcept与异常传播 ===
inner开始
terminate called after throwing an instance of 'std::runtime_error'
  what(): 在noexcept函数中抛出！
已放弃(吐核)
```

#### 4.7.9 实际应用建议

**应该使用noexcept的场景：**

```cpp
// 1. 简单的getter函数
class Point {
    int x, y;
public:
    int get_x() const noexcept { return x; }
    int get_y() const noexcept { return y; }
};

// 2. 移动构造函数和移动赋值运算符
class Buffer {
    char* data;
    size_t size;
public:
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }
    
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
        }
        return *this;
    }
};

// 3. 基本的工具函数
int max_int(int a, int b) noexcept { return a > b ? a : b; }
double abs_double(double x) noexcept { return x >= 0 ? x : -x; }
```

**不应该使用noexcept的场景：**

```cpp
// 1. 内部可能调用会抛异常的函数
void process_data() noexcept {  // 危险！
    string s = get_user_input();  // get_user_input可能抛异常
    parse_data(s);                 // parse_data可能抛异常
}

// 2. 分配内存的函数
int* allocate_array(size_t n) noexcept {  // 不合适！
    return new int[n];  // new可能抛bad_alloc
}

// 3. 涉及I/O的函数
void write_log(const string&) noexcept {  // 不合适！
    ofstream file("log.txt");  // 可能抛异常
    file << "log entry";        // 可能抛异常
}
```

#### 4.7.10 常见错误

**错误1：在noexcept函数中抛出异常**
```cpp
void dangerous() noexcept {
    throw runtime_error("危险！");
    // 程序会直接terminate()
}
```

**错误2：过度使用noexcept**
```cpp
// 不是所有函数都应该noexcept
void process_file(const string& filename) noexcept {  // 不合适！
    // 如果文件不存在，这里会出问题
}
```

**错误3：忘记考虑间接调用**
```cpp
void看起来简单() noexcept {  // 看起来简单
    auto ptr = make_unique<int>();  // 但unique_ptr可能抛bad_alloc！
}
```

#### 4.7.11 练习题

**练习7.1：noexcept判断**
判断以下函数是否应该是noexcept：
1. 返回vector大小的函数
2. 计算两个整数最大值的函数
3. 打开文件的函数
4. 复制字符串的函数
5. 释放内存的函数

**练习7.2：实现noexcept函数**
实现一个`string_view`类的基本功能，成员函数尽可能标记为noexcept。

**练习7.3：noexcept与移动语义**
创建一个类，实现移动构造函数和移动赋值运算符，正确使用noexcept，并测试vector中使用该类时的行为。

---

### 4.8. 异常与析构函数

#### 4.8.1 析构函数与异常的基本规则

**核心规则：析构函数不应该抛出异常！**

```cpp
class Dangerous {
public:
    ~Dangerous() {
        // 危险！析构函数中抛出异常
        throw runtime_error("析构函数中的异常！");
    }
};

int main() {
    try {
        Dangerous d;  // 对象构造
    }  // 这里析构函数被调用
    catch (...) {
        // 可能导致未定义行为
    }
}
```

**为什么析构函数不能抛异常？**

1. **析构函数在对象生命周期结束时自动调用**
2. **堆栈展开过程中可能已经在处理另一个异常**
3. **同时处理两个异常会导致程序terminate()**

#### 4.8.2 析构函数抛异常的危险

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class Resource {
public:
    ~Resource() {
        cout << "Resource析构函数被调用" << endl;
        // 如果这里抛出异常，会有问题
    }
};

class Problematic {
    Resource r;
public:
    ~Problematic() {
        cout << "Problematic析构函数被调用" << endl;
        throw runtime_error("析构函数抛出的异常！");
    }
};

int main() {
    cout << "=== 析构函数抛异常的危险 ===" << endl;
    
    try {
        Problematic p;
        cout << "对象p被创建" << endl;
    }  // 这里会：
        // 1. 调用p的析构函数
        // 2. p的析构函数抛异常
        // 3. 异常被抛出，但此时没有try块保护
        // 4. 程序terminate()
    
    catch (...) {
        cout << "捕获到异常（不会执行到）" << endl;
    }
    
    cout << "程序结束（不会执行到）" << endl;
    return 0;
}
```

#### 4.8.3 正确使用析构函数

**规则：析构函数应该是noexcept的（默认）**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class GoodClass {
    int* data;
    size_t size;
    
public:
    GoodClass(size_t n) : size(n) {
        data = new int[n];
        cout << "分配内存" << endl;
    }
    
    // 析构函数：不应该抛异常
    ~GoodClass() noexcept {
        cout << "释放内存" << endl;
        // 不会抛出异常的操作
        delete[] data;
    }
    
    // 安全的清理方法（供用户调用）
    void close() {
        if (data) {
            delete[] data;
            data = nullptr;
            size = 0;
        }
    }
};

int main() {
    cout << "=== 正确的析构函数使用 ===" << endl;
    
    GoodClass* obj = new GoodClass(100);
    
    try {
        // 使用对象...
        throw runtime_error("使用过程中出错");
    }
    catch (...) {
        cout << "捕获异常，清理资源" << endl;
        obj->close();  // 使用安全的清理方法
        delete obj;
    }
    
    return 0;
}
```

#### 4.8.4 RAII模式（资源获取即初始化）

**核心思想：将资源管理与对象生命周期绑定**

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

// RAII文件包装类
class FileHandle {
    ifstream file;
    string filename;
    bool is_open;
    
public:
    FileHandle(const string& name) : filename(name), is_open(false) {
        file.open(name);
        if (!file.is_open()) {
            throw runtime_error("无法打开文件：" + filename);
        }
        is_open = true;
        cout << "文件已打开：" << filename << endl;
    }
    
    // RAII：析构函数自动关闭文件
    ~FileHandle() noexcept {
        if (is_open) {
            cout << "文件自动关闭：" << filename << endl;
            file.close();
        }
    }
    
    void read_all() {
        string line;
        while (getline(file, line)) {
            cout << line << endl;
        }
    }
    
    // 禁止拷贝
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
};

int main() {
    cout << "=== RAII模式 ===" << endl;
    
    try {
        FileHandle fh("test.txt");  // 打开文件
        fh.read_all();
        // 即使这里抛异常，文件也会被正确关闭
    }
    catch (runtime_error& e) {
        cout << "错误：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

#### 4.8.5 构造函数失败与异常

**构造函数中抛出异常是合法的，但要注意清理**

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

class PartialConstruct {
    int* data1;
    int* data2;
    
public:
    PartialConstruct(size_t size) : data1(nullptr), data2(nullptr) {
        cout << "分配data1..." << endl;
        data1 = new int[size];
        
        cout << "分配data2..." << endl;
        data2 = new int[size];
        
        // 如果这里失败，会导致data1泄漏！
        // 因为data2分配失败，但data1已经分配了
        if (size > 1000000) {
            throw runtime_error("大小超出限制");
        }
    }
    
    ~PartialConstruct() {
        delete[] data1;  // 如果data1没分配呢？
        delete[] data2;
    }
};

// 正确的做法：初始化列表+清理
class SafeConstruct {
    int* data1;
    int* data2;
    
public:
    SafeConstruct(size_t size) : data1(nullptr), data2(nullptr) {
        try {
            cout << "分配data1..." << endl;
            data1 = new int[size];
            
            cout << "分配data2..." << endl;
            data2 = new int[size];
            
            if (size > 1000000) {
                throw runtime_error("大小超出限制");
        }
        }
        catch (...) {
            // 清理已分配的资源
            delete[] data1;
            delete[] data2;
            data1 = nullptr;
            data2 = nullptr;
            throw;  // 重新抛出异常
        }
    }
    
    ~SafeConstruct() {
        delete[] data1;
        delete[] data2;
    }
};

int main() {
    cout << "=== 构造函数与异常 ===" << endl;
    
    cout << "\n--- 测试不安全的构造 ---" << endl;
    try {
        PartialConstruct pc(10);
    }
    catch (runtime_error& e) {
        cout << "捕获：" << e.what() << endl;
    }
    
    cout << "\n--- 测试安全的构造 ---" << endl;
    try {
        SafeConstruct sc(10);
    }
    catch (runtime_error& e) {
        cout << "捕获：" << e.what() << endl;
    }
    
    return 0;
}
```

#### 4.8.6 资源清理的异常安全

**三种异常安全级别：**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 基本保证：异常后对象仍然有效
class BasicGuarantee {
    int* data;
public:
    void do_something() {
        if (rand() % 2 == 0) {
            throw runtime_error("操作失败");
        }
        // 即使失败，data仍然有效
    }
    
    BasicGuarantee() : data(new int[100]) {}
    ~BasicGuarantee() { delete[] data; }
};

// 强保证：操作要么成功，要么完全回滚
class StrongGuarantee {
    int* old_data;
    int* new_data;
    bool committed;
    
public:
    void operation() {
        // 保存旧状态
        old_data = new int[100];
        
        // 尝试新操作
        try {
            new_data = new int[100];
            // 修改new_data...
            committed = true;
        }
        catch (...) {
            // 完全回滚
            delete[] new_data;
            delete[] old_data;
            throw;
        }
        
        // 提交
        delete[] old_data;
        old_data = nullptr;
    }
    
    ~StrongGuarantee() {
        delete[] old_data;
        delete[] new_data;
    }
};

// 不抛异常保证：函数不会抛异常
class NoThrow {
public:
    int get_value() const noexcept {
        return 42;
    }
};

int main() {
    cout << "=== 异常安全级别 ===" << endl;
    cout << "1. 基本保证：对象有效，但状态可能变化" << endl;
    cout << "2. 强保证：要么成功，要么完全回滚" << endl;
    cout << "3. 不抛异常保证：函数绝不抛异常" << endl;
    return 0;
}
```

#### 4.8.7 异常安全与智能指针

```cpp
#include <iostream>
#include <memory>
#include <stdexcept>
using namespace std;

// 使用智能指针自动管理内存
class ResourceManager {
    // 智能指针自动清理，不需要析构函数操心
    unique_ptr<int[]> data1;
    unique_ptr<int[]> data2;
    unique_ptr<double[]> data3;
    
public:
    ResourceManager(size_t size) {
        data1 = make_unique<int[]>(size);
        data2 = make_unique<int[]>(size);
        
        // 如果这里抛异常
        if (size > 1000000) {
            throw runtime_error("大小超出限制");
        }
        
        data3 = make_unique<double[]>(size);
    }
    
    // 智能指针自动清理，不需要手动delete
    // 即使构造函数中抛异常，已分配的内存也会被清理
};

int main() {
    cout << "=== 智能指针与异常安全 ===" << endl;
    
    try {
        ResourceManager rm(100);
    }
    catch (runtime_error& e) {
        cout << "捕获异常：" << e.what() << endl;
        // 不用担心内存泄漏，智能指针已经清理
    }
    
    return 0;
}
```

#### 4.8.8 常见错误

**错误1：析构函数抛异常**
```cpp
class Bad {
public:
    ~Bad() {
        // 危险！不要这样做
        if (error_occurred) {
            throw runtime_error("析构函数错误");
        }
    }
};
```

**错误2：忘记处理构造函数中的异常**
```cpp
class Risky {
    int* data;
public:
    Risky() {
        data = new int[100];
        // 如果后面抛异常，data会泄漏
        throw runtime_error("初始化失败");
    }
    ~Risky() { delete[] data; }  // data可能从未分配！
};
```

**错误3：使用裸指针而不是智能指针**
```cpp
class OldStyle {
    int* data;
public:
    OldStyle() : data(new int[100]) {}
    ~OldStyle() { delete[] data; }  // 需要手动处理异常
};

class NewStyle {
    // 异常安全：自动管理
    unique_ptr<int[]> data = make_unique<int[]>(100);
    // 不需要析构函数！
};
```

#### 4.8.9 最佳实践总结

```cpp
#include <iostream>
#include <memory>
#include <fstream>
using namespace std;

// 最佳实践1：析构函数不要抛异常
class BestPractice1 {
    int* data;
public:
    ~BestPractice1() noexcept {  // 明确声明
        delete[] data;
    }
};

// 最佳实践2：使用智能指针
class BestPractice2 {
    // RAII：自动管理资源
    unique_ptr<int[]> data = make_unique<int[]>(100);
    unique_ptr<fstream> file;
public:
    BestPractice2() {
        file = make_unique<fstream>("data.txt");
    }
    // 不需要析构函数！
};

// 最佳实践3：构造函数使用try-catch
class BestPractice3 {
    unique_ptr<int[]> data1;
    unique_ptr<int[]> data2;
    
public:
    BestPractice3(size_t size) try 
        : data1(make_unique<int[]>(size)),
          data2(make_unique<int[]>(size)) {
        // 构造函数的函数体
    }
    catch (...) {
        // 异常会被传播
        cout << "构造失败" << endl;
    }
};

int main() {
    cout << "=== 异常安全最佳实践 ===" << endl;
    return 0;
}
```

#### 4.8.10 练习题

**练习8.1：析构函数异常**
说明为什么析构函数不应该抛出异常？如果必须在析构函数中执行可能失败的操作，应该怎么做？

**练习8.2：实现RAII类**
实现一个`Mutex`类的RAII包装：
- 构造函数获取锁
- 析构函数释放锁
- 禁止拷贝，允许移动

**练习8.3：构造函数异常安全**
实现一个`Connection`类，构造函数连接数据库，如果失败要正确清理已分配的资源。

**练习8.4：异常安全分析**
分析以下代码的异常安全性，并提出改进建议：
```cpp
class Buffer {
    int* data;
    size_t size;
public:
    void resize(size_t new_size) {
        int* new_data = new int[new_size];
        copy(data, data + size, new_data);
        delete[] data;
        data = new_data;
        size = new_size;
    }
};
```

---

### 4.9. 实际应用场景

#### 4.9.1 文件操作异常处理

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>
#include <string>
#include <vector>
using namespace std;

// 异常类定义
class FileException : public exception {
    string filename;
    string operation;
public:
    FileException(const string& file, const string& op, const string& msg)
        : filename(file), operation(op) {
        // 组合错误消息
    }
    
    const char* what() const noexcept override {
        return ("文件操作失败：" + operation + " - " + filename).c_str();
    }
};

// 文件读取器
class FileReader {
private:
    string filename;
    ifstream file;
    vector<string> lines;
    
public:
    FileReader(const string& name) : filename(name) {
        file.open(name);
        if (!file.is_open()) {
            throw FileException(filename, "打开", "文件不存在或无法访问");
        }
    }
    
    void read_all() {
        string line;
        while (getline(file, line)) {
            lines.push_back(line);
        }
        
        if (file.bad()) {
            throw FileException(filename, "读取", "读取过程中发生错误");
        }
    }
    
    const vector<string>& get_lines() const { return lines; }
    size_t line_count() const { return lines.size(); }
    
    ~FileReader() {
        if (file.is_open()) {
            file.close();
        }
    }
};

// 文件写入器
class FileWriter {
private:
    string filename;
    ofstream file;
    
public:
    FileWriter(const string& name) : filename(name) {
        file.open(name);
        if (!file.is_open()) {
            throw FileException(filename, "创建", "无法创建文件");
        }
    }
    
    void write_line(const string& line) {
        file << line << '\n';
        if (file.fail()) {
            throw FileException(filename, "写入", "写入失败");
        }
    }
    
    void write_lines(const vector<string>& lines) {
        for (const auto& line : lines) {
            write_line(line);
        }
    }
    
    ~FileWriter() {
        if (file.is_open()) {
            file.close();
        }
    }
};

int main() {
    cout << "=== 文件操作异常处理 ===" << endl;
    
    // 测试读取不存在的文件
    cout << "\n--- 测试读取不存在的文件 ---" << endl;
    try {
        FileReader reader("nonexistent.txt");
        reader.read_all();
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    catch (exception& e) {
        cout << "其他异常：" << e.what() << endl;
    }
    
    // 测试创建和写入文件
    cout << "\n--- 测试写入文件 ---" << endl;
    try {
        FileWriter writer("output.txt");
        writer.write_line("第一行数据");
        writer.write_line("第二行数据");
        writer.write_line("第三行数据");
        cout << "写入成功" << endl;
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    // 测试读取刚写入的文件
    cout << "\n--- 测试读取刚写入的文件 ---" << endl;
    try {
        FileReader reader("output.txt");
        reader.read_all();
        cout << "读取到 " << reader.line_count() << " 行数据：" << endl;
        for (const auto& line : reader.get_lines()) {
            cout << "  " << line << endl;
        }
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

#### 4.9.2 网络请求异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <map>
using namespace std;

// 网络异常定义
class NetworkException : public exception {
public:
    enum class ErrorCode {
        CONNECTION_FAILED,
        TIMEOUT,
        NOT_FOUND,
        SERVER_ERROR,
        UNKNOWN
    };
    
private:
    ErrorCode code;
    string message;
    string url;
    
public:
    NetworkException(ErrorCode c, const string& msg, const string& u = "")
        : code(c), message(msg), url(u) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
    string get_url() const { return url; }
};

// 模拟HTTP响应
struct HttpResponse {
    int status_code;
    string body;
    bool success;
};

// 模拟网络请求
class HttpClient {
private:
    string base_url;
    int timeout_ms;
    map<string, string> headers;
    
public:
    HttpClient(const string& base) : base_url(base), timeout_ms(30000) {}
    
    void set_header(const string& key, const string& value) {
        headers[key] = value;
    }
    
    HttpResponse get(const string& path) {
        // 模拟网络请求
        cout << "发送GET请求：" << base_url << path << endl;
        
        // 模拟各种错误情况
        if (path == "/notfound") {
            throw NetworkException(
                NetworkException::ErrorCode::NOT_FOUND,
                "资源不存在",
                base_url + path
            );
        }
        
        if (path == "/servererror") {
            throw NetworkException(
                NetworkException::ErrorCode::SERVER_ERROR,
                "服务器内部错误",
                base_url + path
            );
        }
        
        if (path == "/timeout") {
            throw NetworkException(
                NetworkException::ErrorCode::TIMEOUT,
                "请求超时",
                base_url + path
            );
        }
        
        // 正常响应
        HttpResponse resp;
        resp.status_code = 200;
        resp.body = "{\"message\": \"success\"}";
        resp.success = true;
        return resp;
    }
};

// 错误处理函数
void handle_network_error(const NetworkException& e) {
    cerr << "网络错误发生！" << endl;
    cerr << "URL: " << e.get_url() << endl;
    cerr << "错误类型: ";
    
    switch (e.get_code()) {
        case NetworkException::ErrorCode::CONNECTION_FAILED:
            cerr << "连接失败";
            break;
        case NetworkException::ErrorCode::TIMEOUT:
            cerr << "请求超时";
            break;
        case NetworkException::ErrorCode::NOT_FOUND:
            cerr << "资源不存在";
            break;
        case NetworkException::ErrorCode::SERVER_ERROR:
            cerr << "服务器错误";
            break;
        default:
            cerr << "未知错误";
    }
    cerr << endl;
    
    cerr << "建议: ";
    if (e.get_code() == NetworkException::ErrorCode::TIMEOUT) {
        cerr << "增加超时时间或检查网络连接" << endl;
    }
    else if (e.get_code() == NetworkException::ErrorCode::SERVER_ERROR) {
        cerr << "服务器端问题，稍后重试" << endl;
    }
    else {
        cerr << "检查请求参数或网络状态" << endl;
    }
}

int main() {
    cout << "=== 网络请求异常处理 ===" << endl;
    
    HttpClient client("https://api.example.com");
    
    // 测试各种错误
    vector<string> test_paths = {
        "/success",
        "/notfound", 
        "/servererror",
        "/timeout"
    };
    
    for (const auto& path : test_paths) {
        cout << "\n--- 请求: " << path << " ---" << endl;
        try {
            HttpResponse resp = client.get(path);
            cout << "响应状态码: " << resp.status_code << endl;
            cout << "响应内容: " << resp.body << endl;
        }
        catch (NetworkException& e) {
            handle_network_error(e);
        }
        catch (exception& e) {
            cerr << "其他异常: " << e.what() << endl;
        }
    }
    
    return 0;
}
```

#### 4.9.3 数据库操作异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// 数据库异常
class DatabaseException : public exception {
public:
    enum class ErrorCode {
        CONNECTION_FAILED,
        QUERY_ERROR,
        CONSTRAINT_VIOLATION,
        DUPLICATE_KEY,
        TRANSACTION_FAILED,
        TIMEOUT
    };
    
private:
    ErrorCode code;
    string message;
    string query;
    
public:
    DatabaseException(ErrorCode c, const string& msg, const string& q = "")
        : code(c), message(msg), query(q) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
    string get_query() const { return query; }
};

// 模拟数据库连接
class DatabaseConnection {
private:
    string connection_string;
    bool is_connected;
    bool in_transaction;
    
public:
    DatabaseConnection(const string& conn_str) 
        : connection_string(conn_str), is_connected(false), in_transaction(false) {
        // 模拟连接数据库
        cout << "连接到数据库: " << connection_string << endl;
        
        if (conn_str.empty()) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "连接字符串不能为空"
            );
        }
        
        if (conn_str == "invalid") {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "无法连接到数据库服务器"
            );
        }
        
        is_connected = true;
        cout << "连接成功" << endl;
    }
    
    // 执行查询
    void execute(const string& sql) {
        if (!is_connected) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "未连接到数据库"
            );
        }
        
        cout << "执行SQL: " << sql << endl;
        
        // 模拟各种SQL错误
        if (sql.find("INSERT") != string::npos && sql.find("duplicate") != string::npos) {
            throw DatabaseException(
                DatabaseException::ErrorCode::DUPLICATE_KEY,
                "违反唯一约束：记录已存在",
                sql
            );
        }
        
        if (sql.find("SELECT") != string::npos && sql.find("invalid") != string::npos) {
            throw DatabaseException(
                DatabaseException::ErrorCode::QUERY_ERROR,
                "SQL语法错误",
                sql
            );
        }
    }
    
    // 事务管理
    void begin_transaction() {
        if (!is_connected) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "未连接到数据库"
            );
        }
        in_transaction = true;
        cout << "事务开始" << endl;
    }
    
    void commit() {
        if (!in_transaction) {
            throw DatabaseException(
                DatabaseException::ErrorCode::TRANSACTION_FAILED,
                "没有活跃的事务"
            );
        }
        in_transaction = false;
        cout << "事务提交" << endl;
    }
    
    void rollback() {
        in_transaction = false;
        cout << "事务回滚" << endl;
    }
    
    ~DatabaseConnection() {
        if (in_transaction) {
            cout << "警告：事务未提交，执行自动回滚" << endl;
            rollback();
        }
        if (is_connected) {
            cout << "关闭数据库连接" << endl;
            is_connected = false;
        }
    }
};

// 模拟用户表操作
class UserRepository {
private:
    shared_ptr<DatabaseConnection> db;
    
public:
    UserRepository(shared_ptr<DatabaseConnection> conn) : db(conn) {}
    
    void create_user(const string& username, const string& email) {
        // 检查参数
        if (username.empty()) {
            throw invalid_argument("用户名不能为空");
        }
        if (email.empty()) {
            throw invalid_argument("邮箱不能为空");
        }
        
        // 执行插入
        string sql = "INSERT INTO users (username, email) VALUES ('" 
                   + username + "', '" + email + "')";
        db->execute(sql);
        cout << "用户创建成功: " << username << endl;
    }
    
    void create_user_transaction(const string& username, const string& email) {
        try {
            db->begin_transaction();
            
            // 创建用户
            create_user(username, email);
            
            // 创建相关记录...
            db->execute("INSERT INTO user_profiles (username) VALUES ('" + username + "')");
            
            db->commit();
        }
        catch (...) {
            db->rollback();
            throw;
        }
    }
};

int main() {
    cout << "=== 数据库操作异常处理 ===" << endl;
    
    // 测试1：连接失败
    cout << "\n--- 测试1：无效连接 ---" << endl;
    try {
        DatabaseConnection db1("invalid");
    }
    catch (DatabaseException& e) {
        cerr << "连接失败：" << e.what() << endl;
    }
    
    // 测试2：创建用户（重复键）
    cout << "\n--- 测试2：重复键错误 ---" << endl;
    try {
        auto db2 = make_shared<DatabaseConnection>("mysql://localhost/testdb");
        UserRepository repo(db2);
        repo.create_user("admin", "admin@example.com");
        repo.create_user("admin", "admin2@example.com");  // 重复！
    }
    catch (DatabaseException& e) {
        cerr << "数据库错误：" << e.what() << endl;
        if (e.get_code() == DatabaseException::ErrorCode::DUPLICATE_KEY) {
            cerr << "建议：用户名已存在，请使用其他用户名" << endl;
        }
    }
    
    // 测试3：事务回滚
    cout << "\n--- 测试3：事务回滚 ---" << endl;
    try {
        auto db3 = make_shared<DatabaseConnection>("mysql://localhost/testdb");
        UserRepository repo(db3);
        repo.create_user_transaction("test", "test@example.com");
    }
    catch (DatabaseException& e) {
        cerr << "事务失败：" << e.what() << endl;
        cerr << "所有更改已被回滚" << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

#### 4.9.4 配置解析异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <map>
#include <sstream>
using namespace std;

// 配置异常
class ConfigException : public exception {
public:
    enum class ErrorCode {
        FILE_NOT_FOUND,
        SYNTAX_ERROR,
        TYPE_MISMATCH,
        MISSING_REQUIRED,
        INVALID_VALUE
    };
    
private:
    ErrorCode code;
    string message;
    int line_number;
    
public:
    ConfigException(ErrorCode c, const string& msg, int line = 0)
        : code(c), message(msg), line_number(line) {}
    
    const char* what() const noexcept override {
        if (line_number > 0) {
            return ("[第" + to_string(line_number) + "行] " + message).c_str();
        }
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
};

// 配置解析器
class ConfigParser {
private:
    map<string, string> config_data;
    
    void trim(string& s) {
        // 去除首尾空白
        size_t start = s.find_first_not_of(" \t\r\n");
        size_t end = s.find_last_not_of(" \t\r\n");
        if (start != string::npos) {
            s = s.substr(start, end - start + 1);
        }
    }
    
    void parse_line(const string& line, int line_num) {
        string trimmed = line;
        trim(trimmed);
        
        // 跳过空行和注释
        if (trimmed.empty() || trimmed[0] == '#' || trimmed[0] == ';') {
            return;
        }
        
        // 解析 key = value
        size_t eq_pos = trimmed.find('=');
        if (eq_pos == string::npos) {
            throw ConfigException(
                ConfigException::ErrorCode::SYNTAX_ERROR,
                "缺少等号: " + line,
                line_num
            );
        }
        
        string key = trimmed.substr(0, eq_pos);
        string value = trimmed.substr(eq_pos + 1);
        
        trim(key);
        trim(value);
        
        if (key.empty()) {
            throw ConfigException(
                ConfigException::ErrorCode::SYNTAX_ERROR,
                "配置键不能为空: " + line,
                line_num
            );
        }
        
        config_data[key] = value;
    }
    
public:
    void parse(const string& content) {
        istringstream stream(content);
        string line;
        int line_num = 0;
        
        while (getline(stream, line)) {
            line_num++;
            parse_line(line, line_num);
        }
    }
    
    string get_string(const string& key) {
        if (config_data.find(key) == config_data.end()) {
            throw ConfigException(
                ConfigException::ErrorCode::MISSING_REQUIRED,
                "缺少必需的配置项: " + key
            );
        }
        return config_data[key];
    }
    
    int get_int(const string& key) {
        string value = get_string(key);
        try {
            return stoi(value);
        }
        catch (...) {
            throw ConfigException(
                ConfigException::ErrorCode::TYPE_MISMATCH,
                "配置项 " + key + " 应该是整数，实际是: " + value
            );
        }
    }
    
    double get_double(const string& key) {
        string value = get_string(key);
        try {
            return stod(value);
        }
        catch (...) {
            throw ConfigException(
                ConfigException::ErrorCode::TYPE_MISMATCH,
                "配置项 " + key + " 应该是数字，实际是: " + value
            );
        }
    }
    
    bool get_bool(const string& key) {
        string value = get_string(key);
        if (value == "true" || value == "1" || value == "yes" || value == "on") {
            return true;
        }
        if (value == "false" || value == "0" || value == "no" || value == "off") {
            return false;
        }
        throw ConfigException(
            ConfigException::ErrorCode::TYPE_MISMATCH,
            "配置项 " + key + " 应该是布尔值，实际是: " + value
        );
    }
};

int main() {
    cout << "=== 配置解析异常处理 ===" << endl;
    
    // 正确的配置文件内容
    string valid_config = R"(
# 服务器配置
host = localhost
port = 8080
debug = true
timeout = 30.5
max_connections = 100
)";

    cout << "\n--- 测试1：解析正确配置 ---" << endl;
    try {
        ConfigParser parser;
        parser.parse(valid_config);
        cout << "主机: " << parser.get_string("host") << endl;
        cout << "端口: " << parser.get_int("port") << endl;
        cout << "调试模式: " << (parser.get_bool("debug") ? "开" : "关") << endl;
        cout << "超时时间: " << parser.get_double("timeout") << "秒" << endl;
        cout << "最大连接数: " << parser.get_int("max_connections") << endl;
    }
    catch (ConfigException& e) {
        cerr << "配置错误：" << e.what() << endl;
    }
    
    // 错误的配置文件内容
    string invalid_config = R"(
# 错误配置
host = localhost
port = not_a_number
enabled =
max = 
)";

    cout << "\n--- 测试2：解析错误配置 ---" << endl;
    try {
        ConfigParser parser;
        parser.parse(invalid_config);
        // 尝试获取错误类型的值
        int port = parser.get_int("port");
    }
    catch (ConfigException& e) {
        cerr << "配置错误：" << e.what() << endl;
        
        if (e.get_code() == ConfigException::ErrorCode::TYPE_MISMATCH) {
            cerr << "提示：请检查配置项的类型是否正确" << endl;
        }
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

#### 4.9.5 游戏开发中的异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// 游戏异常定义
class GameException : public exception {
public:
    enum class ErrorCode {
        INVALID_MOVE,
        OUT_OF_BOUNDS,
        RESOURCE_NOT_FOUND,
        SAVE_FAILED,
        LOAD_FAILED,
        CHEATING_DETECTED
    };
    
private:
    ErrorCode code;
    string message;
    
public:
    GameException(ErrorCode c, const string& msg) : code(c), message(msg) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
};

// 位置类
struct Position {
    int x, y;
    
    Position(int x = 0, int y = 0) : x(x), y(y) {}
    
    bool is_valid(int board_width, int board_height) const {
        return x >= 0 && x < board_width && y >= 0 && y < board_height;
    }
};

// 棋子类
enum class Player { NONE, BLACK, WHITE };

// 游戏板
class GameBoard {
private:
    static const int WIDTH = 8;
    static const int HEIGHT = 8;
    Player board[WIDTH][HEIGHT];
    
public:
    GameBoard() {
        for (int i = 0; i < WIDTH; i++) {
            for (int j = 0; j < HEIGHT; j++) {
                board[i][j] = Player::NONE;
            }
        }
    }
    
    void place_stone(int x, int y, Player player) {
        // 检查坐标是否有效
        if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT) {
            throw GameException(
                GameException::ErrorCode::OUT_OF_BOUNDS,
                "坐标 (" + to_string(x) + ", " + to_string(y) + ") 超出棋盘范围"
            );
        }
        
        // 检查位置是否已被占用
        if (board[x][y] != Player::NONE) {
            throw GameException(
                GameException::ErrorCode::INVALID_MOVE,
                "位置 (" + to_string(x) + ", " + to_string(y) + ") 已被占用"
            );
        }
        
        board[x][y] = player;
    }
    
    Player get_stone(int x, int y) const {
        if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT) {
            throw GameException(
                GameException::ErrorCode::OUT_OF_BOUNDS,
                "坐标超出范围"
            );
        }
        return board[x][y];
    }
    
    void print() const {
        cout << "  0 1 2 3 4 5 6 7" << endl;
        for (int y = 0; y < HEIGHT; y++) {
            cout << y << " ";
            for (int x = 0; x < WIDTH; x++) {
                char c = '.';
                if (board[x][y] == Player::BLACK) c = 'X';
                else if (board[x][y] == Player::WHITE) c = 'O';
                cout << c << " ";
            }
            cout << endl;
        }
    }
};

// 游戏类
class Game {
private:
    GameBoard board;
    Player current_player;
    int move_count;
    
public:
    Game() : current_player(Player::BLACK), move_count(0) {}
    
    void make_move(int x, int y) {
        try {
            board.place_stone(x, y, current_player);
            move_count++;
            cout << "玩家 " << (current_player == Player::BLACK ? "黑" : "白") 
                 << " 在 (" << x << ", " << y << ") 放置棋子" << endl;
            
            // 切换玩家
            current_player = (current_player == Player::BLACK) ? 
                            Player::WHITE : Player::BLACK;
        }
        catch (GameException& e) {
            cerr << "游戏错误：" << e.what() << endl;
            
            if (e.get_code() == GameException::ErrorCode::INVALID_MOVE) {
                cerr << "提示：请选择一个空的位置" << endl;
            }
            else if (e.get_code() == GameException::ErrorCode::OUT_OF_BOUNDS) {
                cerr << "提示：请在 0-7 范围内选择坐标" << endl;
            }
            
            // 重新抛出，让调用者知道操作失败
            throw;
        }
    }
    
    void show_board() const {
        board.print();
    }
};

int main() {
    cout << "=== 游戏异常处理 ===" << endl;
    
    Game game;
    
    cout << "\n--- 测试1：正常落子 ---" << endl;
    try {
        game.make_move(3, 3);  // 黑棋
        game.make_move(4, 4);  // 白棋
        game.show_board();
    }
    catch (GameException& e) {
        cerr << e.what() << endl;
    }
    
    cout << "\n--- 测试2：重复落子 ---" << endl;
    try {
        game.make_move(3, 3);  // 尝试在已有棋子的位置落子
    }
    catch (GameException& e) {
        cerr << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n--- 测试3：越界落子 ---" << endl;
    try {
        game.make_move(10, 10);  // 超出棋盘范围
    }
    catch (GameException& e) {
        cerr << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n--- 最终棋盘状态 ---" << endl;
    game.show_board();
    
    return 0;
}
```

#### 4.9.6 异常处理的最佳实践

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// ========== 最佳实践1：明确异常规范 ==========
// 应该使用noexcept的函数要明确标记
class BestPractice1 {
public:
    // 简单的查询函数不应该抛异常
    int get_value() const noexcept { return 42; }
    bool is_valid() const noexcept { return true; }
    
    // 可能失败的函数不应该标记noexcept
    void process_data() /* 可以抛异常 */ {
        throw runtime_error("处理失败");
    }
};

// ========== 最佳实践2：使用智能指针管理资源 ==========
class BestPractice2 {
    // 使用unique_ptr自动管理内存
    unique_ptr<int[]> data;
    
public:
    BestPractice2(size_t size) : data(make_unique<int[]>(size)) {}
    // 不需要析构函数！unique_ptr会自动释放内存
};

// ========== 最佳实践3：使用RAII管理资源 ==========
class BestPractice3 {
    FILE* file;
    
public:
    BestPractice3(const string& filename) : file(nullptr) {
        file = fopen(filename.c_str(), "r");
        if (!file) {
            throw runtime_error("无法打开文件");
        }
    }
    
    // 析构函数自动关闭文件
    ~BestPractice3() noexcept {
        if (file) {
            fclose(file);
        }
    }
    
    // 禁用拷贝
    BestPractice3(const BestPractice3&) = delete;
    BestPractice3& operator=(const BestPractice3&) = delete;
};

// ========== 最佳实践4：提供备选方案 ==========
class BestPractice4 {
public:
    // 方案1：可能失败
    string load_premium_data() {
        throw runtime_error("高级数据加载失败");
    }
    
    // 方案2：提供备选
    string load_data_with_fallback() {
        try {
            return load_premium_data();
        }
        catch (...) {
            cout << "使用基础数据作为备选" << endl;
            return "基础数据";
        }
    }
};

// ========== 最佳实践5：只捕获可处理的异常 ==========
class BestPractice5 {
public:
    void handle_with_care() {
        try {
            // 可能抛出各种异常
            throw runtime_error("错误");
        }
        catch (const invalid_argument& e) {
            // 只处理我们知道的异常
            cerr << "无效参数：" << e.what() << endl;
        }
        catch (const out_of_range& e) {
            // 处理越界错误
            cerr << "越界：" << e.what() << endl;
        }
        // 其他异常让它传播上去
    }
};

// ========== 最佳实践6：异常安全等级 ==========
class BestPractice6 {
    string data;
    
public:
    // 基本保证：操作后对象仍然有效
    void basic_guarantee() noexcept {
        // 即使失败，对象仍然有效
    }
    
    // 强保证：操作要么完全成功，要么完全回滚
    void strong_guarantee() {
        string old_data = data;  // 保存旧状态
        try {
            // 尝试新操作
            data = "new_value";
            // 如果这里抛异常，data已经是新值了
        }
        catch (...) {
            data = old_data;  // 回滚
            throw;
        }
    }
};

int main() {
    cout << "=== 异常处理最佳实践 ===" << endl;
    return 0;
}
```

#### 4.9.7 练习题

**练习9.1：文件解析器**
实现一个CSV文件解析器，能够处理以下异常：
- 文件不存在
- 文件格式错误
- 数据类型不匹配
- 缺少必需字段

**练习9.2：网络请求包装器**
实现一个HTTP客户端包装类，处理连接超时、服务器错误、解析错误等异常。

**练习9.3：数据库连接池**
实现一个简化的数据库连接池，包含以下异常处理：
- 连接获取失败
- 连接池已满
- 连接超时

**练习9.4：游戏引擎异常系统**
为一个简化的游戏引擎设计异常系统，包括：
- 资源加载失败
- 着色器编译错误
- 纹理格式不支持
- 内存不足

---

### 4.10. 异常处理小结

#### 10.1 核心概念回顾

| 概念 | 说明 |
|------|------|
| 异常 | 程序运行时的错误情况 |
| throw | 抛出异常的关键字 |
| try | 包含可能抛出异常的代码块 |
| catch | 捕获并处理异常 |
| noexcept | 声明函数不抛出异常 |

#### 10.2 异常处理语法

```cpp
try {
    // 可能抛出异常的代码
    throw exception_type("错误信息");
}
catch (exception_type1& e) {
    // 处理特定类型的异常
}
catch (exception_type2& e) {
    // 处理另一种异常
}
catch (...) {
    // 捕获所有剩余异常
}
```

#### 10.3 标准异常类层次

```
exception
├── logic_error
│   ├── invalid_argument
│   ├── domain_error
│   ├── length_error
│   └── out_of_range
├── runtime_error
│   ├── range_error
│   ├── overflow_error
│   └── underflow_error
└── bad_alloc
```

#### 10.4 关键规则

1. **析构函数不应抛出异常**
2. **使用智能指针管理资源**
3. **catch块从具体到一般排序**
4. **使用noexcept标记不抛异常的函数**
5. **不要用异常处理正常流程**

#### 10.5 异常安全等级

| 等级 | 说明 |
|------|------|
| 不抛异常保证 | 函数保证不抛异常 |
| 强保证 | 操作要么成功，要么完全回滚 |
| 基本保证 | 操作后对象仍然有效 |

#### 10.6 本章练习答案

**练习1.1：什么是异常**
异常是程序在运行过程中遇到的意外情况或错误状态。使用异常处理可以将正常逻辑和错误处理分离，使代码更清晰，并且强制调用者处理错误。

**练习2.2：执行流程**
输出顺序为：1 → 2 → 4 → 5
（throw后立即跳转到catch，不会输出3）

**练习3.1：抛出异常**
```cpp
int max_of_three(int a, int b, int c) {
    if (a < 0 && b < 0 && c < 0) {
        throw runtime_error("所有数都是负数");
    }
    return max(a, max(b, c));
}
```

**练习4.1：匹配结果**
- invalid_argument的catch：不匹配
- out_of_range的catch：**匹配**
- exception的catch：不匹配（已被out_of_range捕获）

**练习5.1：异常类型选择**
1. out_of_range（数组越界）
2. domain_error（负数平方根）
3. bad_alloc（内存分配）
4. invalid_argument（参数为nullptr）
5. overflow_error（整数溢出）

**练习6.1：NetworkException**
```cpp
class NetworkException : public exception {
    int error_code;
    string message;
public:
    NetworkException(int code, const string& msg) 
        : error_code(code), message(msg) {}
    const char* what() const noexcept override {
        return message.c_str();
    }
};
```

**练习7.1：noexcept判断**
1. 返回vector大小：是noexcept
2. 计算整数最大值：是noexcept
3. 打开文件：不是noexcept
4. 复制字符串：不是noexcept
5. 释放内存：是noexcept

**练习8.1：析构函数异常**
析构函数抛异常会导致双重异常（已有异常时再抛异常），程序会terminate()。如果必须在析构函数中执行可能失败的操作，应该：
1. 使用noexcept(false)显式声明
2. 使用try-catch捕获所有异常
3. 记录错误但不抛出

---

## 5. 命名空间

### 5.1 什么是命名空间？

**通俗理解**：命名空间就像给代码里的名字贴上"姓名牌"，避免大家重名打架。

> 想象一个大型会议：
> - 有两个叫"张三"的人
> - 一个来自北京，一个来自上海
> - 我们不能说"张三"，要说"北京的张三"或"上海的张三"
> - **命名空间**就是这里的"北京""上海"标签

### 5.2 为什么需要命名空间？

**问题场景**：当程序规模变大时，不同库可能定义同名的函数或类。

```cpp
// 库A定义了一个函数
void error() {
    cout << "库A的错误" << endl;
}

// 库B也定义了一个函数
void error() {
    cout << "库B的错误" << endl;
}

int main() {
    error();  // 编译器蒙了：到底调用哪个？
    return 0;
}
```

**解决方案**：用命名空间区分

```cpp
#include <iostream>
using namespace std;

namespace LibraryA {
    void error() {
        cout << "库A的错误" << endl;
    }
}

namespace LibraryB {
    void error() {
        cout << "库B的错误" << endl;
    }
}

int main() {
    LibraryA::error();  // 明确指定用库A的版本
    LibraryB::error();  // 明确指定用库B的版本
    
    return 0;
}
```

### 5.3 命名空间的三种使用方式

```cpp
#include <iostream>
using namespace std;

namespace School {
    string name = "电子科技大学";
    
    void printInfo() {
        cout << "学校名称: " << name << endl;
    }
    
    namespace Class {
        string name = "电子一班";
        
        void printInfo() {
            cout << "班级名称: " << name << endl;
        }
    }
}

int main() {
    // 方式1：完全限定名（最清晰，推荐）
    cout << School::name << endl;
    School::printInfo();
    School::Class::printInfo();  // 嵌套命名空间
    
    // 方式2：using声明（引入单个名字）
    using School::name;
    cout << name << endl;  // 不需要 School:: 了
    
    using School::printInfo;
    printInfo();  // 直接调用
    
    // 方式3：using编译指令（引入整个命名空间）
    using namespace School;
    cout << name << endl;  // 也能用
    printInfo();           // 也能用
    
    return 0;
}
```

### 5.4 标准命名空间 std

C++标准库的所有内容都放在 `std` 命名空间里：

```cpp
#include <iostream>    // std::cout, std::endl 在这里
#include <vector>       // std::vector 在这里
#include <string>       // std::string 在这里

int main() {
    // 不写 using namespace std; 时：
    std::cout << "Hello" << std::endl;
    std::vector<int> nums;
    std::string name = "Alice";
    
    // 写 using namespace std; 后：
    using namespace std;
    cout << "Hello" << endl;  // 简化写法
    vector<int> nums2;
    string name2 = "Bob";
    
    return 0;
}
```

### 5.5 无名命名空间

匿名命名空间用于限制名字的作用域只在当前文件内：

```cpp
#include <iostream>
using namespace std;

// 这个函数只能在这个文件内使用
namespace {
    void helper() {
        cout << "我是内部辅助函数" << endl;
    }
    
    const int MAX = 1000;  // 这个常量也只能在这个文件内使用
}

int main() {
    helper();  // 可以调用
    cout << MAX << endl;   // 可以使用
    return 0;
}

// 其他文件无法访问 helper() 和 MAX
```

**何时使用无名命名空间**：定义只在当前文件使用的辅助函数、常量，避免名字污染全局命名空间。

### 5.6 命名空间取别名

给长名字取个短别名，使用更方便：

```cpp
namespace VeryLongNamespaceName {
    void function() {
        cout << "Hello" << endl;
    }
}

// 取别名
namespace shortName = VeryLongNamespaceName;

int main() {
    VeryLongNamespaceName::function();  // 太长
    shortName::function();              // 简洁
    return 0;
}
```

### 5.7 常见错误

```cpp
// ❌ 错误1：头文件中慎用 using namespace std
// mylib.h
#include <vector>
using namespace std;  // 糟糕！会污染用户命名空间

// ✅ 正确做法：在源文件中使用
// mylib.h
#include <vector>
namespace mylib {
    using std::vector;  // 只引入需要的
}

// ✅ 正确做法：或在函数内部使用
void myFunction() {
    using namespace std;
    cout << "hi" << endl;
}

// ❌ 错误2：命名空间冲突
#include <iostream>
using namespace std;

int count = 10;  // 定义了一个全局变量

int main() {
    // count在某些头文件中可能与std::count冲突
    cout << count << endl;
    return 0;
}
```

### 5.8 练习题

```cpp
// 练习1：创建两个命名空间，分别定义同名的函数
namespace Math {
    int add(int a, int b) { return a + b; }
}

namespace AdvancedMath {
    int add(int a, int b) { return a + b + 100; }
}

// 在main函数中分别调用两个add函数

// 练习2：使用嵌套命名空间描述：学校->计算机学院->软件工程专业

// 练习3：解释为什么不应该在头文件中写 using namespace std;
```

---

## 6. 智能指针

### 6.1 什么是智能指针？

**通俗理解**：智能指针就是"会自己打扫房间的机器人"。

> 普通指针 = 你借了内存，用完得自己还
> 智能指针 = 借了内存，机器人自动还，不用你操心

### 6.2 为什么需要智能指针？

```cpp
// ❌ 普通指针的问题：容易忘记释放内存
void process() {
    int* ptr = new int[1000];
    
    if (someError) {
        return;  // 糟糕！内存泄漏了
    }
    
    delete[] ptr;  // 只有走到这里才释放
}

// ✅ 智能指针：自动释放
#include <memory>

void process() {
    unique_ptr<int[]> ptr(new int[1000]);
    
    if (someError) {
        return;  // 智能指针自动清理
    }
    
    // 不需要手动delete，自动释放
}
```

### 6.3 三种智能指针详解

#### 6.3.1 unique_ptr - 独占所有权

```cpp
#include <iostream>
#include <memory>
using namespace std;

// unique_ptr：独占式智能指针，同一时刻只能有一个指针拥有这块内存

int main() {
    // 创建unique_ptr
    unique_ptr<int> p1(new int(10));
    cout << *p1 << endl;  // 输出: 10
    
    // unique_ptr拥有独占所有权，不能复制
    // unique_ptr<int> p2 = p1;  // ❌ 编译错误！
    
    // 只能移动
    unique_ptr<int> p2 = move(p1);
    cout << *p2 << endl;  // 输出: 10
    // p1现在是空的了
    // cout << *p1 << endl;  // ❌ 运行时错误！
    
    // 使用make_unique (C++14推荐方式)
    auto p3 = make_unique<int>(20);
    auto p4 = make_unique<int[]>(5);  // 数组版本
    
    // unique_ptr销毁时自动释放内存
    return 0;
}  // p2, p3, p4 自动销毁，自动释放内存
```

#### 6.3.2 shared_ptr - 共享所有权

```cpp
#include <iostream>
#include <memory>
using namespace std;

// shared_ptr：共享式智能指针，多个指针可以共享同一块内存
// 通过引用计数追踪有多少个指针在使用

int main() {
    // 创建shared_ptr
    shared_ptr<int> p1 = make_shared<int>(100);
    cout << "引用计数: " << p1.use_count() << endl;  // 1
    
    // 可以复制，多个指针共享
    shared_ptr<int> p2 = p1;
    shared_ptr<int> p3 = p1;
    cout << "引用计数: " << p1.use_count() << endl;  // 3
    
    // 所有指针都能访问同一块内存
    cout << *p1 << endl;  // 100
    cout << *p2 << endl;  // 100
    cout << *p3 << endl;  // 100
    
    // 指针销毁时引用计数减1
    p3.reset();  // p3释放所有权
    cout << "引用计数: " << p1.use_count() << endl;  // 2
    
    p2.reset();
    cout << "引用计数: " << p1.use_count() << endl;  // 1
    
    // 最后一个shared_ptr销毁时，内存被释放
    p1.reset();
    // 到这里内存已经被释放了
    
    return 0;
}
```

#### 6.3.3 weak_ptr - 弱引用

```cpp
#include <iostream>
#include <memory>
using namespace std;

// weak_ptr：弱引用，不拥有所有权，不能直接访问
// 用于解决shared_ptr的循环引用问题

int main() {
    shared_ptr<int> sp = make_shared<int>(42);
    
    // 创建weak_ptr观察shared_ptr
    weak_ptr<int> wp = sp;
    
    cout << "shared_ptr引用计数: " << sp.use_count() << endl;  // 1
    cout << "weak_ptr是否有效: " << wp.expired() << endl;      // false
    
    // weak_ptr不能直接解引用，需要先提升为shared_ptr
    if (auto sp2 = wp.lock()) {
        cout << "通过weak_ptr访问: " << *sp2 << endl;  // 42
    }
    
    // shared_ptr销毁后，weak_ptr自动失效
    sp.reset();
    cout << "weak_ptr是否有效: " << wp.expired() << endl;  // true
    
    return 0;
}
```

### 6.4 循环引用问题（weak_ptr的用武之地）

```cpp
#include <iostream>
#include <memory>
using namespace std;

// 循环引用示例（使用weak_ptr解决）
class Parent;
class Child;

class Child {
public:
    string name;
    weak_ptr<Parent> parent;  // 用weak_ptr避免循环引用！
    
    Child(const string& n) : name(n) {}
    ~Child() { cout << name << " 被销毁" << endl; }
};

class Parent {
public:
    string name;
    shared_ptr<Child> child;  // 正常shared_ptr
    
    Parent(const string& n) : name(n) {}
    ~Parent() { cout << name << " 被销毁" << endl; }
};

int main() {
    auto p = make_shared<Parent>("父亲");
    auto c = make_shared<Child>("儿子");
    
    p->child = c;
    c->parent = p;  // weak_ptr不会增加引用计数
    
    cout << "Parent引用计数: " << p.use_count() << endl;  // 1
    cout << "Child引用计数: " << c.use_count() << endl;   // 1
    
    return 0;
}
// 正常销毁，无内存泄漏
```

### 6.5 智能指针选择指南

| 指针类型 | 使用场景 | 特点 |
|---------|---------|------|
| `unique_ptr` | **优先使用** | 轻量高效，独占所有权 |
| `shared_ptr` | 需要共享所有权时 | 有引用计数开销 |
| `weak_ptr` | 配合shared_ptr使用 | 打破循环引用 |

```cpp
// 选择原则：能用unique_ptr就不用shared_ptr

// ✅ 推荐：独占资源
unique_ptr<File> openFile(const string& path);

// ❌ 不推荐：共享资源（除非真的需要共享）
shared_ptr<Database> db = make_shared<Database>();

// ✅ 正确：观察shared_ptr但不拥有所有权
class Observer {
    weak_ptr<Subject> subject;  // 只观察，不拥有
};
```

### 6.6 常见错误

```cpp
#include <memory>
using namespace std;

// ❌ 错误1：不要用裸指针初始化多个shared_ptr
int* raw = new int(10);
shared_ptr<int> sp1(raw);
shared_ptr<int> sp2(raw);  // ❌ 双重释放！

// ✅ 正确：用make_shared
auto sp3 = make_shared<int>(10);
shared_ptr<int> sp4 = sp3;  // OK，共享同一个控制块

// ❌ 错误2：忘记weak_ptr需要lock()
weak_ptr<int> wp;
cout << *wp << endl;  // ❌ 编译错误！weak_ptr不能直接解引用

// ✅ 正确
if (auto sp = wp.lock()) {
    cout << *sp << endl;
}

// ❌ 错误3：在对象内部存储指向自己的shared_ptr
class Node {
public:
    shared_ptr<Node> next;
    shared_ptr<Node> prev;
};
// 如果Node存储shared_ptr指向自己，会导致引用计数永远不为0
```

### 6.7 练习题

```cpp
// 练习1：使用unique_ptr改写以下代码
void process() {
    int* data = new int[100];
    // ... 使用data
    delete[] data;
}

// 练习2：分析以下代码的输出
int main() {
    shared_ptr<int> p1 = make_shared<int>(10);
    shared_ptr<int> p2 = p1;
    weak_ptr<int> wp = p1;
    
    p1.reset();
    cout << wp.use_count() << endl;  // 输出什么？
    
    p2.reset();
    cout << wp.expired() << endl;    // 输出什么？
}

// 练习3：设计一个类，使用智能指针管理资源
// 要求：类内部持有动态分配的资源，在析构函数中释放
```

---

## 7. auto、范围 for、lambda

### 7.1 什么是lambda？

**通俗理解**：lambda就是"匿名函数"——不用单独定义，直接在使用的地方写函数。

> 就像：不用专门去理发店，找个Tony老师（匿名）直接剪

```cpp
// 传统方式：先定义函数
bool isEven(int n) {
    return n % 2 == 0;
}

// lambda方式：直接在使用的地方写
auto isEven = [](int n) { return n % 2 == 0; };
```

### 7.2 lambda的基本语法

```cpp
// lambda表达式的基本结构
[capture](parameters) -> return_type {
    // 函数体
}

// 最简单的lambda
auto f = []() { 
    cout << "Hello lambda!" << endl; 
};
f();  // 调用

// 带参数的lambda
auto add = [](int a, int b) { 
    return a + b; 
};
cout << add(1, 2) << endl;  // 输出: 3

// 自动推断返回类型
auto multiply = [](int a, int b) { return a * b; };  // 不需要写 -> int

// 多行函数体
auto complexFunc = [](int x) {
    int result = x * 2;
    result += 10;
    return result;
};
```

### 7.3 捕获列表详解

**为什么需要捕获**：lambda在函数外部定义时，需要"捕获"外部变量才能使用。

```cpp
int main() {
    int a = 10;
    int b = 20;
    
    // ❌ 错误：lambda不能直接访问外部变量
    // auto f = []() { return a + b; };  // 编译错误！
    
    // ✅ 正确：使用捕获列表
    auto f = [a, b]() { return a + b; };  // 捕获a和b的拷贝
    cout << f() << endl;  // 输出: 30
    
    // 捕获方式：
    // [a, b]      - 捕获a和b的拷贝（值捕获）
    // [&a, &b]    - 捕获a和b的引用（引用捕获）
    // [=]         - 捕获所有外部变量的拷贝
    // [&]         - 捕获所有外部变量的引用
    // [=, &a]     - 捕获所有变量的拷贝，但a用引用
    // [&, a]      - 捕获所有变量的引用，但a用拷贝
}
```

### 7.4 各种捕获方式示例

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    int x = 10;
    int y = 20;
    vector<int> nums = {1, 2, 3, 4, 5};
    
    // 方式1：值捕获 [a, b]
    auto byValue = [x, y]() {
        // x = 100;  // ❌ 错误：值捕获的变量是const的
        return x + y;
    };
    cout << "值捕获: " << byValue() << endl;  // 30
    
    // 方式2：引用捕获 [&a, &b]
    int z = 5;
    auto byRef = [&z]() {
        z = 100;  // ✅ 可以修改
        return z;
    };
    cout << "引用捕获: " << byRef() << endl;  // 100
    cout << "z的值: " << z << endl;           // 100（被修改了）
    
    // 方式3：[=] 捕获所有变量的拷贝
    int count = 0;
    auto captureAll = [=]() {
        return count;  // 捕获count的拷贝
    };
    cout << "=捕获: " << captureAll() << endl;
    
    // 方式4：[&] 捕获所有变量的引用
    auto captureAllRef = [&]() {
        count = 100;  // 修改会影响外部
        return count;
    };
    cout << "&捕获: " << captureAllRef() << endl;
    cout << "count: " << count << endl;  // 100
    
    // 方式5：混合捕获 [=, &变量名]
    int multiplier = 2;
    auto mixed1 = [=, &multiplier]() {
        multiplier = 10;  // multiplier用引用，可以改
        return x * multiplier;  // x, y用拷贝
    };
    
    // 方式6：混合捕获 [&, 变量名]
    auto mixed2 = [&, multiplier]() {
        // x = 5;  // ❌ x是拷贝，不能改
        multiplier = 50;  // ✅ multiplier是拷贝，可以改（但不会影响外部）
        return x * multiplier;
    };
    
    // mutable：让值捕获的变量可修改
    int data = 10;
    auto mutableLambda = [data]() mutable {
        data = 100;  // ✅ 现在可以修改了
        return data;
    };
    cout << "mutable: " << mutableLambda() << endl;  // 100
    cout << "data: " << data << endl;                 // 10（外部不变）
    
    return 0;
}
```

### 7.5 lambda的实际应用

#### 7.5.1 与STL算法配合

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>
using namespace std;

int main() {
    vector<int> scores = {85, 92, 78, 95, 88, 72, 90};
    
    // 找出不及格的成绩
    auto isFail = [](int score) { return score < 60; };
    auto it = find_if(scores.begin(), scores.end(), isFail);
    if (it != scores.end()) {
        cout << "第一个不及格: " << *it << endl;
    }
    
    // 统计80-90分的人数
    int count = count_if(scores.begin(), scores.end(), 
        [](int s) { return s >= 80 && s <= 90; });
    cout << "80-90分人数: " << count << endl;
    
    // 对所有成绩加5分
    for_each(scores.begin(), scores.end(), [](int& s) { s += 5; });
    
    // 排序：按成绩降序
    sort(scores.begin(), scores.end(), 
        [](int a, int b) { return a > b; });
    
    // 移除不及格的（用remove_if + erase）
    scores.erase(
        remove_if(scores.begin(), scores.end(), 
            [](int s) { return s < 60; }),
        scores.end()
    );
    
    return 0;
}
```

#### 7.5.2 作为回调函数

```cpp
#include <iostream>
#include <vector>
#include <functional>
using namespace std;

// 使用function作为参数，接受lambda
void processNumbers(const vector<int>& nums, 
                    function<bool(int)> filter) {
    cout << "符合条件的数: ";
    for (int n : nums) {
        if (filter(n)) {
            cout << n << " ";
        }
    }
    cout << endl;
}

int main() {
    vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 传入不同的lambda，实现不同的过滤逻辑
    processNumbers(nums, [](int n) { return n % 2 == 0; });  // 偶数
    processNumbers(nums, [](int n) { return n > 5; });        // 大于5
    processNumbers(nums, [](int n) { return n % 3 == 0; });   // 3的倍数
    
    return 0;
}
```

### 7.6 lambda与函数对象的对比

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 函数对象（仿函数）
class GreaterThan {
    int threshold;
public:
    GreaterThan(int t) : threshold(t) {}
    bool operator()(int n) const {
        return n > threshold;
    }
};

int main() {
    vector<int> nums = {1, 5, 10, 15, 20, 25};
    
    // 方式1：函数对象
    GreaterThan gt5(5);
    int count1 = count_if(nums.begin(), nums.end(), gt5);
    
    // 方式2：lambda（更简洁）
    int count2 = count_if(nums.begin(), nums.end(), 
        [](int n) { return n > 5; });
    
    // lambda的优势：代码更简洁，可读性更好
    // 函数对象的优势：可以存储状态，适合复用
    
    return 0;
}
```

### 7.7 常见错误

```cpp
#include <functional>
using namespace std;

// ❌ 错误1：捕获已销毁的变量
function<int()> createLambda() {
    int x = 10;
    // return [x]() { return x; };  // ❌ x在函数结束时销毁
    return [=]() { return x; };  // ✅ 捕获了值（拷贝）
}

// ❌ 错误2：lambda中使用未捕获的变量
int main() {
    auto f = [](int a) { return a + b; };  // ❌ b未捕获
    // 应该: [b](int a) { return a + b; }
}

// ❌ 错误3：lambda作为返回值时不写返回类型
auto wrong = [](int x) { 
    if (x > 0) return x; 
    else return -x;  // ❌ 编译器可能推断错误
};

// ✅ 正确：明确返回类型
auto correct = [](int x) -> int {
    if (x > 0) return x;
    else return -x;
};
```

### 7.8 练习题

```cpp
// 练习1：使用lambda简化以下代码
class Compare {
public:
    bool operator()(int a, int b) const {
        return a > b;
    }
};
sort(v.begin(), v.end(), Compare());

// 练习2：写一个lambda，实现阶乘
auto factorial = /* 你的代码 */;
cout << factorial(5) << endl;  // 输出120

// 练习3：使用lambda和STL算法统计字符串中元音字母的个数
vector<char> letters = {'a', 'e', 'i', 'z', 'o'};
// 统计其中元音字母的个数

// 练习4：解释以下代码的输出
int main() {
    int x = 10;
    auto f1 = [x]() { cout << x << endl; };
    auto f2 = [&x]() { cout << x << endl; };
    
    x = 20;
    f1();  // 输出什么？
    f2();  // 输出什么？
}
```

---

### 7.3 auto 关键字

#### 7.3.1 auto是什么？

**通俗理解**：auto就是"让编译器自己推断类型"，像数学里的"设未知数为x"。

```cpp
// 传统写法：自己写类型
int a = 10;
string s = "hello";
vector<int> v = {1, 2, 3};

// auto写法：编译器帮你推断
auto a = 10;           // int
auto s = "hello";      // const char*
auto v = {1, 2, 3};    // initializer_list<int>
```

#### 7.3.2 auto的使用场景

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

int main() {
    // 场景1：长类型名
    vector<pair<string, int>>::iterator it;  // 太长
    auto it2 = v.begin();                     // 简洁
    
    // 场景2：复杂表达式
    auto result = someComplexFunction();  // 不用关心返回类型
    
    // 场景3：迭代器遍历
    map<string, int> m = {{"a", 1}, {"b", 2}};
    for (auto& [key, value] : m) {  // C++17结构化绑定
        cout << key << ": " << value << endl;
    }
    
    // 场景4：lambda类型
    auto cmp = [](int a, int b) { return a > b; };
    
    // 场景5：模板类型推断
    vector<map<string, vector<int>>> complexType;
    for (const auto& item : complexType) {
        // item的类型太复杂，用auto方便
    }
    
    return 0;
}
```

#### 7.3.3 auto与指针、引用

```cpp
int x = 10;
int& ref = x;
int* ptr = &x;

// auto 推断规则
auto a1 = x;      // int（忽略引用和const）
auto a2 = ref;    // int（ref被解引用）
auto a3 = ptr;    // int*
auto& a4 = x;     // int&（保留引用）
auto* a5 = ptr;   // int*（保留指针）
const auto a6 = x; // const int（加const）
auto& a7 = ref;   // int&（保留引用）
```

#### 7.3.4 auto的注意事项

```cpp
// ❌ 错误1：auto不能用于函数参数
void func(auto x) { }  // ❌ C++20之前不允许

// ❌ 错误2：auto不能用于推导数组
int arr[10];
auto a = arr;  // int*，丢失了数组大小信息
// 如果需要保留：
int (&ra)[10] = arr;  // 引用
// 或使用模板：
template<typename T, size_t N>
void func(T (&arr)[N]) { }

// ❌ 错误3：类成员变量不能用auto（除非C++17 static）
class A {
    auto x = 10;  // ❌ C++14之前不行
    static auto y = 10;  // ✅ C++17可以
};

// ❌ 错误4：危险！容易推断错误
auto x = {1, 2};        // initializer_list<int>
auto x = {1};           // initializer_list<int>
auto x = 1;             // int

// ✅ 建议：配合具体类型使用
vector<int> v = {1, 2, 3};
auto& it = v.begin();  // 明确是引用
```

#### 7.3.5 auto与decltype对比

```cpp
int x = 10;
int& ref = x;
const int c = 20;

// decltype：保留表达式的完整类型
decltype(x) a1 = 10;     // int
decltype(ref) a2 = x;     // int&（保留引用）
decltype(c) a3 = 20;      // const int（保留const）
decltype(x + 1) a4;       // int（表达式结果是int）

// auto：忽略顶层const和引用
auto a5 = x;     // int
auto a6 = ref;   // int
auto a7 = c;     // int（去掉const）
```

#### 7.3.6 常见错误

```cpp
// ❌ 错误：过度使用auto导致可读性差
auto x = getData();  // 什么类型？看不出来

// ✅ 正确：类型明确时不用auto
int count = 0;           // 简单类型不用auto
string name = "Alice";  // 字符串不用auto

// ✅ 正确：类型复杂时用auto
vector<map<string, vector<int>>>::iterator it;
// 用auto更好：
auto it2 = v.begin();
```

#### 7.3.7 练习题

```cpp
// 练习1：判断以下auto的类型
auto a = 3.14;           // 类型是？
auto b = 3;              // 类型是？
auto c = 'A';            // 类型是？
auto d = L'B';           // 类型是？
auto e = "hello";        // 类型是？

// 练习2：写出下面代码的完整类型
vector<pair<int, string>> v;
auto it = v.begin();    // it的类型？

// 练习3：使用auto简化以下代码
for (vector<int>::iterator it = nums.begin(); it != nums.end(); ++it)
for (vector<pair<string, int>>::const_iterator cit = m.begin(); 
     cit != m.end(); ++cit)

// 练习4：解释auto和auto&的区别
int x = 10;
auto y = x;    // 修改y会影响x吗？
auto& z = x;   // 修改z会影响x吗？
```

---

### 7.4 范围 for 循环

#### 7.4.1 基本语法

**通俗理解**：范围for就是"遍历容器不用操心索引"。

```cpp
#include <iostream>
#include <vector>
#include <array>
using namespace std;

int main() {
    // 传统for循环
    vector<int> v = {1, 2, 3, 4, 5};
    for (int i = 0; i < v.size(); i++) {
        cout << v[i] << " ";
    }
    cout << endl;
    
    // 范围for循环（更简洁）
    for (int n : v) {
        cout << n << " ";
    }
    cout << endl;
    
    return 0;
}
```

#### 7.4.2 值拷贝 vs 引用

```cpp
int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    
    // 值拷贝：每个元素拷贝一份，修改不影响原数组
    for (int n : v) {
        n *= 2;  // 修改的是拷贝
    }
    // v还是 {1, 2, 3, 4, 5}
    
    // 引用：修改会影响原数组
    for (int& n : v) {
        n *= 2;  // 修改原元素
    }
    // v变成了 {2, 4, 6, 8, 10}
    
    // 常量引用：只读不修改，效率高
    for (const int& n : v) {
        cout << n << " ";
    }
    
    // auto版本
    for (auto n : v) {           // 值拷贝
        n *= 2;
    }
    for (auto& n : v) {           // 引用，可修改
        n *= 2;
    }
    for (const auto& n : v) {     // 常引用，只读，效率最高
        cout << n << " ";
    }
    
    return 0;
}
```

#### 7.4.3 与auto配合

```cpp
int main() {
    vector<string> words = {"apple", "banana", "cherry"};
    
    // auto自动推断元素类型
    for (auto word : words) {
        cout << word << endl;
    }
    
    // 数组也支持范围for
    int arr[] = {1, 2, 3, 4, 5};
    for (auto n : arr) {
        cout << n << " ";
    }
    
    // C++11 initializer_list
    for (auto n : {10, 20, 30, 40, 50}) {
        cout << n << " ";
    }
    
    return 0;
}
```

#### 7.4.4 常见错误

```cpp
// ❌ 错误1：在循环中修改容器大小导致迭代器失效
vector<int> v = {1, 2, 3, 4, 5};
for (auto n : v) {
    if (n == 3) {
        v.push_back(100);  // ❌ 可能导致崩溃
    }
}

// ✅ 正确：需要修改时用普通循环或先记录要添加的内容

// ❌ 错误2：范围for不能直接获取索引
vector<int> v = {1, 2, 3, 4, 5};
for (auto n : v) {
    // 没有索引信息
}

// ✅ 如果需要索引，用传统循环或enumerate
for (size_t i = 0; i < v.size(); i++) {
    cout << i << ": " << v[i] << endl;
}

// ❌ 错误3：使用auto时没有意识到拷贝开销
vector<HeavyObject> objects = {/* ... */};
for (auto obj : objects) {  // ❌ 大量拷贝！
    obj.process();
}
// ✅ 用常引用
for (const auto& obj : objects) {
    obj.process();
}
```

#### 7.4.5 练习题

```cpp
// 练习1：使用范围for计算vector<int>所有元素的和

// 练习2：找出vector<string>中最长的字符串

// 练习3：使用范围for将二维数组每个元素翻倍
int matrix[3][4] = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 练习4：解释为什么以下代码不能改变元素的值
vector<int> nums = {1, 2, 3};
for (auto n : nums) {
    n *= 2;
}
// 循环结束后nums的值是什么？
```

---

### 7.5 C++11/14/17 其他新特性

#### 7.5.1 nullptr - 空指针

```cpp
#include <iostream>
using namespace std;

// nullptr vs NULL

void func(int* p) {
    cout << "int* 版本" << endl;
}

void func(int n) {
    cout << "int 版本" << endl;
}

int main() {
    // ❌ NULL的问题：在C++中可能被定义为0，导致函数歧义
    // func(NULL);  // 调用哪个？可能调用int版本
    
    // ✅ nullptr：C++11引入，专门表示空指针
    int* p = nullptr;
    func(nullptr);  // 明确调用int*版本
    
    // nullptr的类型是std::nullptr_t
    nullptr_t np;
    
    return 0;
}
```

#### 7.5.2 constexpr - 编译时常量

```cpp
#include <iostream>
using namespace std;

// const vs constexpr
const int constVal = 10;        // const：运行时常量
constexpr int constexprVal = 10; // constexpr：编译时常量

// constexpr函数：在编译时就能计算结果
constexpr int square(int x) {
    return x * x;
}

int main() {
    // 编译时计算
    int arr[square(5)];  // ✅ 编译时确定数组大小
    // int arr[constVal];  // ❌ 可能不行（取决于编译器）
    
    // constexpr还可以用于类
    struct Point {
        constexpr Point(int x, int y) : x(x), y(y) {}
        int x, y;
    };
    
    constexpr Point p1(1, 2);  // 编译时创建
    cout << p1.x << ", " << p1.y << endl;
    
    return 0;
}
```

#### 7.5.3 初始化列表

```cpp
#include <iostream>
#include <vector>
#include <initializer_list>
using namespace std;

// 统一初始化语法
int main() {
    // 数组
    int arr1[] = {1, 2, 3};
    int arr2{1, 2, 3};  // C++11新语法
    
    // vector
    vector<int> v1 = {1, 2, 3, 4, 5};
    vector<string> v2{"hello", "world"};
    
    // 结构体/对象
    struct Point { int x; int y; };
    Point p1 = {1, 2};
    
    // 类成员默认值
    class A {
    public:
        int x{0};           // 默认值0
        string s{"default"};
        vector<int> v{1, 2, 3};
    };
    
    // 防止类型收窄
    // int x1 = 3.14;      // ❌ 可能丢失精度
    // int x2{3.14};       // ❌ 编译错误！防止类型收窄
    int x3 = {3};           // ✅ OK
    
    return 0;
}
```

#### 7.5.4 类型别名

```cpp
#include <iostream>
#include <vector>
#include <type_traits>
using namespace std;

// typedef（C++98）
typedef unsigned int uint_t;
typedef vector<int> IntVec;
typedef int (*FuncPtr)(int, int);

// using（C++11，推荐）
using uint = unsigned int;
using IntVector = vector<int>;
using Func = int(*)(int, int);

// using的优势：模板别名
template<typename T>
using Vec = vector<T>;

Vec<int> v1;      // vector<int>
Vec<string> v2;   // vector<string>

// typedef不能这样用
// template<typename T>
// typedef vector<T> VecT;  // ❌ 语法错误

int main() {
    uint a = 10;
    IntVector v = {1, 2, 3};
    
    return 0;
}
```

#### 7.5.5 结构化绑定（C++17）

```cpp
#include <iostream>
#include <map>
#include <tuple>
using namespace std;

int main() {
    // pair的结构化绑定
    auto [x, y] = make_pair(10, 20);
    cout << x << ", " << y << endl;
    
    // tuple的结构化绑定
    auto [a, b, c] = make_tuple(1, 2.5, "hello");
    cout << a << ", " << b << ", " << c << endl;
    
    // 数组的结构化绑定
    int arr[] = {1, 2, 3};
    auto [first, second, third] = arr;
    cout << first << ", " << second << ", " << third << endl;
    
    // 结构体/类
    struct Point { int x; int y; };
    Point p{10, 20};
    auto [px, py] = p;
    cout << px << ", " << py << endl;
    
    // map遍历
    map<string, int> m = {{"apple", 1}, {"banana", 2}};
    for (const auto& [key, value] : m) {
        cout << key << ": " << value << endl;
    }
    
    return 0;
}
```

#### 7.5.6 if/switch初始化（C++17）

```cpp
#include <iostream>
#include <map>
#include <optional>
using namespace std;

int main() {
    map<string, int> m = {{"a", 1}, {"b", 2}};
    
    // C++17之前
    auto it = m.find("a");
    if (it != m.end()) {
        cout << it->second << endl;
    }
    
    // C++17：if/switch带初始化
    if (auto it = m.find("a"); it != m.end()) {
        cout << it->second << endl;
    }
    
    // switch示例
    enum class State { START, END };
    State s = State::START;
    switch (auto old = s; s) {
        case State::START:
            cout << "开始" << endl;
            break;
        case State::END:
            cout << "结束" << endl;
            break;
    }
    
    // optional示例
    #include <optional>
    optional<int> opt = 42;
    if (auto val = opt; val.has_value()) {
        cout << *val << endl;
    }
    
    return 0;
}
```

#### 7.5.7 std::optional（C++17）

```cpp
#include <iostream>
#include <optional>
using namespace std;

// optional：表示"可能有值，也可能没值"

optional<int> findUserById(int id) {
    if (id == 1) return 100;
    if (id == 2) return 200;
    return nullopt;  // 表示没有值
}

int main() {
    auto result = findUserById(1);
    
    // 检查是否有值
    if (result.has_value()) {
        cout << "找到: " << result.value() << endl;
        cout << "或者: " << *result << endl;
    } else {
        cout << "没找到" << endl;
    }
    
    // 提供默认值
    int id = findUserById(999).value_or(-1);
    cout << "用户ID: " << id << endl;
    
    // 链式调用
    auto finalResult = findUserById(1).value_or(0) * 10;
    cout << "计算结果: " << finalResult << endl;
    
    return 0;
}
```

#### 7.5.8 std::variant（C++17）

```cpp
#include <iostream>
#include <variant>
#include <string>
using namespace std;

// variant：类型安全的union
variant<int, double, string> data;

int main() {
    data = 10;
    cout << get<int>(data) << endl;
    
    data = 3.14;
    cout << get<double>(data) << endl;
    
    data = "hello";
    cout << get<string>(data) << endl;
    
    // 安全访问
    if (holds_alternative<int>(data)) {
        cout << "是int: " << get<int>(data) << endl;
    }
    
    // visitor模式
    visitor v{
        [](int i) { cout << "int: " << i << endl; },
        [](double d) { cout << "double: " << d << endl; },
        [](const string& s) { cout << "string: " << s << endl; }
    };
    visit(v, data);
    
    return 0;
}
```

#### 7.5.9 常见错误

```cpp
// ❌ 错误1：用NULL而不是nullptr
int* p = NULL;  // ❌ C++中NULL是0，不是空指针
int* p2 = nullptr;  // ✅

// ❌ 错误2：constexpr函数写错了
constexpr int divide(int a, int b) {
    // ❌ C++14之前：constexpr函数只能一行return
    if (b == 0) return 0;
    return a / b;
}

// ✅ C++14以后可以写多行
constexpr int divide(int a, int b) {
    if (b == 0) return 0;  // C++14支持
    return a / b;
}

// ❌ 错误3：结构化绑定忘了引用
map<string, int> m = {{"a", 1}};
// for (auto [k, v] : m) { v++; }  // ❌ v是拷贝，不影响原值
for (auto& [k, v] : m) { v++; }   // ✅ 用引用
```

#### 7.5.10 练习题

```cpp
// 练习1：使用nullptr改写以下函数调用
void process(int* ptr) { }
process(NULL);  // 改为nullptr

// 练习2：用constexpr计算10的阶乘（编译时）

// 练习3：使用结构化绑定简化以下代码
pair<int, int> getMinMax(const vector<int>& v);
// 传统方式
auto result = getMinMax(v);
int minVal = result.first;
int maxVal = result.second;

// 练习4：解释为什么optional比直接返回-1更好
optional<int> findIndex(const vector<int>& v, int target);
int findIndex2(const vector<int>& v, int target);  // 返回-1表示未找到
```

---

## 8. 本章小结

- STL 是工具箱，优先用成熟工具。
- 迭代器让算法能作用于不同容器。
- `string` 比 C 字符串更友好。
- 异常处理错误，不处理懒惰。
- 智能指针负责资源释放。
- lambda 适合短小临时逻辑。

[返回主目录](../README.md)

[返回主目录](../README.md)

[返回主目录](../README.md)
