# 第7章 STL标准库

> 本章目标：学会使用 C++ 标准库工具箱。STL 不是偷懒，是站在前人写好的轮子上，不用每次都从木头开始削。

## 目录

1. STL 是什么
2. string
3. 常用容器
4. 迭代器
5. 常用算法
6. 本章小结

## 1. STL 是什么

| 组件 | 作用 |
| --- | --- |
| 容器 | 存数据，比如 `vector`、`map` |
| 迭代器 | 像指针一样遍历容器 |
| 算法 | 排序、查找、统计等 |

## 2. string

```cpp
string s = "hello";
s += " C++";
cout << s.size();
cout << s.substr(0, 5);
```

`string` 比 C 风格字符串安全和方便，不用手动管理字符数组长度。

## 3. 常用容器

| 容器 | 特点 | 适合场景 |
| --- | --- | --- |
| `vector` | 连续存储，随机访问快 | 默认首选 |
| `list` | 双向链表，插入删除方便 | 频繁中间插入删除 |
| `deque` | 双端队列 | 头尾都要操作 |
| `set` | 有序、不重复 | 去重、排序集合 |
| `map` | 键值对 | 字典、计数表 |

```cpp
vector<int> v = {3, 1, 2};
sort(v.begin(), v.end());

map<string, int> score;
score["Alice"] = 95;
```

## 4. 迭代器

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << endl;
}
```

也可以用范围 for：

```cpp
for (int x : v) {
    cout << x << endl;
}
```

注意迭代器失效：比如 `vector` 扩容后，原来的迭代器可能不能再用。它像旧地图，城市扩建后还拿着它找路，容易走进墙里。

## 5. 常用算法

```cpp
sort(v.begin(), v.end());
reverse(v.begin(), v.end());
auto it = find(v.begin(), v.end(), 3);
int total = accumulate(v.begin(), v.end(), 0);
```

需要头文件：

```cpp
#include <algorithm>
#include <numeric>
```

## 6. 本章小结

- `vector` 是默认首选容器。
- `map` 适合键值映射，`set` 适合去重集合。
- 迭代器负责连接容器和算法。
- 标准算法能用就用，少写重复循环。
- 了解迭代器失效规则，能少踩很多坑。

[返回主目录](../README.md)