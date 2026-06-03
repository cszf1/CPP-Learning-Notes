# 第6章 模板与数据结构

> 本章目标：写通用代码，并理解顺序表、查找、排序这些基础结构。模板像模具，数据结构像收纳方法。

## 目录

1. 模板的作用
2. 函数模板
3. 类模板
4. 顺序表
5. 查找算法
6. 排序算法
7. 本章小结

## 1. 模板的作用

模板把类型作为参数，减少重复代码。

```cpp
template <typename T>
T maxValue(T a, T b) {
    return a > b ? a : b;
}
```

函数模板不是函数本身，编译器会根据实际类型生成对应函数。像点餐模板：菜单是模板，真正端上来的是具体菜。

## 2. 函数模板

```cpp
template <typename T>
T absValue(T x) {
    return x < 0 ? -x : x;
}
```

注意：

- 模板参数可以有多个。
- 匹配模板函数时通常不做自动类型转换。
- 普通函数完全匹配时，优先使用普通函数。

## 3. 类模板

```cpp
template <typename T>
class Store {
    T item;
public:
    void set(T x) { item = x; }
    T get() const { return item; }
};

Store<int> a;
Store<string> b;
```

类模板外定义成员函数：

```cpp
template <typename T>
T Store<T>::get() const {
    return item;
}
```

## 4. 顺序表

顺序表是线性表的一种，特点是连续存储。

优点：随机访问快。缺点：中间插入删除要移动元素。

```cpp
template <typename T>
class SeqList {
    T* data;
    int length;
    int capacity;
public:
    SeqList(int cap = 100) : data(new T[cap]), length(0), capacity(cap) {}
    ~SeqList() { delete[] data; }
};
```

现代 C++ 里很多顺序表需求可以直接用 `vector`。

## 5. 查找算法

顺序查找：适合无序表。

```cpp
for (int i = 0; i < n; ++i)
    if (a[i] == x) return i;
```

折半查找：适合有序表。

```cpp
int l = 0, r = n - 1;
while (l <= r) {
    int m = l + (r - l) / 2;
    if (a[m] == x) return m;
    if (a[m] < x) l = m + 1;
    else r = m - 1;
}
```

分块查找：先用索引确定块，再在块内顺序查找。它像先找楼栋，再找房间。

## 6. 排序算法

| 算法 | 思路 | 特点 |
| --- | --- | --- |
| 插入排序 | 把新元素插到前面有序区 | 小规模数据好理解 |
| 冒泡排序 | 相邻元素交换，大的往后冒 | 简单但效率低 |
| 选择排序 | 每轮选最小值放前面 | 交换次数少 |

实际写程序优先：

```cpp
sort(a.begin(), a.end());
```

## 7. 本章小结

- 模板解决“逻辑相同、类型不同”的重复。
- 类模板常用于容器。
- 顺序表访问快，插入删除可能慢。
- 顺序查找不要求有序，折半查找必须有序。
- 排序要理解原理，工程中优先用标准库。

[返回主目录](../README.md)