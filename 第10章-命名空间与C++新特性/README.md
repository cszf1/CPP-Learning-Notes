# 第10章 命名空间与C++新特性

> ⭐ **重点章节** - 现代C++的必备知识

---

## 📋 目录

1. [命名空间（namespace）](#1-命名空间namespace)
2. [智能指针](#2-智能指针)
3. [lambda表达式](#3-lambda表达式)
4. [auto关键字](#4-auto关键字)
5. [范围for循环](#5-范围for循环)
6. [C++11/14/17其他新特性](#6-c111417其他新特性)

---

## 1. 命名空间（namespace）

### 1.1 什么是命名空间？

**通俗理解**：命名空间就像给代码里的名字贴上"姓名牌"，避免大家重名打架。

> 想象一个大型会议：
> - 有两个叫"张三"的人
> - 一个来自北京，一个来自上海
> - 我们不能说"张三"，要说"北京的张三"或"上海的张三"
> - **命名空间**就是这里的"北京""上海"标签

### 1.2 为什么需要命名空间？

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

### 1.3 命名空间的三种使用方式

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

### 1.4 标准命名空间 std

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

### 1.5 无名命名空间

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

### 1.6 命名空间取别名

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

### 1.7 常见错误

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

### 1.8 练习题

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

## 2. 智能指针

### 2.1 什么是智能指针？

**通俗理解**：智能指针就是"会自己打扫房间的机器人"。

> 普通指针 = 你借了内存，用完得自己还
> 智能指针 = 借了内存，机器人自动还，不用你操心

### 2.2 为什么需要智能指针？

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

### 2.3 三种智能指针详解

#### 2.3.1 unique_ptr - 独占所有权

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

#### 2.3.2 shared_ptr - 共享所有权

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

#### 2.3.3 weak_ptr - 弱引用

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

### 2.4 循环引用问题（weak_ptr的用武之地）

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

### 2.5 智能指针选择指南

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

### 2.6 常见错误

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

### 2.7 练习题

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

## 3. lambda表达式

### 3.1 什么是lambda？

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

### 3.2 lambda的基本语法

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

### 3.3 捕获列表详解

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

### 3.4 各种捕获方式示例

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

### 3.5 lambda的实际应用

#### 3.5.1 与STL算法配合

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

#### 3.5.2 作为回调函数

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

### 3.6 lambda与函数对象的对比

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

### 3.7 常见错误

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

### 3.8 练习题

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

## 4. auto关键字

### 4.1 auto是什么？

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

### 4.2 auto的使用场景

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

### 4.3 auto与指针、引用

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

### 4.4 auto的注意事项

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

### 4.5 auto与decltype对比

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

### 4.6 常见错误

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

### 4.7 练习题

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

## 5. 范围for循环

### 5.1 基本语法

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

### 5.2 值拷贝 vs 引用

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

### 5.3 与auto配合

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

### 5.4 常见错误

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

### 5.5 练习题

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

## 6. C++11/14/17其他新特性

### 6.1 nullptr - 空指针

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

### 6.2 constexpr - 编译时常量

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

### 6.3 初始化列表

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

### 6.4 类型别名

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

### 6.5 结构化绑定（C++17）

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

### 6.6 if/switch初始化（C++17）

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

### 6.7 std::optional（C++17）

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

### 6.8 std::variant（C++17）

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

### 6.9 常见错误

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

### 6.10 练习题

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

## 📚 本章小结

| 知识点 | 核心要点 |
|-------|---------|
| 命名空间 | 避免名字冲突，`std`是标准库命名空间 |
| 智能指针 | `unique_ptr`独占，`shared_ptr`共享，`weak_ptr`观察 |
| lambda | `[捕获](参数)->返回{函数体}`，与STL算法配合 |
| auto | 让编译器推断类型，简化长类型名 |
| 范围for | `for(元素:容器)`遍历，注意引用vs拷贝 |
| nullptr | 类型安全的空指针 |
| constexpr | 编译时常量 |
| 结构化绑定 | `auto [a, b, c] = ...;` |

---

## 📝 练习答案

### 命名空间
```cpp
// 练习1
namespace Math {
    int add(int a, int b) { return a + b; }
}
namespace AdvancedMath {
    int add(int a, int b) { return a + b + 100; }
}
int main() {
    cout << Math::add(1, 2) << endl;        // 3
    cout << AdvancedMath::add(1, 2) << endl; // 103
}
```

### 智能指针
```cpp
// 练习1
void process() {
    unique_ptr<int[]> data = make_unique<int[]>(100);
    // ... 使用data
    // 自动释放，不需要delete
}

// 练习2
// 输出：2，然后输出 true(expired())
```

### lambda
```cpp
// 练习2
auto factorial = [](int n) {
    int result = 1;
    for (int i = 2; i <= n; i++) result *= i;
    return result;
};
```

### auto
```cpp
// 练习1
// a = double, b = int, c = char, d = wchar_t, e = const char*
```

### 范围for
```cpp
// 练习4
// 循环结束后nums仍然是 {1, 2, 3}，因为n是拷贝
```

---

*返回 [主目录](../README.md)*
