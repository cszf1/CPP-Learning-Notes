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

### 1.1 痛苦的函数重载

想象一下，你要写一个求最大值的函数：

```cpp
// 版本1：处理int
int max(int a, int b, int c) {
    if(b > a) a = b;
    if(c > a) a = c;
    return a;
}

// 版本2：处理float
float max(float a, float b, float c) {
    if(b > a) a = b;
    if(c > a) a = c;
    return a;
}

// 版本3：处理double
double max(double a, double b, double c) {
    if(b > a) a = b;
    if(c > a) a = c;
    return a;
}
```

**问题**：这三个函数的逻辑完全相同！只是数据类型不同，却要写三遍。

### 1.2 模板的解决方案

C++提供了**模板（Template）**机制，用一个"模板"来生成针对不同类型的代码：

```cpp
template<typename T>  // T是一个类型参数，编译器会自动推断
T max(T a, T b, T c) {
    if(b > a) a = b;
    if(c > a) a = c;
    return a;
}
```

**通俗理解**：模板就像一个"万能模具"，往里面倒什么材料（T），就生产出什么产品。

### 1.3 模板的两大类

| 类型 | 作用 | 关键词 |
|------|------|--------|
| **函数模板** | 创建通用功能的函数 | `template<typename T>` |
| **类模板** | 创建通用类型的类 | `template<typename T>` |

---



## 2. 函数模板

### 2.1 基本语法

```cpp
template<模板参数表>
返回类型 函数名(形式参数表) { 
    // 函数体
}
```

**语法要点**：
- 模板参数表用尖括号 `<>` 包裹
- `typename` 是最常用的类型参数关键字（也可以用 `class`，两者等价）
- 类型参数名可以自己起，常见的有 `T`、`Type`、`Value` 等

### 2.2 第一个函数模板示例

```cpp
#include <iostream>
using namespace std;

// 定义函数模板：求绝对值
template<typename T>
T myabs(T x) {
    return x < 0 ? -x : x;
}

int main() {
    int n = -5;
    double d = -5.5;
    float f = 3.0f;
    
    cout << myabs(n) << endl;  // 输出: 5
    cout << myabs(d) << endl;  // 输出: 5.5
    cout << myabs(f) << endl;  // 输出: 3
    
    return 0;
}
```

**运行结果**：
```
5
5.5
3
```

**编译器做了什么**：当你调用 `myabs(n)` 时，编译器看到 `n` 是 `int` 类型，就会自动生成一个 `int` 版本的函数：
```cpp
int myabs(int x) {
    return x < 0 ? -x : x;
}
```

### 2.3 显式实例化 vs 自动推断

**方式一：自动推断（推荐）**
```cpp
int i = max(35, 120);      // 编译器推断T为int
double d = max(172.5, 193.4);  // 编译器推断T为double
```

**方式二：显式指定类型**
```cpp
int i = max<int>(35, 120);
double d = max<double>(172.5, 193.4);
```

**什么时候需要显式指定？**
- 当编译器无法自动推断时
- 当你想强制使用某种类型时

### 2.4 多个类型参数

函数模板可以有多个类型参数：

```cpp
#include <iostream>
using namespace std;

// 两个类型参数
template<typename T1, typename T2>
T1 max(T1 a, T2 b) {
    return (b > a) ? b : a;
}

int main() {
    cout << max(7.5, 5) << endl;   // 输出: 7.5（T1=double, T2=int）
    cout << max(5, 7.5) << endl;  // 输出: 7（T1=int, T2=double）
    
    return 0;
}
```

**注意**：返回类型是 `T1`，所以第一个参数的类型决定了返回类型。

### 2.5 数组版函数模板

```cpp
#include <iostream>
#include <string>
using namespace std;

// 求数组中的最大值
template<typename T>
T max(const T* array, int size) {
    T max_val = array[0];
    for (int i = 1; i < size; ++i) {
        if (array[i] > max_val) {
            max_val = array[i];
        }
    }
    return max_val;
}

int main() {
    int ia[5] = {10, 7, 14, 3, 25};
    double da[6] = {10.2, 7.1, 14.5, 3.2, 25.6, 16.8};
    string sa[5] = {"上海", "北京", "沈阳", "广州", "武汉"};
    
    cout << "整数最大值为：" << max(ia, 5) << endl;
    cout << "实数最大值为：" << max(da, 6) << endl;
    cout << "字典排序最大为：" << max(sa, 5) << endl;
    
    return 0;
}
```

**运行结果**：
```
整数最大值为：25
实数最大值为：25.6
字典排序最大为：武汉
```

**通俗理解**：这个函数就像一个"万能评委"，无论是整数数组、浮点数数组还是字符串数组，它都能找出最大值。

### 2.6 函数模板与普通函数的重载

当存在同名普通函数和函数模板时，C++的调用优先级是：

1. **首先**找参数完全匹配的**普通函数**
2. **然后**找参数完全匹配的**模板函数**
3. **最后**尝试通过自动类型转换匹配的普通函数

```cpp
#include <iostream>
using namespace std;

// 函数模板
template<typename T>
T max(T a, T b) {
    cout << "调用函数模板" << endl;
    return (a > b) ? a : b;
}

// 普通函数（优先匹配）
int max(int a, int b) {
    cout << "调用普通函数" << endl;
    return (a > b) ? a : b;
}

int main() {
    int i1 = 10, i2 = 20;
    double d1 = 10.5, d2 = 20.5;
    
    max(i1, i2);   // 调用普通函数：参数完全匹配
    max(d1, d2);   // 调用函数模板：没有double版本的普通函数
    
    return 0;
}
```

### 2.7 函数模板的注意事项

1. **模板参数类型不能自动转换**
   ```cpp
   // 错误！模板匹配时不进行类型转换
   max(7.5, 5);  // error: 没有完全匹配的类型
   
   // 正确：显式指定类型
   max<double>(7.5, 5);
   ```

2. **只适用于参数个数相同、函数体相同的情况**
   - 如果参数个数不同，就不能用同一个函数模板

3. **模板代码通常放在头文件中**
   - 因为编译器需要看到模板定义才能实例化

---



## 3. 类模板

### 3.1 为什么需要类模板？

同样的问题：如果要写一个"比较"类来处理不同类型：

```cpp
// int版本
class Compare_int {
public:
    Compare_int(int a, int b) { x = a; y = b; }
    int max() { return (x > y) ? x : y; }
    int min() { return (x < y) ? x : y; }
private:
    int x, y;
};

// 如果还要处理float、double...太痛苦了！
```

**解决方案**：用类模板！

### 3.2 类模板的基本语法

```cpp
template<typename numtype>
class Compare {
public:
    Compare(numtype a, numtype b) : x(a), y(b) {}
    numtype max() { return (x > y) ? x : y; }
    numtype min() { return (x < y) ? x : y; }
private:
    numtype x, y;
};
```

### 3.3 类模板的使用

```cpp
#include <iostream>
using namespace std;

template<typename numtype>
class Compare {
public:
    Compare(numtype a, numtype b) : x(a), y(b) {}
    numtype max() { return (x > y) ? x : y; }
    numtype min() { return (x < y) ? x : y; }
private:
    numtype x, y;
};

int main() {
    // 创建不同类型的对象
    Compare<int> intComp(10, 20);
    Compare<double> doubleComp(3.14, 2.71);
    Compare<char> charComp('A', 'a');
    
    cout << "int最大: " << intComp.max() << endl;
    cout << "int最小: " << intComp.min() << endl;
    cout << "double最大: " << doubleComp.max() << endl;
    cout << "char最大: " << charComp.max() << endl;  // 'a'
    
    return 0;
}
```

**运行结果**：
```
int最大: 20
int最小: 10
double最大: 3.14
char最大: a
```

### 3.4 类模板外定义成员函数

当在类模板外部定义成员函数时，也需要带上模板声明：

```cpp
template<typename T>
class Store {
public:
    Store();                  // 构造函数
    T GetElem();              // 获取元素
    void PutElem(T x);        // 存放元素
private:
    T item;
    int valued;               // 标记是否有值
};

// 类外定义构造函数
template<typename T>
Store<T>::Store() : valued(0) { }

// 类外定义获取元素函数
template<typename T>
T Store<T>::GetElem() {
    if (valued == 0) {
        cout << "No item present!" << endl;
        exit(1);
    }
    return item;
}

// 类外定义存放元素函数
template<typename T>
void Store<T>::PutElem(T x) {
    valued++;
    item = x;
}
```

**注意**：类名后的 `<T>` 不能省略！

### 3.5 类模板应用示例：顺序表

顺序表是一种最基础的数据结构，用类模板实现：

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

// 顺序表类模板
template <typename T, int size>
class SeqList {
private:
    T data[size];      // 存储数据的数组
    int maxSize;       // 最大容量
    int last;          // 最后一个元素的下标

public:
    SeqList() { last = -1; maxSize = size; }
    
    // 获取表长度
    int Length() const { return last + 1; }
    
    // 判断是否为空
    bool IsEmpty() const { return last == -1; }
    
    // 判断是否已满
    bool IsFull() const { return last == maxSize - 1; }
    
    // 查找元素位置
    int Find(const T& x) const {
        int i = 0;
        while (i <= last && data[i] != x) i++;
        if (i > last) return -1;  // 未找到
        return i;                 // 返回下标
    }
    
    // 判断元素是否存在
    bool IsIn(const T& x) const {
        return Find(x) != -1;
    }
    
    // 插入元素到第i个位置（下标从0开始）
    bool Insert(const T& x, int i) {
        if (i < 0 || i > last + 1 || IsFull()) return false;
        for (int j = last; j >= i; j--) {
            data[j + 1] = data[j];
        }
        data[i] = x;
        last++;
        return true;
    }
    
    // 删除元素
    bool Remove(const T& x) {
        int pos = Find(x);
        if (pos == -1) return false;
        for (int j = pos; j < last; j++) {
            data[j] = data[j + 1];
        }
        last--;
        return true;
    }
    
    // 获取第i个元素
    T& operator[](int i) {
        if (i < 0 || i > last) {
            cout << "下标越界！" << endl;
            exit(1);
        }
        return data[i];
    }
};

// 学生结构体
struct Student {
    int id;
    int age;
};

int main() {
    // 创建整型顺序表
    SeqList<int, 100> intList;
    
    // 插入一些数据
    intList.Insert(2, 0);
    intList.Insert(3, 1);
    intList.Insert(5, 2);
    intList.Insert(7, 3);
    
    // 使用下标访问
    cout << "表长度: " << intList.Length() << endl;
    cout << "第2个元素: " << intList[1] << endl;
    
    // 查找元素
    int key = 5;
    if (intList.IsIn(key)) {
        cout << key << " 在表中，下标为: " << intList.Find(key) << endl;
    }
    
    // 删除元素
    intList.Remove(5);
    cout << "删除5后表长度: " << intList.Length() << endl;
    
    return 0;
}
```

---



### 3.5 模板的深入应用

#### 3.5.1 非类型模板参数

模板参数不一定是类型，也可以是具体的值：

```cpp
#include <iostream>
using namespace std;

// 固定大小的数组类模板
template<typename T, int size>
class Array {
private:
    T element[size];
    int num;
public:
    Array() : num(size) {}
    int getSize() const { return num; }
};

int main() {
    Array<int, 30> a1;    // 30个int的数组
    Array<double, 40> a2; // 40个double的数组
    
    cout << "a1大小: " << a1.getSize() << endl;
    cout << "a2大小: " << a2.getSize() << endl;
    
    return 0;
}
```

**注意**：非类型参数通常是整数类型（int、char等）或指针。

#### 3.5.2 模板参数默认值

C++11开始支持模板参数默认值：

```cpp
#include <iostream>
using namespace std;

template<typename T = int, int size = 100>
class Stack {
private:
    T data[size];
    int top;
public:
    Stack() : top(-1) {}
    void push(T val) { data[++top] = val; }
    T pop() { return data[top--]; }
};

int main() {
    // 使用默认参数
    Stack<> s1;  // Stack<int, 100>
    
    // 指定参数
    Stack<double, 50> s2;
    
    return 0;
}
```

#### 3.5.3 模板特化

有时需要对特定类型进行特殊处理：

```cpp
#include <iostream>
#include <cstring>
using namespace std;

// 通用模板
template<typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}

// 字符数组的特化版本
template<>
char* max<char*>(char* a, char* b) {
    return strcmp(a, b) > 0 ? a : b;
}

int main() {
    cout << max(10, 20) << endl;           // 调用通用模板
    cout << max("apple", "banana") << endl; // 调用特化版本
    
    return 0;
}
```

#### 3.5.4 模板分离式编程的注意事项

**重要**：类模板的声明和实现通常要放在同一个头文件中！

```cpp
// ❌ 错误做法：分开存放会导致链接错误
// mytemplate.h (只有声明)
// mytemplate.cpp (只有实现)

// ✅ 正确做法：全部放在头文件中
// mytemplate.h
template<typename T>
class MyTemplate {
public:
    void doSomething(T val);
};

template<typename T>
void MyTemplate<T>::doSomething(T val) {
    // 实现
}
```

**原因**：模板在编译时需要看到完整定义才能实例化。

---



## 4. 顺序表

### 4.1 顺序表 vs 链表

| 特性 | 顺序表 | 链表 |
|------|--------|------|
| 存储方式 | 连续内存 | 分散内存+指针 |
| 访问速度 | O(1) 随机访问 | O(n) 顺序访问 |
| 插入删除 | O(n) 需要移动 | O(1) 修改指针 |
| 内存利用率 | 固定大小，可能浪费 | 按需分配 |

**通俗理解**：
- 顺序表就像电影院的座位，固定位置，插入要"挪人"
- 链表就像游乐场的排队手环，随时可以插队

### 4.2 顺序表类模板实现

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

### 4.3 顺序表使用示例

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

### 4.4 顺序表操作复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| 访问元素 `[]` | O(1) | 直接通过下标访问 |
| 查找元素 | O(n) | 最坏情况遍历整个表 |
| 插入元素 | O(n) | 需要移动元素 |
| 删除元素 | O(n) | 需要移动元素 |

---



## 5. 查找算法

#### 7.1.1 什么是查找？

**查找（Search）**：在数据集合中寻找满足条件的数据。

**关键字（Key）**：用于识别数据的字段，如学号、身份证号等。

#### 7.1.2 顺序查找

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

#### 7.1.3 折半查找（二分查找）

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

#### 7.1.4 分块查找（索引查找）

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



## 6. 排序算法

#### 7.2.1 什么是排序？

**排序（Sorting）**：将一组数据按照关键字有序排列。

**稳定排序 vs 不稳定排序**：
- 稳定：相等的元素排序后相对位置不变
- 不稳定：相等的元素排序后相对位置可能改变

#### 7.2.2 插入排序

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

#### 7.2.3 冒泡排序

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

#### 7.2.4 选择排序

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

#### 7.2.5 对半插入排序（二分插入排序）

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

#### 7.2.6 希尔排序（缩小增量排序）

**思想**：改进自插入排序，通过设置**增量（gap）**将数组分成多个子序列，对每个子序列进行插入排序，然后逐步缩小增量，直到增量为1。

**通俗理解**：就像先按姓氏分组排序，再按名字排序，最后整体有序。

```cpp
#include <iostream>
using namespace std;

// 希尔排序（模板函数版本）
template<typename T>
void ShellSort(T slist[], int last) {
    int gap = (last + 1) / 2;  // 初始增量
    
    while (gap > 0) {
        // 对每个子序列进行插入排序
        for (int i = gap; i <= last; i++) {
            T temp = slist[i];
            int j = i;
            
            // 在子序列中向后查找插入位置
            while (j >= gap && temp < slist[j - gap]) {
                slist[j] = slist[j - gap];  // 元素后移
                j -= gap;
            }
            slist[j] = temp;  // 插入元素
        }
        
        gap /= 2;  // 缩小增量
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
    int arr[] = {20, 26, 49, 21, 15, 6, 9};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    cout << "排序前: ";
    PrintArray(arr, n);
    
    ShellSort(arr, n - 1);
    
    cout << "排序后: ";
    PrintArray(arr, n);
    
    return 0;
}
```

**排序过程演示**（以 {20, 26, 49, 21, 15, 6, 9} 为例）：
```
初始数组: [20, 26, 49, 21, 15, 6, 9]
          (N=7, 初始增量gap=7/2=4)

第1轮(gap=4):
  分组: Group1=[20,6], Group2=[26,9], Group3=[49,15], Group4=[21]
  组内排序后: [6, 9, 15, 20, 21, 26, 49]

第2轮(gap=2):
  分组: Group1=[6,15,21], Group2=[9,20,26]
  组内排序后: [6, 9, 15, 20, 21, 26, 49]

第3轮(gap=1):
  直接插入排序: [6, 9, 15, 20, 21, 26, 49]

结果: [6, 9, 15, 20, 21, 26, 49]
```

**重要特点**：
- 每一趟排序包含若干子序列，第一个子序列第一个元素是0号，第二个元素是gap号
- 每趟排序从第一个子序列开始直接插入排序，穿插完成所有子序列
- 增量逐步缩小（gap/=2），直到为1时就是直接插入排序

**复杂度**：平均 O(n^1.3)，最坏 O(n²)，比直接插入排序性能好。

#### 7.2.7 快速排序

**思想**：从数组中选择一个**基准元素（pivot）**，将数组分为两部分：左边所有元素小于基准，右边所有元素大于等于基准。然后对左右两部分递归执行同样的操作。

**通俗理解**：就像排队找座位，让每个人先和一个"标准人"比较，比他矮的站左边，比他高的站右边，然后再分别在这两组里找标准人分组。

```cpp
#include <iostream>
using namespace std;

// 一趟快速排序（分割函数）
template<typename T>
int Partition(T slist[], int low, int high) {
    T temp = slist[low];  // 基准元素
    int i = low, j = high;
    
    while (i < j) {
        // 从右往左找第一个小于基准的元素
        while (temp <= slist[j] && i < j) j--;
        if (i < j) {
            slist[i] = slist[j];  // 移到左边
            i++;
        }
        
        // 从左往右找第一个大于基准的元素
        while (temp > slist[i] && i < j) i++;
        if (i < j) {
            slist[j] = slist[i];  // 移到右边
            j--;
        }
    }
    
    slist[i] = temp;  // 基准元素归位
    return i;  // 返回基准位置
}

// 快速排序（递归版本）
template<typename T>
void QuickSort(T slist[], int low, int high) {
    if (low < high) {
        int pivotPos = Partition(slist, low, high);  // 分割
        
        QuickSort(slist, low, pivotPos - 1);   // 对左半部分排序
        QuickSort(slist, pivotPos + 1, high);  // 对右半部分排序
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
    int arr[] = {49, 38, 65, 97, 76, 13, 27};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    cout << "排序前: ";
    PrintArray(arr, n);
    
    QuickSort(arr, 0, n - 1);
    
    cout << "排序后: ";
    PrintArray(arr, n);
    
    return 0;
}
```

**排序过程演示**（以 {49, 38, 65, 97, 76, 13, 27} 为例）：
```
初始: [49, 38, 65, 97, 76, 13, 27]
      ↑
     pivot

第1趟分割:
  基准=49，从右往左找小于49的: 27 < 49 → 移到左边
  [27, 38, 65, 97, 76, 13, 49]
                   ↑
  从左往右找大于49的: 65 > 49 → 移到右边
  [27, 38, 49, 97, 76, 13, 65]
        ↑
  继续...直到i=j
  基准归位: [27, 38, 13, 49, 76, 97, 65]
                   ↑
           pivot=49已归位

递归处理左右两部分:
  左: [27, 38, 13] → 基准38 → [27, 13, 38]
  右: [76, 97, 65] → 基准76 → [65, 76, 97]

最终: [13, 27, 38, 49, 65, 76, 97]
```

**特点**：
- 一趟排序后，基准元素在准确的排序位置上
- 递归处理左右两部分，直到数组大小为1
- 平均性能很好，是应用最广泛的排序算法

**复杂度**：平均 O(n log n)，最坏 O(n²)，空间 O(log n)。

#### 7.2.8 归并排序（分而治之）

**思想**：采用**分治法**策略：
1. **分**：把大的数组分成两个小的数组，递归地分割，直到每个子数组只有一个元素（有序）
2. **治**：将两个有序的小数组合并成一个有序的大数组

**通俗理解**：就像把一副牌分成两堆排好序，再把两堆有序的牌合并成一堆。

```cpp
#include <iostream>
using namespace std;

// 合并两个有序数组
template<typename T>
void Merge(T slist[], T temp[], int low, int mid, int high) {
    // slist[low...mid] 和 slist[mid+1...high] 是两个有序数组
    int i = low, j = mid + 1, k = low;
    
    // 合并到临时数组
    while (i <= mid && j <= high) {
        if (slist[i] <= slist[j]) {
            temp[k++] = slist[i++];
        } else {
            temp[k++] = slist[j++];
        }
    }
    
    // 处理剩余元素
    while (i <= mid) {
        temp[k++] = slist[i++];
    }
    while (j <= high) {
        temp[k++] = slist[j++];
    }
    
    // 复制回原数组
    for (int i = low; i <= high; i++) {
        slist[i] = temp[i];
    }
}

// 归并排序递归函数
template<typename T>
void MergeSort(T slist[], T temp[], int low, int high) {
    if (low < high) {
        int mid = (low + high) / 2;  // 取中点分割
        
        MergeSort(slist, temp, low, mid);      // 对左半部分排序
        MergeSort(slist, temp, mid + 1, high); // 对右半部分排序
        Merge(slist, temp, low, mid, high);    // 合并两个有序数组
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
    int arr[] = {26, 18, 20, 49, 21, 9, 6, 15};
    int n = sizeof(arr) / sizeof(arr[0]);
    int* temp = new int[n];  // 临时数组
    
    cout << "排序前: ";
    PrintArray(arr, n);
    
    MergeSort(arr, temp, 0, n - 1);
    
    cout << "排序后: ";
    PrintArray(arr, n);
    
    delete[] temp;
    return 0;
}
```

**排序过程演示**（分治过程）：
```
初始: [26, 18, 20, 49, 21, 9, 6, 15]

分（分割过程）:
  [26, 18, 20, 49 | 21, 9, 6, 15]
  [26, 18 | 20, 49]  [21, 9 | 6, 15]
  [26 | 18]  [20 | 49]  [21 | 9]  [6 | 15]
  
  现在每个子数组只有一个元素（天然有序）

合（合并过程）:
  [18, 26]  [20, 49]  [9, 21]  [6, 15]
  [18, 20, 26, 49]  [6, 9, 15, 21]
  [6, 9, 15, 18, 20, 21, 26, 49]

结果: [6, 9, 15, 18, 20, 21, 26, 49]
```

**合并算法核心**（两个有序数组合并为一个有序数组）：
```
有序数组A: [18, 26]  有序数组B: [6, 9, 15, 21]

比较过程:
  18 vs 6  → 取6 → temp=[6]
  18 vs 9  → 取9 → temp=[6,9]
  18 vs 15 → 取15 → temp=[6,9,15]
  18 vs 21 → 取18 → temp=[6,9,15,18]
  26 vs 21 → 取21 → temp=[6,9,15,18,21]
  A已空 → 取26 → temp=[6,9,15,18,21,26]

合并完成: [6, 9, 15, 18, 21, 26]
```

**特点**：
- 稳定排序：相等的元素相对位置不变
- 需要额外的 O(n) 空间存储临时数组
- 适合外部排序（如大文件排序）

**复杂度**：无论最好、最坏、平均都是 O(n log n)。

#### 7.2.9 排序算法对比

| 算法 | 时间复杂度 | 空间复杂度 | 稳定性 |
|------|------------|------------|--------|
| 直接插入排序 | O(n²) | O(1) | 稳定 |
| 对半插入排序 | O(n²) | O(1) | 稳定 |
| 冒泡排序 | O(n²) | O(1) | 稳定 |
| 选择排序 | O(n²) | O(1) | 不稳定 |
| 希尔排序 | O(n^1.3) | O(1) | 不稳定 |
| 快速排序 | O(n log n) | O(log n) | 不稳定 |
| 归并排序 | O(n log n) | O(n) | 稳定 |
| 堆排序 | O(n log n) | O(1) | 不稳定 |

**对于初学者**：重点掌握冒泡、插入、选择三种简单排序，理解排序的思想即可。

---



### 7.1 常见错误与调试

#### 7.1.1 常见编译错误

**错误1：模板参数不匹配**
```cpp
template<typename T>
T add(T a, T b) { return a + b; }

add(5, 3.14);  // ❌ error: 类型不一致
add<int>(5, 3.14);  // ✅ 显式指定类型
```

**错误2：忘记类模板后的 `<T>`**
```cpp
template<typename T>
class MyClass {
    void func();
};

// ❌ 错误
void MyClass::func() { }

// ✅ 正确
template<typename T>
void MyClass<T>::func() { }
```

**错误3：模板定义不在头文件中**
```cpp
// ❌ 常见错误
// .h文件只有声明
// .cpp文件有实现
// 链接时会报错：undefined reference
```

#### 7.1.2 调试技巧

1. **先让普通版本工作**：先写一个 `int` 版本的代码，确认逻辑正确后再改成模板。

2. **使用 `static_assert`**（C++11）：
   ```cpp
   template<typename T>
   T divide(T a, T b) {
       static_assert(std::is_integral<T>::value, "T必须是整数类型");
       return a / b;
   }
   ```

3. **查看模板实例化错误信息**：编译器会显示生成的代码，很长但能帮助定位问题。

---



### 7.2 练习建议

### 基础练习

1. **实现一个 `Swap` 函数模板**：交换两个同类型变量的值。

2. **实现一个 `ArrayMax` 函数模板**：返回数组中的最大元素。

3. **实现一个 `Stack` 类模板**：包含 `push`、`pop`、`isEmpty` 方法。

### 进阶练习

4. **实现一个 `Pair` 类模板**：存储一对数据，提供 `getFirst()` 和 `getSecond()` 方法。

5. **实现一个 `Matrix` 类模板**：支持矩阵转置和乘法运算。

6. **实现排序算法模板**：实现一个通用的 `sort` 函数模板（可以先实现冒泡排序）。

### 思考题

7. 什么时候应该用模板？什么时候应该用普通函数重载？

8. 模板会让代码变慢还是变快？

---



### 7.3 本章小结

1. **模板是C++泛型编程的基础**，让我们写出与类型无关的代码。

2. **函数模板**用 `template<typename T>` 定义，编译器会自动实例化。

3. **类模板**让我们可以创建支持多种类型的类。

4. **模板参数**可以是类型参数，也可以是非类型参数（具体值）。

5. **模板代码通常放在头文件中**，因为需要看到完整定义才能实例化。

---

*返回 [主目录](../README.md)*


## 7. 本章小结

- 模板解决“逻辑相同、类型不同”的重复。
- 类模板常用于容器。
- 顺序表访问快，插入删除可能慢。
- 顺序查找不要求有序，折半查找必须有序。
- 排序要理解原理，工程中优先用标准库。

[返回主目录](../README.md)

[返回主目录](../README.md)
