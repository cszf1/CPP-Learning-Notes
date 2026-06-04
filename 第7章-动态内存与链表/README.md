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

### 1.1 为什么要动态内存分配？

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

### 1.2 为什么需要动态内存？

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

### 1.3 new运算符 - 申请内存

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

### 1.4 delete运算符 - 释放内存

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

### 1.5 内存泄漏 - 最常见的问题

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

### 1.6 空悬指针 - 危险的错误

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

### 1.7 动态数组 - 实战应用

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

### 1.8 二维动态数组

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

### 1.9 动态内存分配与构造函数/析构函数

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

### 1.10 常见错误与注意事项

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

### 4.1 链表 vs 顺序表

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

### 4.2 单链表结点结构

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

### 4.3 单链表基本操作

#### 4.3.1 插入操作

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

#### 4.3.2 一般插入算法

```cpp
// 将数据x插入到单链表p结点之后
void Insert(Node *p, Datatype x) {
    Node *newnode = new Node;      // 创建新结点
    newnode->info = x;              // 存入数据
    newnode->link = p->link;        // 新结点指向p的后继
    p->link = newnode;              // p指向新结点
}
```

#### 4.3.3 遍历链表

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

#### 4.3.4 查找操作

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

#### 4.3.5 删除操作

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

### 4.4 带表头的单链表

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

### 4.5 链表的生成算法

#### 4.5.1 向后生成法

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

#### 4.5.2 向前生成法

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

### 4.6 单链表模板类

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

### 4.7 综合应用示例

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

### 4.8 ⚠️ 链表使用注意事项

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


---

### 4.8 双向循环链表

#### 4.8.1 双向链表的基本概念

**双向链表（Doubly Linked List）**：每个结点包含两个指针域——一个指向后继结点，一个指向前驱结点。

**双向循环链表**：在双向链表的基础上，让头结点的前驱指向尾结点，尾结点的后继指向头结点，形成一个环。

```
双向循环链表结构示意图：

        ┌─────────────────────────────────────┐
        ↓                                     │
    ┌─────────┐    ┌─────────┐    ┌─────────┐ │    ┌─────────┐
    │  head   │←───│ node 1  │←───│ node 2  │←───...←───│ node n │
    │ (表头)  │───→│ llink=  │───→│ llink=  │───→      │ llink= │
    │         │    │ rlink=  │    │ rlink=  │          │ rlink= │
    └────↑────┘    └─────────┘    └─────────┘          └─────────┘
         │                                                     │
         └───────────────── rlink ─────────────────────────────┘
```

**与单链表的区别**：
- 单链表：只能从表头向表尾遍历
- 双向链表：可以从表头向表尾，也可以从表尾向表头

**双向链表的特点**：
1. 可以从两个方向访问结点
2. 插入和删除操作更灵活（但需要同时修改两个指针）
3. 占用更多内存（每个结点多一个指针）

#### 4.8.2 双向链表的结点结构

```cpp
#include <iostream>
using namespace std;

// 双向链表结点类模板
template <typename T>
class DblNode {
private:
    T info;                      // 结点数据
    DblNode<T>* llink;           // 前驱指针
    DblNode<T>* rlink;           // 后继指针

public:
    // 构造函数
    DblNode(T data = 0) {
        info = data;
        llink = rlink = nullptr;
    }
    
    // 无参构造函数
    DblNode() {
        info = 0;
        llink = rlink = nullptr;
    }
    
    // 获取结点数据
    T GetInfo() {
        return info;
    }
    
    // 设置结点数据
    void SetInfo(const T& data) {
        info = data;
    }
    
    // 友元类声明
    friend class DblList<T>;
};
```

**结点结构图解**：

```
┌────────────────────────────────────┐
│           DblNode<T>              │
├────────────┬───────────┬───────────┤
│   llink    │    info   │   rlink   │
│  (前驱指针) │   (数据)   │ (后继指针) │
└────────────┴───────────┴───────────┘
       ↑                           ↑
    指向前驱                       指向后继
```

#### 4.8.3 双向循环链表类模板

```cpp
// 双向循环链表类模板
template <typename T>
class DblList {
private:
    DblNode<T>* head;      // 表头指针
    DblNode<T>* current;   // 当前指针

public:
    // 构造函数：创建空表
    DblList();
    
    // 析构函数：销毁链表
    ~DblList();
    
    // 在表头插入新结点
    void Insert(const T& data);
    
    // 删除指定结点
    DblNode<T>* Remove(DblNode<T>* p);
    
    // 打印链表
    void Print();
    
    // 获取链表长度
    int Length();
    
    // 查找数据
    DblNode<T>* Find(const T& data) const;
    
    // 清空链表
    void MakeEmpty();
    
    // 判断链表是否为空
    bool IsEmpty() const;
};
```

#### 4.8.4 双向循环链表的基本操作

**1. 空表建立（构造函数）**

```cpp
template <typename T>
DblList<T>::DblList() {
    // 创建表头结点
    head = new DblNode<T>();
    // 双向循环链表：头结点的前驱和后继都指向自己
    head->rlink = head->llink = head;
    current = nullptr;
}
```

**空表建立图解**：

```
head
  │
  └──→ ┌─────────┐
       │  head   │
       │(表头)   │
       │ llink ──┼──→ head
       │ rlink ──┼──→ head
       └─────────┘
```

**2. 插入结点**

```cpp
template <typename T>
void DblList<T>::Insert(const T& data) {
    // 创建新结点
    current = new DblNode<T>;
    current->info = data;  // 存入数据
    
    // 循环链接：新结点的前驱指向表头的前驱
    current->llink = head->llink;
    // 新结点的后继指向表头
    current->rlink = head;
    
    // 修改原最后结点：它的后继指向新结点
    head->llink->rlink = current;
    // 修改表头：它的前驱指向新结点
    head->llink = current;
}
```

**插入操作图解**：

```
插入前：                        插入后：

head                           head
  │                               │
  ↓                               ↓
┌─────────┐ ←──rlink──┐    ┌─────────┐ ←──rlink──┐
│  head   │           │    │  head   │            │
│(表头)   │           │    │(表头)   │            │
└─────────┼───llink──→┘    └─────────┼───llink──→┘
  ↑                           ↑  ↑
  │                           │  │
  └──rlink───────────────────┘  │  │
                               │  │
                    ┌─────────┴──┘
                    ↓
              ┌─────────┐
              │  new    │ ← current
              │  node   │
              └─────────┘
```

**插入步骤详解**：
1. 创建新结点 `current`
2. `current->llink = head->llink` - 新结点的前驱指向原最后一个结点
3. `current->rlink = head` - 新结点的后继指向表头
4. `head->llink->rlink = current` - 原最后一个结点的后继指向新结点
5. `head->llink = current` - 表头的前驱指向新结点

**3. 删除结点**

```cpp
template <typename T>
DblNode<T>* DblList<T>::Remove(DblNode<T>* p) {
    // 从表头开始查找要删除的结点
    current = head->rlink;
    while (current != head && current != p) {
        current = current->rlink;
    }
    
    if (current == head) {
        // 没找到，p不在链表中
        current = nullptr;
    } else {
        // 找到了，修改前驱结点的后继指针
        p->llink->rlink = p->rlink;
        // 修改后继结点的前驱指针
        p->rlink->llink = p->llink;
        // 断开设点：清空p的指针
        p->rlink = p->llink = nullptr;
    }
    return current;
}
```

**删除操作图解**：

```
删除前：                        删除后：

head                           head
  │                               │
  ↓                               ↓
┌─────────┐                     ┌─────────┐
│  head   │                     │  head   │
│(表头)   │                     │(表头)   │
└─────────┼──→─┐     ┌─────────┼──→─┐
          │     │     │         │     │
    ┌─────┘     ↓     ↓         └─────┘
    ↓       ┌─────────┐   ┌─────────┐
  node1     │   p     │   │ node2  │
    │       │(待删除) │   │
    │       └─────────┘   │
    │                     │
    └─────────────────────┘

修改：p->llink->rlink = p->rlink  // node1的后继指向node2
修改：p->rlink->llink = p->llink  // node2的前驱指向node1
```

#### 4.8.5 双向循环链表 vs 单链表

| 特性 | 单链表 | 双向循环链表 |
|------|--------|--------------|
| 结点结构 | data + 1个指针 | data + 2个指针 |
| 遍历方向 | 单向（从头到尾） | 双向（从头到尾/从尾到头） |
| 内存占用 | 较少 | 较多（多一个指针） |
| 插入删除 | 只需修改1个指针 | 需同时修改2个指针 |
| 找前驱 | O(n) - 需要遍历 | O(1) - 直接通过llink |
| 适用场景 | 只需要从头遍历 | 需要双向操作 |

**选择建议**：
- 如果只需要从头到尾遍历 → 单链表
- 如果需要频繁找前驱或双向操作 → 双向链表
- 如果需要快速找到头尾 → 双向循环链表

#### 4.8.6 综合示例：双向循环链表的使用

```cpp
#include <iostream>
using namespace std;

// 双向链表结点类
template <typename T>
class DblNode {
private:
    T info;
    DblNode<T>* llink;
    DblNode<T>* rlink;
public:
    DblNode(T data = 0) {
        info = data;
        llink = rlink = nullptr;
    }
    T GetInfo() { return info; }
    friend class DblList<T>;
};

// 双向循环链表类
template <typename T>
class DblList {
private:
    DblNode<T>* head;
    DblNode<T>* current;
public:
    DblList() {
        head = new DblNode<T>();
        head->rlink = head->llink = head;
        current = nullptr;
    }
    
    ~DblList() {
        MakeEmpty();
        delete head;
    }
    
    void Insert(const T& data) {
        current = new DblNode<T>(data);
        current->llink = head->llink;
        current->rlink = head;
        head->llink->rlink = current;
        head->llink = current;
    }
    
    void Print() {
        current = head->rlink;
        cout << "链表内容: ";
        while (current != head) {
            cout << current->GetInfo() << " ";
            current = current->rlink;
        }
        cout << endl;
    }
    
    int Length() {
        int count = 0;
        current = head->rlink;
        while (current != head) {
            count++;
            current = current->rlink;
        }
        return count;
    }
    
    void MakeEmpty() {
        current = head->rlink;
        while (current != head) {
            head->rlink = current->rlink;
            current->rlink->llink = head;
            delete current;
            current = head->rlink;
        }
    }
    
    bool IsEmpty() {
        return head->rlink == head;
    }
};

int main() {
    DblList<int> list;
    
    // 插入元素
    cout << "=== 双向循环链表操作演示 ===" << endl;
    list.Insert(10);
    list.Insert(20);
    list.Insert(30);
    list.Insert(40);
    
    cout << "插入4个元素后: ";
    list.Print();
    cout << "链表长度: " << list.Length() << endl;
    
    // 再次插入
    list.Insert(25);
    cout << "再插入25后: ";
    list.Print();
    
    cout << endl << "链表是否为空: " << (list.IsEmpty() ? "是" : "否") << endl;
    
    return 0;
}
```

**运行结果**：
```
=== 双向循环链表操作演示 ===
插入4个元素后: 链表内容: 10 20 30 40 
链表长度: 4
再插入25后: 链表内容: 10 20 30 40 25 
```

---



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

### 7.1 什么是栈和队列？

**栈和队列**都是特殊的线性表，它们限制了数据存取的位置。

```
线性表（可自由存取）:   a0  a1  a2  a3  ...  an-1
                        ↑
                    可以在任意位置插入/删除

栈（LIFO）:            a0  a1  a2  a3  ...  an-1
                        ↑                        ↑
                       bottom                  top
                    只能在"一端"操作（栈顶）

队列（FIFO）:    front → a0  a1  a2  ... an-1 ← rear
                       ↑                      ↑
                     出队端                  入队端
```

**实现方式**：可以用顺序表（数组）实现，也可以用链表实现。

---

### 7.2 栈

#### 7.2.1 栈的基本概念

**栈（Stack）**：只允许在表的一端（称为**栈顶**）进行插入和删除操作的线性表。

**特点**：**后进先出（LIFO：Last In First Out）**

```
┌─────────────┐
│    top      │  ← 栈顶：只能在这里操作
│    ...      │
│    a2       │
│    a1       │
│    a0       │
├─────────────┤
│   bottom    │  ← 栈底：固定不动
└─────────────┘
```

**生活实例**：
- **叠盘子**：最后放的盘子最先被拿走
- **函数调用**：被调用的函数最后执行完，最先返回

**基本操作**：
- **压栈（Push）**：将元素放入栈顶
- **弹出（Pop）**：将栈顶元素取出并删除
- **取元素（GetTop）**：查看栈顶元素（不出栈）
- **判空/判满**：检查栈状态

#### 7.2.2 顺序栈的类模板

```cpp
#include <iostream>
#include <cassert>
using namespace std;

// 顺序栈类模板
template<typename T>
class Stack {
private:
    int top;              // 栈顶指针（数组下标）
    T* elements;          // 动态数组存储元素
    int maxSize;          // 栈的最大容量

public:
    // 构造函数
    Stack(int size = 20) {
        maxSize = size;
        top = -1;                    // 空栈时top为-1
        elements = new T[maxSize];
        assert(elements != nullptr); // 确保分配成功
    }
    
    // 析构函数
    ~Stack() {
        delete[] elements;
    }
    
    // 压栈：将元素放入栈顶
    void Push(const T& data) {
        assert(!IsFull());           // 栈满则退出
        elements[++top] = data;      // top先加1，再存入数据
    }
    
    // 弹出：取出栈顶元素
    T& Pop() {
        assert(!IsEmpty());           // 栈空则退出
        return elements[top--];       // 返回栈顶数据，top减1
    }
    
    // 取栈顶元素（不出栈）
    T& GetTop() const {
        assert(!IsEmpty());
        return elements[top];
    }
    
    // 判空
    bool IsEmpty() const {
        return top == -1;
    }
    
    // 判满
    bool IsFull() const {
        return top == maxSize - 1;
    }
    
    // 清空栈
    void MakeEmpty() {
        top = -1;
    }
    
    // 打印栈内容
    void PrintStack() const {
        cout << "栈内容(从栈底到栈顶): ";
        for (int i = 0; i <= top; i++) {
            cout << elements[i] << " ";
        }
        cout << endl;
    }
};
```

**顺序栈操作图解**：

```
初始空栈:
┌────┐
│top=-1│
└────┘

Push(10):           Push(20):           Push(30):
┌────┐              ┌────┐              ┌────┐
│top=1 │             │top=2 │             │top=2 │
├────┤              ├────┤              ├────┤
│ 20 │←top          │ 30 │←top          │ 30 │
├────┤              ├────┤              ├────┤
│ 10 │              │ 20 │              │ 20 │
├────┤              ├────┤              ├────┤
│    │              │ 10 │              │ 10 │
└────┘              └────┘              └────┘

Pop(): 取出30，top回到1
┌────┐
│top=1 │
├────┤
│ 20 │←top
├────┤
│ 10 │
└────┘
```

#### 7.2.3 顺序栈使用示例

```cpp
int main() {
    int i;
    int a[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    int b[10];
    
    Stack<int> istack(10);
    
    // 压栈：将a数组中的元素全部压入栈
    for (i = 0; i < 10; i++) {
        istack.Push(a[i]);
    }
    
    // 检查是否栈满
    if (istack.IsFull()) {
        cout << "栈满" << endl;
    }
    
    cout << "压栈后: ";
    istack.PrintStack();
    
    // 弹栈：将栈中元素弹出并存入b数组
    for (i = 0; i < 10; i++) {
        b[i] = istack.Pop();
    }
    
    // 检查是否栈空
    if (istack.IsEmpty()) {
        cout << "栈空" << endl;
    }
    
    cout << "弹栈结果: ";
    for (i = 0; i < 10; i++) {
        cout << b[i] << "\t";
    }
    cout << endl;
    
    return 0;
}
```

**运行结果**：
```
栈满
压栈后: 栈内容(从栈底到栈顶): 0 1 2 3 4 5 6 7 8 9 
栈空
弹栈结果: 9	8	7	6	5	4	3	2	1	0	
```

**注意**：后进先出，所以弹栈顺序是 `9, 8, 7, 6, 5, 4, 3, 2, 1, 0`。

#### 7.2.4 链栈的类模板

**链栈**：用链表实现的栈，栈顶在链表头部。

```cpp
#include <iostream>
#include <cassert>
using namespace std;

// 链栈结点类模板
template<typename T>
class Node {
private:
    T info;              // 结点数据
    Node<T>* link;        // 指向下一个结点的指针
    
public:
    Node(T data = 0, Node<T>* next = nullptr) {
        info = data;
        link = next;
    }
    
    friend class Stacklink<T>;
};

// 链栈类模板
template<typename T>
class Stacklink {
private:
    Node<T>* top;         // 栈顶指针（指向链表头结点）
    
public:
    // 构造函数：空栈
    Stacklink() {
        top = nullptr;
    }
    
    // 析构函数
    ~Stacklink() {
        MakeEmpty();
    }
    
    // 压栈
    void Push(const T& data) {
        // 在链表头部插入新结点
        top = new Node<T>(data, top);
    }
    
    // 弹栈
    T Pop() {
        assert(!IsEmpty());
        Node<T>* temp = top;
        T data = temp->info;    // 保存数据
        top = top->link;        // 栈顶下移
        delete temp;            // 释放原栈顶结点
        return data;
    }
    
    // 取栈顶元素
    T& GetTop() const {
        assert(!IsEmpty());
        return top->info;
    }
    
    // 判空
    bool IsEmpty() const {
        return top == nullptr;
    }
    
    // 清空栈
    void MakeEmpty() {
        while (top != nullptr) {
            Node<T>* temp = top;
            top = top->link;
            delete temp;
        }
    }
};
```

**链栈示意图**：

```
链栈（无表头）:
      top
       ↓
    ┌───────┐    ┌───────┐    ┌───────┐
    │ data1 │───→│ data2 │───→│ data3 │───→ NULL
    │(栈顶)  │    │       │    │(栈底)  │
    └───────┘    └───────┘    └───────┘

压栈Push(x):         弹栈Pop():
    top                  top
     ↓                    ↓
  ┌───────┐          ┌───────┐    ┌───────┐
  │   x   │──┐       │ data1 │───→│ data2 │───→ NULL
  ├───────┤  │       ├───────┤    ├───────┤
  │ data1 │──┼───    │ data2 │    │ data3 │
  ├───────┤  │       └───────┘    └───────┘
  │ data2 │──┘
  └───────┘
```

#### 7.2.5 顺序栈 vs 链栈

| 特性 | 顺序栈 | 链栈 |
|------|--------|------|
| **存储方式** | 数组（连续内存） | 链表（分散内存） |
| **访问方式** | 可随机访问 | 只能顺序访问栈顶 |
| **内存管理** | 预先分配固定大小 | 按需分配，动态扩展 |
| **优点** | 实现简单，速度快 | 不会溢出，可无限增长 |
| **缺点** | 可能溢出（容量有限） | 每个结点多一个指针开销 |
| **适用场景** | 已知最大容量，频繁访问 | 容量不确定，插入删除频繁 |

**选择建议**：
- 如果能估计最大数据量 → 顺序栈
- 如果数据量不可预知 → 链栈

#### 7.2.6 栈的应用

##### 应用1：函数调用

**原理**：函数调用时，系统使用栈保存调用信息和局部变量。

```
函数调用栈示例:

main() {
    fun1();          ← 入栈
}

fun1() {
    fun2();          ← 入栈
}

fun2() {             ← 出栈（最后调用，最先返回）
    ...
}
```

```
调用栈状态变化:

调用前:                    fun1调用时:              fun2调用时:
┌─────────┐                ┌─────────┐              ┌─────────┐
│  main   │                │  main   │              │  main   │
│  栈帧   │                │  栈帧   │              │  栈帧   │
└─────────┘                └─────────┘              └─────────┘
                              ↑                        ↑
                             top                     top
                          ┌─────────┐            ┌─────────┐
                          │  fun1   │            │  fun1   │
                          │  栈帧   │            │  栈帧   │
                          └─────────┘            └─────────┘
                                                      ↑
                                                   top
                                                ┌─────────┐
                                                │  fun2   │
                                                │  栈帧   │
                                                └─────────┘
```

**栈帧包含**：参数、返回地址、局部变量

##### 应用2：十进制转R进制

**算法**：除R取余，余数入栈，余数为0时结束，最后弹栈输出。

```cpp
#include <iostream>
#include <cassert>
using namespace std;

// 顺序栈类（简化版）
template<typename T>
class Stack {
private:
    int top;
    T* elements;
    int maxSize;
public:
    Stack(int size = 20) {
        maxSize = size;
        top = -1;
        elements = new T[maxSize];
    }
    ~Stack() { delete[] elements; }
    void Push(const T& data) { elements[++top] = data; }
    T& Pop() { return elements[top--]; }
    bool IsEmpty() const { return top == -1; }
};

// 十进制转换为R进制
void DecChange(int decimal, int R) {
    Stack<char> stack(10);
    
    // 除R取余，余数入栈
    while (decimal > 0) {
        int remainder = decimal % R;
        // 转换为字符：10-15转为A-F
        char c = (remainder < 10) ? (remainder + '0') : (remainder - 10 + 'A');
        stack.Push(c);
        decimal /= R;
    }
    
    // 输出转换结果
    cout << "转换为" << R << "进制为: ";
    while (!stack.IsEmpty()) {
        cout << stack.Pop();
    }
    cout << endl;
}

int main() {
    // 十进制938转换为八进制
    DecChange(938, 8);   // 输出: 1652
    
    // 十进制100转换为十六进制
    DecChange(100, 16);  // 输出: 64
    
    // 十进制10转换为二进制
    DecChange(10, 2);    // 输出: 1010
    
    return 0;
}
```

**转换过程图解**（以938转八进制为例）：

```
938 ÷ 8 = 117 ... 2  →  入栈
117 ÷ 8 = 14  ... 5  →  入栈
14  ÷ 8 = 1   ... 6  →  入栈
1   ÷ 8 = 0   ... 1  →  入栈

栈状态: (栈底→栈顶)
┌─────┬─────┬─────┬─────┐
│  1  │  6  │  5  │  2  │
└─────┴─────┴─────┴─────┘

弹栈输出: 1 6 5 2

所以 938(十进制) = 1652(八进制)
```

##### 应用3：括号匹配

**算法**：从左到右扫描字符串，遇到左括号压栈，遇到右括号弹栈匹配。

```cpp
#include <iostream>
#include <cstring>
using namespace std;

// 字符栈（简化版）
class CharStack {
private:
    char* data;
    int top;
    int maxSize;
public:
    CharStack(int size = 100) {
        maxSize = size;
        top = -1;
        data = new char[maxSize];
    }
    ~CharStack() { delete[] data; }
    void Push(char c) { data[++top] = c; }
    char Pop() { return data[top--]; }
    bool IsEmpty() const { return top == -1; }
    char GetTop() const { return data[top]; }
};

// 括号匹配函数
bool IsMatch(const char* expr) {
    CharStack stack(strlen(expr));
    
    for (int i = 0; expr[i] != '\0'; i++) {
        char c = expr[i];
        
        if (c == '(' || c == '[' || c == '{') {
            // 左括号：压栈
            stack.Push(c);
        } 
        else if (c == ')' || c == ']' || c == '}') {
            // 右括号：弹栈匹配
            if (stack.IsEmpty()) {
                cout << "错误：多余的右括号 '" << c << "'" << endl;
                return false;
            }
            
            char left = stack.Pop();
            if ((c == ')' && left != '(') ||
                (c == ']' && left != '[') ||
                (c == '}' && left != '{')) {
                cout << "错误：括号不匹配 '" << left << "' 与 '" << c << "'" << endl;
                return false;
            }
        }
    }
    
    // 检查栈是否为空
    if (!stack.IsEmpty()) {
        cout << "错误：有未匹配的左括号" << endl;
        return false;
    }
    
    return true;
}

int main() {
    const char* expr1 = "(a + b) * [c - {d / e}]";
    const char* expr2 = "(a + b) * [c - {d / e}";
    const char* expr3 = "(a + b) * c] - d}";
    
    cout << expr1 << " : " << (IsMatch(expr1) ? "匹配" : "不匹配") << endl;
    cout << expr2 << " : " << (IsMatch(expr2) ? "匹配" : "不匹配") << endl;
    cout << expr3 << " : " << (IsMatch(expr3) ? "匹配" : "不匹配") << endl;
    
    return 0;
}
```

**匹配过程图解**：

```
表达式: (a + b) * [c - {d / e}]

扫描过程:
字符    操作        栈状态
─────────────────────────────
(       压栈        [(]
a       忽略        [(]
+       忽略        [(]
b       忽略        [(]
)       匹配弹栈    []  ← 匹配成功！
*       忽略        []
[       压栈        [[]
c       忽略        [[]
-       忽略        [[]
{       压栈        [[[
d       忽略        [[[]
/       忽略        [[[]
e       忽略        [[[]
}       匹配弹栈    [[]  ← 匹配成功！
]       匹配弹栈    []  ← 匹配成功！

最终栈空，匹配成功！
```

##### 应用4：表达式计算

**中缀表达式**：我们平常使用的表达式，如 `a + b * c`

**后缀表达式（逆波兰式）**：运算符在操作数后面，如 `a b c * +`

**中缀转后缀算法**：
1. 数字直接输出
2. 左括号压栈
3. 运算符：与栈顶比较优先级
   - 栈顶优先级低（或左括号）：压栈
   - 栈顶优先级高：弹栈输出，然后压栈
4. 右括号：弹栈输出直到匹配左括号

```cpp
#include <iostream>
#include <cstring>
#include <cctype>
using namespace std;

class CharStack {
private:
    char* data;
    int top;
    int maxSize;
public:
    CharStack(int size = 100) {
        maxSize = size;
        top = -1;
        data = new char[maxSize];
    }
    ~CharStack() { delete[] data; }
    void Push(char c) { data[++top] = c; }
    char Pop() { return data[top--]; }
    bool IsEmpty() const { return top == -1; }
    char GetTop() const { return data[top]; }
    int Priority(char op) const {
        if (op == '+' || op == '-') return 1;
        if (op == '*' || op == '/') return 2;
        return 0;  // 左括号最低
    }
};

// 中缀转后缀
void InfixToPostfix(const char* infix, char* postfix) {
    CharStack stack(strlen(infix));
    int j = 0;
    
    for (int i = 0; infix[i] != '\0'; i++) {
        char c = infix[i];
        
        if (isalnum(c)) {
            // 操作数：直接输出
            postfix[j++] = c;
        }
        else if (c == '(') {
            // 左括号：压栈
            stack.Push(c);
        }
        else if (c == ')') {
            // 右括号：弹栈直到左括号
            while (!stack.IsEmpty() && stack.GetTop() != '(') {
                postfix[j++] = stack.Pop();
            }
            stack.Pop();  // 弹出左括号
        }
        else if (c == '+' || c == '-' || c == '*' || c == '/') {
            // 运算符：处理优先级
            while (!stack.IsEmpty() && 
                   stack.GetTop() != '(' && 
                   stack.Priority(stack.GetTop()) >= stack.Priority(c)) {
                postfix[j++] = stack.Pop();
            }
            stack.Push(c);
        }
    }
    
    // 弹出剩余运算符
    while (!stack.IsEmpty()) {
        postfix[j++] = stack.Pop();
    }
    postfix[j] = '\0';
}

int main() {
    const char* infix = "a+b*c";
    char postfix[100];
    
    InfixToPostfix(infix, postfix);
    cout << "中缀: " << infix << endl;
    cout << "后缀: " << postfix << endl;
    
    const char* infix2 = "(a+b)*c";
    InfixToPostfix(infix2, postfix);
    cout << "中缀: " << infix2 << endl;
    cout << "后缀: " << postfix << endl;
    
    return 0;
}
```

**转换示例**：

```
中缀表达式: a + b * c + (d - e) * f

转换过程:
┌─────┬────────┬──────────────────┐
│ 扫描 │  栈(底→顶) │  输出           │
├─────┼────────┼──────────────────┤
│ a   │ 空     │ a                │
│ +   │ +      │ a                │
│ b   │ +      │ ab               │
│ *   │ +*     │ ab               │  *优先级高于+
│ c   │ +*     │ abc              │
│ +   │ +      │ abc*+            │  *弹栈，+压栈
│ (   │ +(     │ abc*+            │
│ d   │ +(     │ abc*+d           │
│ -   │ +(-    │ abc*+d           │
│ e   │ +(-    │ abc*+de          │
│ )   │ +      │ abc*+de-         │  弹栈至(
│ *   │ +*     │ abc*+de-         │
│ f   │ +*     │ abc*+de-f        │
│ 结束│ 空     │ abc*+de-*f+      │  弹栈全部
└─────┴────────┴──────────────────┘

结果: abc*+de-*f+
验证: a b c * + d e - f * +
```

---

### 7.3 队列

#### 7.3.1 队列的基本概念

**队列（Queue）**：只允许在一端插入，另一端删除的线性表。

**特点**：**先进先出（FIFO：First In First Out）**

```
┌─────────────────────────────────────────────────┐
│                    队  列                       │
├─────────────────────────────────────────────────┤
│  队头(front)                              队尾  │
│    ↓                                   (rear)   │
│    │                                     ↑      │
│  出队                                  入队     │
│    │                                     │      │
│    a1   a2   a3   ...   an-1   an        │      │
│  ←───←──←──←──────────────────────←──────┘      │
│                                                 │
│  元素移动方向 ─────────────────────→           │
└─────────────────────────────────────────────────┘
```

**生活实例**：
- **排队买东西**：先排队的先买到
- **打印机队列**：先提交的文档先打印

**基本操作**：
- **进队（EnQueue）**：在队尾插入元素
- **出队（DeQueue）**：从队头删除元素
- **取队头（GetFront）**：查看队头元素（不出队）

#### 7.3.2 循环队列

**问题**：用普通数组实现队列时，随着入队出队，元素会逐渐向数组末端移动，造成"假溢出"。

**解决方案**：循环队列 - 把数组想象成环形，首尾相连。

```
普通队列的问题（假溢出）:

初始:      入队3个:     再出队2个:   再入队2个:
┌────┐    ┌────┐      ┌────┐      ┌────┐
│ a1 │    │ a1 │      │    │      │    │
├────┤    ├────┤      ├────┤      ├────┤
│ a2 │    │ a2 │      │ a2 │      │ c1 │
├────┤    ├────┤      ├────┤      ├────┤
│ a3 │    │ a3 │      │ a3 │      │ c2 │
├────┤    └────┘      ├────┤      ├────┤
│    │               │    │      ├────┤
└────┘               └────┘      ├────┤  ← 无法继续入队！
                              明明前面还有空位

循环队列（环形）:

初始:           入队3个:        再出队2个:     再入队2个:
┌────┐        ┌────┐         ┌────┐        ┌────┐
│    │        │ a1 │         │    │        │ c1 │
├────┤        ├────┤         ├────┤        ├────┤
│    │←front  │ a2 │←rear    │ a2 │←rear   │ c2 │
└────┘        └────┘         └────┘        └────┘
              front           front
```

**循环队列的指针移动**：

```cpp
// 普通队列
rear++;              // 一直向右移动

// 循环队列（取模实现循环）
rear = (rear + 1) % maxSize;    // 到达末端后回到开头
front = (front + 1) % maxSize;
```

#### 7.3.3 循环队列类模板

```cpp
#include <iostream>
#include <cassert>
using namespace std;

// 循环队列类模板
template <typename T>
class Queue {
private:
    int rear;            // 队尾指针
    int front;           // 队头指针
    T* elements;         // 存放队列元素的数组
    int maxSize;         // 队列最大容量（实际能存maxSize-1个元素）

public:
    // 构造函数
    Queue(int ms = 18) {
        maxSize = ms;
        elements = new T[maxSize];
        rear = front = 0;     // 初始状态：队空
        assert(elements != nullptr);
    }
    
    // 析构函数
    ~Queue() {
        delete[] elements;
    }
    
    // 判空
    bool IsEmpty() const {
        return rear == front;
    }
    
    // 判满（牺牲一个空间来区分队空和队满）
    bool IsFull() const {
        return (rear + 1) % maxSize == front;
    }
    
    // 求队列长度
    int Length() const {
        return (rear - front + maxSize) % maxSize;
    }
    
    // 进队
    void EnQue(const T& data) {
        assert(!IsFull());                // 队满则退出
        rear = (rear + 1) % maxSize;       // 队尾指针循环加1
        elements[rear] = data;             // 存入数据
    }
    
    // 出队
    T DeQue() {
        assert(!IsEmpty());                // 队空则退出
        front = (front + 1) % maxSize;     // 队头指针循环加1
        return elements[front];             // 返回队头数据
    }
    
    // 取队头元素
    T& GetFront() const {
        assert(!IsEmpty());
        return elements[(front + 1) % maxSize];
    }
    
    // 置空
    void MakeEmpty() {
        front = rear = 0;
    }
};
```

**循环队列操作图解**：

```
初始状态（队空）:
┌────┬────┬────┬────┬────┐
│    │    │    │    │    │
└────┴────┴────┴────┴────┘
 ↑                          ↑
front                      rear
(rear == front 表示队空)

EnQue(A), EnQue(B), EnQue(C):
┌────┬────┬────┬────┬────┐
│  C │    │    │    │  A │ B
└────┴────┴────┴────┴────┘
                        ↑  ↑
                      front rear
                      
DeQue(): 取出A
┌────┬────┬────┬────┬────┐
│  C │    │    │    │  A │ B
└────┴────┴────┴────┴────┘
      ↑          ↑
    front       rear

EnQue(D), EnQue(E):
┌────┬────┬────┬────┬────┐
│  C │  D │  E │    │  A │ B
└────┴────┴────┴────┴────┘
              ↑  ↑
            front rear
            (循环回到开头)

队满状态:
┌────┬────┬────┬────┬────┐
│  C │  D │    │    │    │
└────┴────┴────┴────┴────┘
  ↑                      ↑
 rear                   front
(rear+1)%maxSize == front 表示队满)
```

#### 7.3.4 循环队列使用示例

```cpp
int main() {
    int i;
    Queue<char> que;                   // 默认18个元素队列，可用17个
    
    char str[] = "abcdefghijklmnop";   // 16个字符
    
    que.MakeEmpty();                    // 置空
    
    // 进队
    for (i = 0; i < 16; i++) {
        que.EnQue(str[i]);
    }
    
    // 检查队满
    if (que.IsFull()) {
        cout << "队满" << endl;
    }
    
    // 输出队列长度
    cout << "队中元素个数: " << que.Length() << endl;
    
    // 出队（先进先出）
    cout << "出队顺序: ";
    for (i = 0; i < 16; i++) {
        cout << que.DeQue();
    }
    cout << endl;
    
    // 检查队空
    if (que.IsEmpty()) {
        cout << "队空" << endl;
    }
    cout << "队中元素个数: " << que.Length() << endl;
    
    return 0;
}
```

**运行结果**：
```
队满
队中元素个数: 16
出队顺序: abcdefghijklmnop
队空
队中元素个数: 0
```

#### 7.3.5 链队（链表实现的队列）

**链队**：用链表实现的队列，队头在链表头部，队尾在链表尾部。

```cpp
#include <iostream>
#include <cassert>
using namespace std;

// 链队结点类模板
template<typename T>
class Node {
private:
    T info;           // 结点数据
    Node* link;       // 指向下一个结点
    
public:
    Node(T data = 0, Node* next = nullptr) {
        info = data;
        link = next;
    }
    
    friend class Queuelink<T>;
};

// 链队列类模板
template<typename T>
class Queuelink {
private:
    Node<T>* front;    // 队头指针
    Node<T>* rear;     // 队尾指针

public:
    // 构造函数
    Queuelink() {
        front = rear = nullptr;    // 空队列
    }
    
    // 析构函数
    ~Queuelink() {
        Empty();
    }
    
    // 判空
    bool IsEmpty() {
        return front == nullptr;
    }
    
    // 入队
    void EnQuelink(const T& data) {
        if (front == nullptr) {
            // 第一个元素
            front = rear = new Node<T>(data, nullptr);
        } else {
            // 新结点接到队尾
            rear = rear->link = new Node<T>(data, nullptr);
        }
    }
    
    // 出队
    T DeQuelink() {
        assert(!IsEmpty());
        Node<T>* temp = front;
        T data = temp->info;          // 保存数据
        front = front->link;          // 队头下移
        delete temp;                  // 释放原队头
        return data;
    }
    
    // 取队头元素
    T& GetFront() {
        assert(!IsEmpty());
        return front->info;
    }
    
    // 求队列长度
    int Length() {
        int count = 0;
        Node<T>* p = front;
        while (p != nullptr) {
            count++;
            p = p->link;
        }
        return count;
    }
    
    // 清空队列
    void Empty() {
        Node<T>* temp;
        while (front != nullptr) {
            temp = front;
            front = front->link;
            delete temp;
        }
        rear = nullptr;
    }
};
```

**链队示意图**：

```
链队结构:

队头                              队尾
↓                                 ↓
┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐
│ node1  │───→│ node2  │───→│ node3  │───→│ node4  │───→ NULL
│(front) │    │        │    │        │    │ (rear) │
└────────┘    └────────┘    └────────┘    └────────┘

空队列:
front = rear = nullptr

只有一个结点:
front = rear = node1
```

#### 7.3.6 循环队列 vs 链队

| 特性 | 循环队列 | 链队 |
|------|----------|------|
| **存储方式** | 数组（固定大小） | 链表（动态大小） |
| **长度限制** | 预先确定，可能满 | 理论上无限制 |
| **内存利用** | 固定，空间利用率固定 | 按需分配，但有指针开销 |
| **入队出队** | O(1) | O(1) |
| **访问队头** | O(1) | O(1) |

**选择建议**：
- 能确定最大数据量 → 循环队列（更快）
- 数据量不确定 → 链队（更灵活）

#### 7.3.7 队列应用场景

1. **任务调度**：操作系统中的任务队列
2. **广度优先搜索（BFS）**：图的遍历
3. **消息队列**：进程间通信
4. **打印队列**：多文档打印
5. **键盘缓冲区**：按键输入缓冲

---

### 7.4 栈与队列总结

#### 对比表

| 特性 | 栈 | 队列 |
|------|-----|------|
| **存取规则** | LIFO（后进先出） | FIFO（先进先出） |
| **操作端** | 一端（栈顶） | 两端（头删尾插） |
| **典型应用** | 函数调用、括号匹配、表达式求值 | 任务调度、缓存、宽度优先搜索 |

#### 实现方式对比

| 实现 | 栈 | 队列 |
|------|-----|------|
| **顺序实现** | 顺序栈（数组） | 循环队列（数组） |
| **链式实现** | 链栈（链表） | 链队（链表） |

#### 选择建议

```
需要后进先出？     ──→  栈
    │
    └── 数据量确定？  ──→  顺序栈
        │                 
        └── 数据量不确定？  ──→  链栈

需要先进先出？     ──→  队列
    │
    └── 数据量确定？  ──→  循环队列
        │                 
        └── 数据量不确定？  ──→  链队
```

---



## 8. 本章小结

- 动态内存要申请也要释放。
- `new/delete` 必须配对正确。
- 指针成员要警惕浅拷贝。
- 链表插入删除灵活，查找较慢。
- 带头结点能减少边界判断。
- 栈和队列是受限操作的线性结构。

[返回主目录](../README.md)

[返回主目录](../README.md)
