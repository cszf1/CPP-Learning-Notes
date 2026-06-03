# 第7章 动态内存与链表

> 本章目标：理解堆内存和链式结构。动态内存像临时租房，租了要退；链表像接力队，每个人知道下一个人在哪。

## 目录

1. 动态内存基础
2. new/delete 与 new[]/delete[]
3. 浅拷贝与深拷贝
4. 单链表结点
5. 带头结点链表
6. 链表基本操作
7. 栈与队列
8. 本章小结

## 1. 动态内存基础

普通局部变量通常在栈上，动态申请的对象在堆上。

```cpp
int* p = new int(10);
delete p;
```

堆内存不会自动在作用域结束时释放，忘记 `delete` 就可能内存泄漏。

## 2. new/delete 与 new[]/delete[]

```cpp
int* p = new int;
delete p;

int* arr = new int[100];
delete[] arr;
```

配对很重要：`new` 对 `delete`，`new[]` 对 `delete[]`。别乱配，像鞋子左右脚穿反，能走但别扭，还可能摔。

## 3. 浅拷贝与深拷贝

类里有指针成员时，默认复制可能只是复制地址。

```cpp
class Array {
    int* data;
    int n;
public:
    Array(int n) : data(new int[n]{}), n(n) {}
    Array(const Array& other) : data(new int[other.n]), n(other.n) {
        for (int i = 0; i < n; ++i) data[i] = other.data[i];
    }
    ~Array() { delete[] data; }
};
```

如果写了析构函数，通常也要考虑复制构造和赋值运算符。

## 4. 单链表结点

```cpp
struct Node {
    int data;
    Node* next;
};
```

每个结点保存数据和下一个结点地址。链表不要求内存连续，所以插入删除灵活。

## 5. 带头结点链表

头结点不存有效数据，主要让插入删除逻辑统一。

```cpp
Node* head = new Node{0, nullptr};
```

没有头结点也能做，但很多边界情况要单独判断。头结点像队伍里的“空班长”，不参赛，但让队伍好管理。

## 6. 链表基本操作

头插法：

```cpp
void pushFront(Node* head, int x) {
    Node* p = new Node{x, head->next};
    head->next = p;
}
```

查找：

```cpp
Node* find(Node* head, int x) {
    for (Node* p = head->next; p != nullptr; p = p->next)
        if (p->data == x) return p;
    return nullptr;
}
```

删除时要找到前驱结点：

```cpp
void eraseAfter(Node* prev) {
    Node* victim = prev->next;
    if (victim) {
        prev->next = victim->next;
        delete victim;
    }
}
```

## 7. 栈与队列

栈：后进先出。队列：先进先出。

链式栈可用头插头删实现，链式队列通常维护队头和队尾指针。

```cpp
stack<int> s;
queue<int> q;
```

学习时理解链式实现，写项目时优先标准库。

## 8. 本章小结

- 动态内存要申请也要释放。
- `new/delete` 必须配对正确。
- 指针成员要警惕浅拷贝。
- 链表插入删除灵活，查找较慢。
- 带头结点能减少边界判断。
- 栈和队列是受限操作的线性结构。

[返回主目录](../README.md)