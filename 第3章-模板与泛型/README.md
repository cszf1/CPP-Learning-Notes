# 第3章 模板与泛型

> 本章介绍C++的模板机制，包括函数模板和类模板，让你学会编写"一次编写，多种类型通用"的代码。

---

## 📋 目录

1. [为什么需要模板](#1-为什么需要模板)
2. [函数模板](#2-函数模板)
3. [类模板](#3-类模板)
4. [模板的深入应用](#4-模板的深入应用)
5. [模板与STL的关系](#5-模板与stl的关系)
6. [常见错误与注意事项](#6-常见错误与注意事项)
7. [练习建议](#7-练习建议)

---

## 1. 为什么需要模板

### 1.1 没有模板的痛苦

假设你要写一个比较两个数大小的函数，可能会这样写：

```cpp
// 整数版本
int max(int a, int b) {
    return a > b ? a : b;
}

// 浮点数版本
double max(double a, double b) {
    return a > b ? a : b;
}

// 字符版本
char max(char a, char b) {
    return a > b ? a : b;
}
```

如果还要支持 `long`、`short`、`unsigned int` 呢？代码会变得又臭又长，而且每份代码的逻辑都是一样的，纯粹是类型不同而已。

### 1.2 模板的解决方案

**模板（Template）** 就是C++给你的一把"万能钥匙"——你只写一份代码，编译器帮你生成针对不同类型的具体代码。

```cpp
// 模板版本：一个函数搞定所有类型！
template<typename T>
T max(T a, T b) {
    return a > b ? a : b;
}
```

现在你只需要调用：
```cpp
cout << max(3, 5) << endl;          // 整数
cout << max(3.14, 2.71) << endl;    // 浮点数
cout << max('a', 'z') << endl;      // 字符
```

编译器会根据你传入的参数类型，自动生成对应的函数。

---

## 2. 函数模板

### 2.1 基本语法

```cpp
template<typename T>
返回类型 函数名(参数列表) {
    // 函数体
}
```

或者使用 `class` 关键字（效果相同）：
```cpp
template<class T>
返回类型 函数名(参数列表) {
    // 函数体
}
```

**解释**：
- `template` - 声明这是一个模板
- `typename T` 或 `class T` - 声明一个类型参数 `T`，`T` 只是一个名字，可以换成任何你喜欢的名字，如 `T`、`Type`、`Value` 等
- 类型参数 `T` 就像一个"占位符"，在函数被调用时会替换成具体的类型

### 2.2 通俗理解

可以把 `typename T` 想象成一个"会自动填写的表单"：
- 当你写 `max(3, 5)` 时，`T` 就自动变成 `int`
- 当你写 `max(3.14, 2.71)` 时，`T` 就自动变成 `double`
- 编译器在编译阶段会帮你生成对应类型的函数

### 2.3 完整示例：交换两个数

```cpp
#include <iostream>
using namespace std;

// 函数模板：交换任意类型的两个数
template<typename T>
void swap(T& a, T& b) {
    T temp = a;  // 注意：这里创建了一个 T 类型的临时变量
    a = b;
    b = temp;
}

int main() {
    // 交换整数
    int x = 10, y = 20;
    cout << "交换前: x = " << x << ", y = " << y << endl;
    swap(x, y);
    cout << "交换后: x = " << x << ", y = " << y << endl;
    
    cout << "---" << endl;
    
    // 交换浮点数
    double p = 3.14, q = 2.71;
    cout << "交换前: p = " << p << ", q = " << q << endl;
    swap(p, q);
    cout << "交换后: p = " << p << ", q = " << q << endl;
    
    return 0;
}
```

**运行结果**：
```
交换前: x = 10, y = 20
交换后: x = 20, y = 10
---
交换前: p = 3.14, q = 2.71
交换后: p = 2.71, q = 3.14
```

### 2.4 多个类型参数

函数模板可以有多个类型参数：

```cpp
#include <iostream>
using namespace std;

// 两个不同的类型参数
template<typename T1, typename T2>
void printBoth(T1 a, T2 b) {
    cout << "第一个值: " << a << " (类型参数T1)" << endl;
    cout << "第二个值: " << b << " (类型参数T2)" << endl;
}

// 返回值类型可以与参数类型不同
template<typename T1, typename T2>
T1 maxOfDifferentTypes(T1 a, T2 b) {
    return (a > b) ? a : b;  // 返回T1类型
}

int main() {
    printBoth("Hello", 42);        // 字符串和整数
    printBoth(3.14, 'A');         // 浮点数和字符
    
    cout << "---" << endl;
    
    int result = maxOfDifferentTypes(10, 3.14);  // 返回int类型
    cout << "较大值: " << result << endl;
    
    return 0;
}
```

### 2.5 函数模板的显式实例化

有时候你想控制模板的具体化过程，可以用显式实例化：

```cpp
#include <iostream>
using namespace std;

template<typename T>
T add(T a, T b) {
    return a + b;
}

// 显式实例化：告诉编译器现在就生成int版本的函数
template int add<int>(int, int);

int main() {
    // 如果某处调用了 add() 但你没有显式实例化，
    // 编译器会隐式实例化（按需生成）
    cout << add(3, 5) << endl;
    return 0;
}
```

### 2.6 普通函数与函数模板的重载

当普通函数和函数模板都能匹配时，**普通函数优先**：

```cpp
#include <iostream>
using namespace std;

template<typename T>
T max(T a, T b) {
    cout << "调用模板函数" << endl;
    return a > b ? a : b;
}

// 普通函数版本
int max(int a, int b) {
    cout << "调用普通函数" << endl;
    return a > b ? a : b;
}

int main() {
    cout << max(3, 5) << endl;      // 调用普通函数（优先匹配）
    cout << max(3.14, 2.71) << endl; // 调用模板函数（只有模板匹配）
    return 0;
}
```

**运行结果**：
```
调用普通函数
8
调用模板函数
3.14
```

---

## 3. 类模板

### 3.1 为什么需要类模板

类模板允许你创建一个可以处理多种数据类型的类。最典型的例子就是 **STL中的容器类**，比如 `vector` 可以存放任何类型的元素：

```cpp
vector<int> intVec;      // 存放整数的向量
vector<double> doubleVec; // 存放浮点数的向量
vector<string> strVec;    // 存放字符串的向量
```

### 3.2 类模板的基本语法

```cpp
template<typename T>
class 类名 {
private:
    T 成员变量;  // 使用类型参数 T
public:
    类名(T param);           // 构造函数
    T getValue();            // 成员函数
    void setValue(T value);
};
```

**类模板的成员函数定义**：
在类外定义模板类的成员函数时，必须重复 `template<typename T>`：

```cpp
template<typename T>
返回类型 类名<T>::成员函数名(参数) {
    // 函数体
}
```

### 3.3 完整示例：简单的 Stack 类

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
using namespace std;

// 类模板：实现一个简单的栈
template<typename T>
class Stack {
private:
    vector<T> data;  // 使用vector存储数据
    
public:
    // 入栈
    void push(T value) {
        data.push_back(value);
    }
    
    // 出栈
    T pop() {
        if (data.empty()) {
            throw out_of_range("Stack is empty!");
        }
        T top = data.back();
        data.pop_back();
        return top;
    }
    
    // 查看栈顶元素（不出栈）
    T& top() {
        if (data.empty()) {
            throw out_of_range("Stack is empty!");
        }
        return data.back();
    }
    
    // 判断栈是否为空
    bool empty() const {
        return data.empty();
    }
    
    // 获取栈的大小
    size_t size() const {
        return data.size();
    }
};

int main() {
    // 整数栈
    Stack<int> intStack;
    intStack.push(10);
    intStack.push(20);
    intStack.push(30);
    
    cout << "整数栈大小: " << intStack.size() << endl;
    cout << "栈顶元素: " << intStack.top() << endl;
    cout << "弹出: " << intStack.pop() << endl;
    cout << "弹出后栈顶: " << intStack.top() << endl;
    
    cout << "---" << endl;
    
    // 字符串栈
    Stack<string> strStack;
    strStack.push("Hello");
    strStack.push("World");
    cout << "字符串栈顶: " << strStack.top() << endl;
    
    return 0;
}
```

**运行结果**：
```
整数栈大小: 3
栈顶元素: 30
弹出: 30
弹出后栈顶: 20
---
字符串栈顶: World
```

### 3.4 类模板的特化

有时你可能想让某种特定类型使用不同的实现，这就是 **模板特化**：

```cpp
#include <iostream>
#include <cstring>
using namespace std;

// 通用类模板
template<typename T>
class Comparator {
public:
    static bool isEqual(T a, T b) {
        return a == b;
    }
};

// 字符数组的特化版本
template<>
class Comparator<char*> {
public:
    static bool isEqual(char* a, char* b) {
        return strcmp(a, b) == 0;  // 字符串比较用strcmp
    }
};

int main() {
    // 使用通用模板
    cout << Comparator<int>::isEqual(3, 3) << endl;      // 1 (true)
    cout << Comparator<int>::isEqual(3, 5) << endl;      // 0 (false)
    
    // 使用特化版本
    char str1[] = "hello";
    char str2[] = "hello";
    char str3[] = "world";
    
    cout << Comparator<char*>::isEqual(str1, str2) << endl;  // 1 (true)
    cout << Comparator<char*>::isEqual(str1, str3) << endl;  // 0 (false)
    
    return 0;
}
```

### 3.5 类模板的多参数

类模板可以有多于一个类型参数：

```cpp
#include <iostream>
using namespace std;

// 两个类型参数的模板
template<typename T1, typename T2>
class Pair {
private:
    T1 first;
    T2 second;
    
public:
    Pair(T1 f, T2 s) : first(f), second(s) {}
    
    T1 getFirst() { return first; }
    T2 getSecond() { return second; }
    
    void print() {
        cout << "First: " << first << ", Second: " << second << endl;
    }
};

int main() {
    Pair<int, string> student(1, "Alice");
    student.print();
    
    Pair<double, char> mixed(3.14, 'A');
    mixed.print();
    
    return 0;
}
```

---

## 4. 模板的深入应用

### 4.1 非类型模板参数

除了类型参数，模板还可以接受**具体的值**作为参数：

```cpp
#include <iostream>
using namespace std;

// 非类型模板参数：指定数组大小
template<int N>
class ArrayWrapper {
private:
    int data[N];
    int currentSize;
    
public:
    ArrayWrapper() : currentSize(0) {}
    
    void add(int value) {
        if (currentSize < N) {
            data[currentSize++] = value;
        }
    }
    
    void print() {
        cout << "Array (size " << currentSize << "/" << N << "): ";
        for (int i = 0; i < currentSize; i++) {
            cout << data[i] << " ";
        }
        cout << endl;
    }
};

int main() {
    ArrayWrapper<5> smallArray;  // 最多5个元素
    ArrayWrapper<10> bigArray;   // 最多10个元素
    
    for (int i = 1; i <= 7; i++) {
        smallArray.add(i * 10);
    }
    smallArray.print();
    
    return 0;
}
```

**运行结果**：
```
Array (size 5/5): 10 20 30 40 50 
```

### 4.2 模板参数默认值

类模板的参数可以有默认值：

```cpp
#include <iostream>
using namespace std;

template<typename T, int SIZE = 10>
class FixedArray {
private:
    T data[SIZE];
    int length;
    
public:
    FixedArray() : length(0) {}
    
    void add(T value) {
        if (length < SIZE) {
            data[length++] = value;
        }
    }
    
    void print() {
        cout << "Array: ";
        for (int i = 0; i < length; i++) {
            cout << data[i] << " ";
        }
        cout << endl;
    }
};

int main() {
    FixedArray<int> defaultSize;    // 默认大小10
    FixedArray<int, 5> customSize;  // 自定义大小5
    
    for (int i = 0; i < 6; i++) {
        defaultSize.add(i);
        customSize.add(i);
    }
    
    defaultSize.print();   // 输出6个元素
    customSize.print();    // 输出5个元素
    
    return 0;
}
```

### 4.3 模板的编译与分离式编程

⚠️ **重要提醒**：模板的声明和实现**通常必须放在同一个文件中**，因为模板需要**在编译时实例化**，编译器需要看到完整的模板定义。

下面这种分离式编程（.h + .cpp）对普通函数可以，但**对模板不行**：

```cpp
// ❌ 错误示范
// Student.h
template<typename T>
class Student {
    void display();  // 只声明，不实现
};

// Student.cpp
template<typename T>
void Student<T>::display() {
    // 实现
}

// main.cpp
#include "Student.h"
Student<int> s;  // 编译错误！编译器找不到display的实现
```

**正确做法**：将模板的声明和实现都放在头文件中：

```cpp
// ✅ 正确做法：全部放在 .h 文件中
// Student.h
template<typename T>
class Student {
public:
    void display();  // 声明
};

// 模板的实现也放在 .h 文件中
template<typename T>
void Student<T>::display() {
    cout << "Student template" << endl;
}
```

### 4.4 typename 的另一种用法

`typename` 还可以用来明确告诉编译器**这是一个类型名**，避免解析歧义：

```cpp
#include <iostream>
using namespace std;

template<typename T>
class MyClass {
public:
    // 嵌套类型
    typedef T InternalType;
    
    // 使用typename明确这是类型名
    typename T::SubType* createObject() {
        // ...
        return nullptr;
    }
};
```

---

## 5. 模板与STL的关系

理解模板后，你就能更好地理解STL（标准模板库）了：

| STL组件 | 基于模板的实现 |
|---------|---------------|
| `vector<T>` | 动态数组，存放任意类型T |
| `list<T>` | 双向链表 |
| `map<K, V>` | 键值对映射，K是键类型，V是值类型 |
| `set<T>` | 集合，不允许重复 |
| `stack<T>` | 栈 |
| `queue<T>` | 队列 |

**举例 - vector 的简化原理**：
```cpp
// vector 的简化版实现（帮你理解原理）
template<typename T>
class vector {
private:
    T* data;      // 指向动态数组的指针
    size_t size;  // 元素个数
    size_t capacity; // 容量
    
public:
    void push_back(const T& value);  // 添加元素
    T& operator[](size_t index);      // 下标访问
    // ...其他成员函数
};
```

---

## 6. 常见错误与注意事项

### 6.1 常见错误

| 错误类型 | 示例 | 说明 |
|---------|------|------|
| 类型不匹配 | `max(1, 2.0)` | 两个参数类型不一致，需要显式指定或类型转换 |
| 模板参数推导失败 | `swap(a, b)` 其中 a 和 b 类型不同 | 编译器无法推导类型 |
| 忘记模板参数 | `MyClass obj;` | 类模板使用时必须指定类型参数 |

### 6.2 注意事项

1. **模板定义要完整**：编译器需要看到完整的模板定义才能实例化
2. **模板不编译成具体代码**：模板本身不生成可执行代码，只有被调用时才会生成
3. **模板会增加编译时间**：但运行时代价与普通函数相同
4. **调试时注意查看实例化错误**：模板错误信息可能很长，但耐心看总能定位问题

### 6.3 模板错误信息太长怎么办？

当你写错模板代码时，编译器会生成很长的错误信息。可以尝试：

1. 使用 IDE（如CLion、VS Code）的提示功能
2. 简化错误信息：找到第一个错误，通常那是根源
3. 用简单的测试用例缩小问题范围

---

## 7. 练习建议

### 基础练习

1. **练习1**：编写一个函数模板 `findMax`，找出数组中的最大值

```cpp
// 提示：函数签名
template<typename T>
T findMax(T arr[], int n);
```

2. **练习2**：实现一个类模板 `Pair`，存储两个任意类型的值

3. **练习3**：编写一个函数模板 `sort`，对任意类型的数组排序（可以使用简单的冒泡排序）

### 进阶练习

4. **练习4**：实现一个模板类 `CircularBuffer`，循环缓冲区

5. **练习5**：实现模板特化，处理字符指针的特化版本

6. **练习6**：使用非类型模板参数，实现一个固定大小的矩阵类

### 答案参考

<details>
<summary>练习1参考答案：findMax</summary>

```cpp
#include <iostream>
using namespace std;

template<typename T>
T findMax(T arr[], int n) {
    T maxVal = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > maxVal) {
            maxVal = arr[i];
        }
    }
    return maxVal;
}

int main() {
    int intArr[] = {3, 7, 2, 9, 4};
    cout << "最大值: " << findMax(intArr, 5) << endl;
    
    double doubleArr[] = {3.14, 2.71, 1.41, 1.73};
    cout << "最大值: " << findMax(doubleArr, 4) << endl;
    
    return 0;
}
```
</details>

---

## 📝 本章小结

| 概念 | 说明 |
|------|------|
| **函数模板** | 用 `template<typename T>` 声明，编写与类型无关的函数 |
| **类模板** | 用 `template<typename T>` 声明，定义支持多种类型的类 |
| **类型参数** | 用 `typename T` 或 `class T` 声明，代表任意类型 |
| **非类型参数** | 用具体值作为模板参数，如 `int N` |
| **模板特化** | 为特定类型提供特殊实现 |
| **模板实例化** | 编译器根据调用时的类型生成具体代码 |

**核心思想**：模板实现了C++的**泛型编程**，让你"一次编写，处处适用"。

---

*返回 [主目录](../README.md)*
