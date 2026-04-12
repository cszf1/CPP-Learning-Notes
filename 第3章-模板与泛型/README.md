# 第3章 模板与泛型

> ⭐ **核心章节** - C++泛型编程的基础

---

## 📋 目录

1. [为什么需要模板？](#1-为什么需要模板)
2. [函数模板](#2-函数模板)
3. [类模板](#3-类模板)
4. [模板的深入应用](#4-模板的深入应用)
5. [常见错误与调试](#5-常见错误与调试)
6. [练习建议](#6-练习建议)

---

## 1. 为什么需要模板？

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

## 4. 模板的深入应用

### 4.1 非类型模板参数

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

### 4.2 模板参数默认值

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

### 4.3 模板特化

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

### 4.4 模板分离式编程的注意事项

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

## 5. 常见错误与调试

### 5.1 常见编译错误

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

### 5.2 调试技巧

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

## 6. 练习建议

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

## 📝 本章小结

1. **模板是C++泛型编程的基础**，让我们写出与类型无关的代码。

2. **函数模板**用 `template<typename T>` 定义，编译器会自动实例化。

3. **类模板**让我们可以创建支持多种类型的类。

4. **模板参数**可以是类型参数，也可以是非类型参数（具体值）。

5. **模板代码通常放在头文件中**，因为需要看到完整定义才能实例化。

---

*返回 [主目录](../README.md)*
