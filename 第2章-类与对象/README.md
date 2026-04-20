# 第2章 类与对象

> ⭐ **重点章节** - 面向对象编程的核心基础

---

## 📋 目录

1. [结构体（struct）](#1-结构体struct)
2. [共用体（union）](#2-共用体union)
3. [类的定义与对象](#3-类的定义与对象)
4. [构造函数](#4-构造函数)
5. [析构函数](#5-析构函数)
6. [复制构造函数](#6-复制构造函数)
7. [this指针](#7-this指针)
8. [静态成员](#8-静态成员)
9. [友元](#9-友元)
10. [运算符重载](#10-运算符重载)
11. [自由存储区对象的构造与析构](#11-自由存储区对象的构造与析构)
12. [浅复制与深复制](#12-浅复制与深复制)

---

## 1. 结构体（struct）

### 1.1 为什么需要结构体？

想象一下，我们要存储一个学生的信息：
- 学号（整数）
- 姓名（字符串）
- 性别（字符）
- 年龄（整数）
- 成绩（浮点数）
- 住址（字符串）

如果用普通变量，我们需要定义6个独立的变量，管理起来非常麻烦。**结构体**就是来解决这个问题的——它可以把不同类型的数据组合成一个整体！

### 1.2 结构体的定义

```cpp
#include <iostream>
#include <string>
using namespace std;

// 定义结构体类型
struct Student {
    int num;           // 学号
    string name;      // 姓名
    char sex;         // 性别
    int age;          // 年龄
    float score;      // 成绩
    string addr;      // 住址
};  // 别忘了分号！

int main() {
    // 定义结构体变量
    Student stu1;
    
    // 给成员赋值（使用点操作符）
    stu1.num = 1001;
    stu1.name = "张三";
    stu1.sex = 'M';
    stu1.age = 18;
    stu1.score = 89.5;
    stu1.addr = "北京东路1号";
    
    // 输出信息
    cout << "学号: " << stu1.num << endl;
    cout << "姓名: " << stu1.name << endl;
    cout << "性别: " << stu1.sex << endl;
    cout << "年龄: " << stu1.age << endl;
    cout << "成绩: " << stu1.score << endl;
    cout << "住址: " << stu1.addr << endl;
    
    return 0;
}
```

### 1.3 定义结构体的三种方式

```cpp
// 方式1：先定义类型，再定义变量（最常用）
struct Student {
    int num;
    string name;
};
Student stu1, stu2;

// 方式2：定义类型的同时定义变量
struct Student {
    int num;
    string name;
} stu1, stu2;

// 方式3：直接定义变量（不推荐，因为没有类型名）
struct {
    int num;
    string name;
} stu1, stu2;
```

### 1.4 结构体的初始化

```cpp
// 方式1：定义时逐个初始化
Student stu1 = {1001, "张三", 'M', 18, 89.5, "北京"};

// 方式2：定义后逐个赋值
Student stu2;
stu2.num = 1002;
stu2.name = "李四";
```

### 1.5 结构体成员的引用

```cpp
Student stu1 = {1001, "张三", 'M', 18, 89.5};

// ✅ 正确：引用成员
cout << stu1.name << endl;
stu1.age = 19;  // 修改成员

// ❌ 错误：不能直接对结构体整体赋值
// stu1 = "张三";  // 错误！
// cout << stu1;   // 错误！

// ✅ 可以整体赋值（同类结构体之间）
Student stu2 = stu1;  // 把stu1的所有成员复制给stu2
```

### 1.6 结构体数组

```cpp
#include <iostream>
using namespace std;

struct Student {
    int num;
    string name;
    float score;
};

int main() {
    // 定义结构体数组
    Student class1[3] = {
        {1001, "张三", 85},
        {1002, "李四", 92},
        {1003, "王五", 78}
    };
    
    // 访问数组元素和成员
    for (int i = 0; i < 3; i++) {
        cout << class1[i].num << "\t" 
             << class1[i].name << "\t" 
             << class1[i].score << endl;
    }
    
    return 0;
}
```

### 1.7 结构体与函数

```cpp
#include <iostream>
using namespace std;

struct Point {
    int x;
    int y;
};

// 方式1：值传递（不推荐，效率低）
void printPoint1(Point p) {
    cout << "(" << p.x << ", " << p.y << ")" << endl;
}

// 方式2：指针传递（推荐）
void printPoint2(Point *p) {
    cout << "(" << p->x << ", " << p->y << ")" << endl;
}

// 方式3：引用传递（最推荐，简洁又高效）
void printPoint3(Point &p) {
    cout << "(" << p.x << ", " << p.y << ")" << endl;
}

int main() {
    Point p = {10, 20};
    
    printPoint1(p);   // 值传递
    printPoint2(&p);  // 指针传递
    printPoint3(p);   // 引用传递
    
    return 0;
}
```

### 1.8 💡 通俗理解

> **类比**：结构体就像一个"信息卡片"
> - 结构体类型 = 卡片的模板/格式
> - 结构体变量 = 具体的卡片
> - 成员 = 卡片上的各个项目

```
┌─────────────────────────────────────┐
│         Student（结构体类型）         │
├─────────────────────────────────────┤
│  num    │ int      │ 学号            │
│  name   │ string   │ 姓名            │
│  sex    │ char     │ 性别            │
│  age    │ int      │ 年龄            │
│  score  │ float    │ 成绩            │
└─────────────────────────────────────┘
```

---

## 2. 共用体（union）

### 2.1 什么是共用体？

**共用体**是一种特殊的数据类型，它的所有成员**共享同一块内存空间**。这意味着：
- 所有成员占用相同的内存
- 内存大小等于最大成员的大小
- 某一时刻只能有一个成员有效

### 2.2 共用体的定义

```cpp
#include <iostream>
using namespace std;

// 定义共用体
union Data {
    int i;      // 4字节
    float f;    // 4字节
    char ch;    // 1字节
};

int main() {
    Data data;
    
    // 共用同一块内存，赋值不同成员会相互覆盖
    data.i = 65;
    cout << "data.i = " << data.i << endl;    // 输出: 65
    cout << "data.ch = " << data.ch << endl;  // 输出: A (ASCII 65)
    
    data.f = 3.14;
    cout << "data.f = " << data.f << endl;    // 输出: 3.14
    
    return 0;
}
```

### 2.3 共用体 vs 结构体

```
┌─────────────────────────────────────────────────────────────────┐
│                    共用体 vs 结构体                               │
├─────────────────────────────────────────────────────────────────┤
│  结构体（struct）                                                │
│  ├── 所有成员各自独立，各占各的内存                               │
│  ├── 总大小 = 所有成员大小之和                                   │
│  └── 任何时刻所有成员都有效                                       │
│                                                                  │
│  共用体（union）                                                 │
│  ├── 所有成员共享同一块内存                                       │
│  ├── 总大小 = 最大成员的大小                                      │
│  └── 任何时刻只有一个成员有效                                     │
└─────────────────────────────────────────────────────────────────┘
```

### 2.4 共用体的应用场景

```cpp
#include <iostream>
using namespace std;

// 共用体常用于节省内存
struct Student {
    string name;           // 姓名
    
    // 考试成绩，根据类型选择存储方式
    union {
        char grade;        // 字符等级：'A', 'B', 'C', 'D'
        float score;       // 分数：0-100
    } exam;
    
    bool useGrade;  // 标记使用哪种方式
};

int main() {
    Student stu1, stu2;
    
    stu1.name = "张三";
    stu1.useGrade = true;
    stu1.exam.grade = 'A';
    
    stu2.name = "李四";
    stu2.useGrade = false;
    stu2.exam.score = 92.5;
    
    cout << stu1.name << ": " << stu1.exam.grade << endl;
    cout << stu2.name << ": " << stu2.exam.score << endl;
    
    return 0;
}
```

### 2.5 ⚠️ 注意事项

```cpp
// ❌ 不能直接对共用体整体赋值或比较
// data = 10;       // 错误
// if (data == 10)  // 错误

// ✅ 只能逐个引用成员
data.i = 10;

// ✅ 同类型共用体可以整体赋值
Data data1, data2;
data1.i = 10;
data2 = data1;  // 正确：同类型可以赋值
```

---

## 3. 类的定义与对象

### 3.1 从结构体到类

结构体只关注**数据**，而类不仅关注数据，还关注**对数据的操作**！

```cpp
// 结构体：只包含数据
struct Point {
    int x;
    int y;
};

// 类：既包含数据，又包含操作
class Point {
private:          // 私有成员：外部不能直接访问
    int x;
    int y;

public:           // 公有成员：外部可以访问
    // 设置坐标
    void set(int a, int b) {
        x = a;
        y = b;
    }
    
    // 获取坐标
    void get(int &a, int &b) {
        a = x;
        b = y;
    }
    
    // 打印坐标
    void print() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};
```

### 3.2 类的基本结构

```cpp
class 类名 {
private:
    // 私有成员：类内部可以访问，外部不能直接访问
    数据成员1;
    数据成员2;
    
public:
    // 公有成员：类内部和外部都可以访问
    成员函数1();
    成员函数2();
    
protected:
    // 保护成员：类内部和派生类可以访问
};
```

### 3.3 访问权限详解

```
┌─────────────────────────────────────────────────────────────────┐
│                        访问权限                                   │
├─────────────────────────────────────────────────────────────────┤
│  private（私有）                                                 │
│  ├── 类内成员函数：✅ 可以访问                                    │
│  ├── 类的对象：❌ 不能直接访问                                    │
│  ├── 友元函数：✅ 可以访问                                        │
│  └── 派生类：❌ 不能访问                                          │
│                                                                  │
│  public（公有）                                                  │
│  ├── 类内成员函数：✅ 可以访问                                    │
│  ├── 类的对象：✅ 可以访问                                        │
│  ├── 友元函数：✅ 可以访问                                        │
│  └── 派生类：✅ 可以访问                                          │
│                                                                  │
│  protected（保护）                                               │
│  ├── 类内成员函数：✅ 可以访问                                    │
│  ├── 类的对象：❌ 不能直接访问                                    │
│  ├── 友元函数：✅ 可以访问                                        │
│  └── 派生类：✅ 可以访问                                          │
└─────────────────────────────────────────────────────────────────┘
```

### 3.4 类的定义示例

```cpp
#include <iostream>
using namespace std;

class Clock {
private:
    int hour;      // 小时
    int minute;    // 分钟
    int second;    // 秒

public:
    // 设置时间
    void setTime(int h, int m, int s) {
        hour = h;
        minute = m;
        second = s;
    }
    
    // 显示时间
    void showTime() {
        cout << hour << ":" << minute << ":" << second << endl;
    }
    
    // 闹钟响铃
    void alarm() {
        cout << "叮铃铃~" << endl;
    }
};

int main() {
    Clock myClock;  // 创建对象
    
    myClock.setTime(8, 30, 0);  // 设置时间
    myClock.showTime();         // 显示时间
    myClock.alarm();           // 响铃
    
    // ❌ 错误：不能在类外访问私有成员
    // myClock.hour = 9;  // 编译错误！
    
    return 0;
}
```

### 3.5 💡 类的通俗理解

> **类 vs 对象的关系，就像 "图纸" vs "房子"**

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                  │
│    class（类）                    object（对象）                 │
│    ┌─────────────────┐           ┌─────────────────┐           │
│    │  属性（数据）   │  建造      │                 │           │
│    │  - 颜色         │ ──────▶   │  红色房子        │           │
│    │  - 大小         │            │  100平方米       │           │
│    │  - 位置         │            │  北京            │           │
│    │                 │            │                 │           │
│    │  方法（函数）   │            │                 │           │
│    │  - 开门()       │            │                 │           │
│    │  - 关门()       │            │                 │           │
│    │  - 开窗()       │            │                 │           │
│    └─────────────────┘            └─────────────────┘           │
│                                                                  │
│    抽象的模板                           具体的存在                 │
│    定义的规则                           占用内存                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. 构造函数

### 4.1 为什么需要构造函数？

之前我们都是手动给成员赋值，这样容易出错。**构造函数**是类的一个特殊成员函数，在创建对象时**自动调用**，用于初始化对象。

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    // 构造函数：与类名相同，无返回值
    Point(int a, int b) {
        x = a;
        y = b;
        cout << "构造函数被调用了！" << endl;
    }
    
    void print() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1(10, 20);  // 创建对象时自动调用构造函数
    p1.print();
    
    // Point p2;  // ❌ 错误：因为定义了带参数的构造函数
    
    return 0;
}

/*
输出：
构造函数被调用了！
(10, 20)
*/
```

### 4.2 构造函数的特征

1. **与类名相同**
2. **没有返回值**（也不写void）
3. **在创建对象时自动调用**
4. **可以重载**（定义多个构造函数）
5. **可以有默认参数**

### 4.3 构造函数的多种形式

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    // 形式1：无参构造函数
    Point() {
        x = 0;
        y = 0;
        cout << "调用无参构造函数" << endl;
    }
    
    // 形式2：带参构造函数
    Point(int a, int b) {
        x = a;
        y = b;
        cout << "调用带参构造函数" << endl;
    }
    
    // 形式3：带默认参数的构造函数
    // Point(int a = 0, int b = 0) { x = a; y = b; }
    
    void print() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1;          // 调用无参构造函数
    Point p2(10, 20);  // 调用带参构造函数
    
    p1.print();  // (0, 0)
    p2.print();  // (10, 20)
    
    return 0;
}
```

### 4.4 构造函数重载

```cpp
#include <iostream>
using namespace std;

class Rectangle {
private:
    int width, height;

public:
    // 构造函数1：无参
    Rectangle() {
        width = 0;
        height = 0;
        cout << "无参构造函数" << endl;
    }
    
    // 构造函数2：一个参数
    Rectangle(int w) {
        width = w;
        height = w;  // 正方形
        cout << "正方形构造函数" << endl;
    }
    
    // 构造函数3：两个参数
    Rectangle(int w, int h) {
        width = w;
        height = h;
        cout << "矩形构造函数" << endl;
    }
    
    int area() {
        return width * height;
    }
};

int main() {
    Rectangle r1;        // 调用无参构造函数
    Rectangle r2(5);    // 调用一个参数的构造函数
    Rectangle r3(3, 4); // 调用两个参数的构造函数
    
    cout << "r1面积: " << r1.area() << endl;  // 0
    cout << "r2面积: " << r2.area() << endl;  // 25
    cout << "r3面积: " << r3.area() << endl;  // 12
    
    return 0;
}
```

### 4.5 ⚠️ 注意事项

```cpp
// ❌ 错误示例
class Test {
public:
    void Test() { }  // ❌ 错误：构造函数不能有返回值，也不能写void
};

// ⚠️ 默认构造函数
class A {
public:
    A() { }  // 如果你没有定义任何构造函数，编译器会自动生成一个
};

// ⚠️ 构造函数的参数缺省值
class B {
private:
    int x, y;
public:
    B(int a = 0, int b = 0) { x = a; y = b; }  // 可以全部或部分缺省
};
```

### 4.6 构造函数的初始化列表

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    const int x, y;  // 常量成员
    int &ref;        // 引用成员

public:
    // 初始化列表：用于初始化常量、引用和没有默认构造的成员
    Point(int a, int b, int &r) : x(a), y(b), ref(r) {
        // 注意：这里是对x、y、ref的赋值，不是初始化
        // 但实际上，常量和引用只能通过初始化列表初始化
    }
    
    void print() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    int r = 100;
    Point p(10, 20, r);
    p.print();
    
    return 0;
}
```

### 4.7 💡 构造函数调用时机

```cpp
class Test {
public:
    Test() { cout << "构造函数" << endl; }
};

int main() {
    // 1. 创建对象时
    Test obj1;           // 调用无参构造函数
    
    // 2. 创建对象数组时
    Test arr[3];         // 调用3次无参构造函数
    
    // 3. 用new创建对象时
    Test *p = new Test();  // 调用构造函数
    
    // 4. 对象作为函数参数（值传递）
    void func(Test t);   // 会调用复制构造函数（后面讲）
    
    return 0;
}
```

---

## 5. 析构函数

### 5.1 什么是析构函数？

**析构函数**是类的另一个特殊成员函数，在对象**销毁时自动调用**，用于释放资源、清理工作。

```cpp
#include <iostream>
using namespace std;

class Student {
private:
    char *name;

public:
    // 构造函数：分配内存
    Student(const char *n) {
        name = new char[strlen(n) + 1];
        strcpy(name, n);
        cout << name << " 的构造函数被调用" << endl;
    }
    
    // 析构函数：释放内存
    ~Student() {
        cout << name << " 的析构函数被调用" << endl;
        delete[] name;
    }
    
    void display() {
        cout << "学生: " << name << endl;
    }
};

int main() {
    Student s1("张三");
    s1.display();
    
    cout << "--- 函数结束 ---" << endl;
    // 当s1离开作用域时，自动调用析构函数
    
    return 0;
}

/*
输出：
张三 的构造函数被调用
学生: 张三
--- 函数结束 ---
张三 的析构函数被调用
*/
```

### 5.2 析构函数的特征

1. **与类名相同，但前面加 `~`**
2. **没有参数**
3. **没有返回值**
4. **不能重载**（一个类只能有一个析构函数）
5. **在对象销毁时自动调用**

### 5.3 构造函数与析构函数的调用顺序

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    Base() { cout << "Base 构造函数" << endl; }
    ~Base() { cout << "Base 析构函数" << endl; }
};

class Derived : public Base {
public:
    Derived() { cout << "Derived 构造函数" << endl; }
    ~Derived() { cout << "Derived 析构函数" << endl; }
};

int main() {
    cout << "--- 创建对象 ---" << endl;
    Derived obj;
    cout << "--- 对象销毁 ---" << endl;
    
    return 0;
}

/*
输出：
--- 创建对象 ---
Base 构造函数        // 先调用基类构造函数
Derived 构造函数     // 再调用派生类构造函数
--- 对象销毁 ---
Derived 析构函数     // 先调用派生类析构函数
Base 析构函数        // 再调用基类析构函数
*/
```

### 5.4 💡 什么情况需要析构函数？

| 情况 | 是否需要析构函数 |
|:---|:---:|
| 类中没有任何指针成员 | ❌ 不需要 |
| 类中有new分配的内存 | ✅ 需要 |
| 类中打开了文件 | ✅ 需要 |
| 类中申请了系统资源 | ✅ 需要 |

### 5.5 ⚠️ 常见错误

```cpp
// ❌ 错误1：析构函数不能有参数
class Test {
    ~Test(int x);  // ❌ 错误
};

// ❌ 错误2：忘记释放内存
class BadClass {
    char *p;
public:
    BadClass() { p = new char[100]; }
    // ❌ 没有析构函数，内存泄漏！
};

// ✅ 正确做法
class GoodClass {
    char *p;
public:
    GoodClass() { p = new char[100]; }
    ~GoodClass() { delete[] p; }  // ✅ 释放内存
};
```

---

## 6. 复制构造函数

### 6.1 什么是复制构造函数？

**复制构造函数**是一种特殊的构造函数，用一个已存在的对象来初始化另一个同类对象。

```cpp
class Point {
private:
    int x, y;

public:
    // 普通构造函数
    Point(int a = 0, int b = 0) {
        x = a;
        y = b;
        cout << "普通构造函数被调用" << endl;
    }
    
    // 复制构造函数（参数必须是同类的引用）
    Point(const Point &p) {
        x = p.x;
        y = p.y;
        cout << "复制构造函数被调用" << endl;
    }
};
```

### 6.2 复制构造函数的调用时机

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    Point(int a = 0, int b = 0) : x(a), y(b) {
        cout << "普通构造函数: (" << x << ", " << y << ")" << endl;
    }
    
    Point(const Point &p) : x(p.x), y(p.y) {
        cout << "复制构造函数: (" << x << ", " << y << ")" << endl;
    }
};

void func1(Point p) {  // 形参是对象（值传递）
    cout << "func1 中" << endl;
}

Point func2() {
    Point p(100, 200);
    return p;  // 返回对象
}

int main() {
    cout << "--- 1. 创建对象 ---" << endl;
    Point p1(10, 20);
    
    cout << "\n--- 2. 用对象初始化另一个对象 ---" << endl;
    Point p2(p1);  // 调用复制构造函数
    
    cout << "\n--- 3. 函数形参（值传递） ---" << endl;
    func1(p1);  // 调用复制构造函数
    
    cout << "\n--- 4. 函数返回值 ---" << endl;
    Point p3 = func2();  // 可能调用复制构造函数
    
    return 0;
}
```

### 6.3 ⚠️ 为什么参数必须是引用？

```cpp
// ❌ 错误：参数是值传递，会导致无限递归
Point(const Point p) {  // ❌ 会先调用复制构造函数创建p！
    // ...
}

// ✅ 正确：参数是引用，避免递归
Point(const Point &p) {  // ✅ 不会递归
    // ...
}
```

### 6.4 默认复制构造函数

如果你没有定义复制构造函数，编译器会自动生成一个**浅拷贝**的版本：

```cpp
class Test {
public:
    int *p;
    Test() { p = new int(10); }  // 构造函数
};

// 使用默认的复制构造函数
Test t1;
Test t2(t1);  // 浅拷贝：t2.p 和 t1.p 指向同一块内存！
```

### 6.5 ⚠️ 浅拷贝的问题

```cpp
#include <iostream>
using namespace std;

class shallowCopy {
public:
    int *p;
    
    shallowCopy(int val) {
        p = new int(val);
        cout << "构造函数: p = " << *p << endl;
    }
    
    // 没有定义析构函数，使用默认的
    
    void setValue(int val) {
        *p = val;
    }
};

int main() {
    shallowCopy obj1(100);
    shallowCopy obj2(obj1);  // 默认复制构造函数（浅拷贝）
    
    cout << "obj1.p = " << *obj1.p << endl;
    cout << "obj2.p = " << *obj2.p << endl;
    
    obj2.setValue(200);  // 修改obj2
    
    // ⚠️ 问题：obj1和obj2的p指向同一块内存！
    cout << "修改后:" << endl;
    cout << "obj1.p = " << *obj1.p << endl;  // 变成200了！
    cout << "obj2.p = " << *obj2.p << endl;  // 200
    
    // ❌ 双重释放！
    return 0;
}
```

### 6.6 解决方案：深拷贝

```cpp
#include <iostream>
using namespace std;

class deepCopy {
public:
    int *p;
    
    deepCopy(int val) {
        p = new int(val);
        cout << "构造函数: p = " << *p << endl;
    }
    
    // 复制构造函数（深拷贝）
    deepCopy(const deepCopy &obj) {
        p = new int(*obj.p);  // 分配新内存，复制数据
        cout << "复制构造函数(深拷贝): p = " << *p << endl;
    }
    
    // 析构函数
    ~deepCopy() {
        cout << "析构函数: 释放 " << *p << endl;
        delete p;
    }
    
    void setValue(int val) {
        *p = val;
    }
};

int main() {
    deepCopy obj1(100);
    deepCopy obj2(obj1);  // 深拷贝
    
    cout << "obj1.p = " << *obj1.p << endl;
    cout << "obj2.p = " << *obj2.p << endl;
    
    obj2.setValue(200);  // 修改obj2
    
    cout << "修改后:" << endl;
    cout << "obj1.p = " << *obj1.p << endl;  // 仍然是100！
    cout << "obj2.p = " << *obj2.p << endl;  // 200
    
    return 0;
}
```

---

## 7. this指针

### 7.1 什么是this指针？

**this指针**是一个隐含的指针，指向当前对象。通过它可以访问对象的成员。

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    // 注意：参数名和成员名相同，需要用this区分
    void set(int x, int y) {
        this->x = x;   // this->x 表示成员x
        this->y = y;   // this->y 表示成员y
    }
    
    // 返回当前对象的引用（用于链式调用）
    Point& add(Point &p) {
        this->x += p.x;
        this->y += p.y;
        return *this;  // 返回当前对象
    }
    
    void print() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1(10, 20);
    Point p2(5, 10);
    
    // 链式调用
    p1.add(p2).add(p2);  // 10+5+5=20, 20+10+10=40
    p1.print();  // (20, 40)
    
    return 0;
}
```

### 7.2 this指针的特点

| 特点 | 说明 |
|:---|:---|
| 隐含存在 | 每个成员函数都有一个this指针 |
| 指向当前对象 | this指向调用该函数的对象 |
| 是常量指针 | `ClassName *const this` |
| 可以解引用 | `*this` 表示当前对象本身 |

### 7.3 this指针的使用场景

```cpp
class String {
private:
    char *str;
    int len;

public:
    // 1. 参数名与成员名相同时区分
    void setStr(char *str) {
        this->str = str;  // this区分
    }
    
    // 2. 返回当前对象
    String& append(String &s) {
        // 拼接字符串...
        return *this;  // 支持链式调用
    }
    
    // 3. 在const成员函数中使用
    void print() const {
        // this是指向常量的指针
        // this->str = "xxx";  // ❌ 错误：不能修改
    }
};
```

### 7.4 💡 通俗理解

```cpp
class Dog {
public:
    string name;
    
    void bark() {
        // 假设有两只狗：小黄和小黑
        // 小黄调用bark()时，this指向小黄
        // 小黑调用bark()时，this指向小黑
        cout << this->name << " 汪汪叫！" << endl;
    }
};

int main() {
    Dog xiaohei;
    xiaohei.name = "小黑";
    
    Dog xiaohuang;
    xiaohuang.name = "小黄";
    
    xiaohei.bark();  // this指向xiaohuang，打印"小黑 汪汪叫！"
    xiaohuang.bark(); // this指向xiaohuang，打印"小黄 汪汪叫！"
    
    return 0;
}
```

---

## 8. 静态成员

### 8.1 什么是静态成员？

**静态成员**是被所有对象**共享**的成员，无论创建多少个对象，静态成员只有一份。

```
┌─────────────────────────────────────────────────────────────────┐
│                      静态成员 vs 普通成员                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  普通成员（每个对象都有一份）                                      │
│  ┌────────┐  ┌────────┐  ┌────────┐                              │
│  │ 对象1  │  │ 对象2  │  │ 对象3  │                              │
│  │ x = 1  │  │ x = 2  │  │ x = 3  │   ← 各自的x                  │
│  └────────┘  └────────┘  └────────┘                              │
│                                                                  │
│  静态成员（所有对象共享一份）                                      │
│  ┌────────────────────────────────────┐                         │
│  │          static count = 0          │   ← 共享的count          │
│  ├────────┬────────┬────────┤                                   │
│  │ 对象1  │ 对象2  │ 对象3  │                                   │
│  └────────┴────────┴────────┘                                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 8.2 静态数据成员

```cpp
#include <iostream>
using namespace std;

class Student {
private:
    string name;
    int id;
    
public:
    static int totalCount;  // 静态数据成员：所有学生共享

public:
    Student(string n) : name(n) {
        id = ++totalCount;  // 使用静态成员
        cout << "创建学生: " << name << ", 学号: " << id << endl;
    }
    
    ~Student() {
        cout << "学生离开: " << name << endl;
    }
    
    // 静态成员函数：用于访问静态成员
    static int getTotalCount() {
        return totalCount;
    }
};

// ⚠️ 必须在类外初始化静态成员
int Student::totalCount = 0;

int main() {
    cout << "当前学生总数: " << Student::getTotalCount() << endl;
    
    Student s1("张三");
    Student s2("李四");
    Student s3("王五");
    
    cout << "当前学生总数: " << Student::getTotalCount() << endl;
    
    return 0;
}

/*
输出：
当前学生总数: 0
创建学生: 张三, 学号: 1
创建学生: 李四, 学号: 2
创建学生: 王五, 学号: 3
当前学生总数: 3
学生离开: 王五
学生离开: 李四
学生离开: 张三
*/
```

### 8.3 静态成员函数

```cpp
#include <iostream>
using namespace std;

class Counter {
private:
    int value;
    static int totalCount;  // 静态成员

public:
    Counter(int v = 0) : value(v) {
        totalCount++;
    }
    
    ~Counter() {
        totalCount--;
    }
    
    // 静态成员函数
    static int getTotal() {
        return totalCount;
    }
    
    // ⚠️ 静态成员函数不能访问非静态成员
    // static void show() {
    //     cout << value << endl;  // ❌ 错误！不能访问非静态成员
    // }
    
    void show() {
        cout << "value = " << value << endl;  // ✅ 非静态函数可以访问静态成员
    }
};

int Counter::totalCount = 0;

int main() {
    Counter c1(10);
    Counter c2(20);
    Counter c3(30);
    
    // 通过类名调用静态成员函数
    cout << "对象总数: " << Counter::getTotal() << endl;  // 3
    
    // 通过对象调用（不推荐）
    cout << "对象总数: " << c1.getTotal() << endl;  // 3
    
    return 0;
}
```

### 8.4 💡 静态成员的特点

| 特点 | 说明 |
|:---|:---|
| 所有对象共享 | 不管有多少对象，静态成员只有一份 |
| 在类外初始化 | 必须单独定义和初始化 |
| 类名访问 | 可以通过 `类名::静态成员` 访问 |
| 属于类 | 不属于某个对象 |

### 8.5 静态成员的应用场景

```cpp
// 场景1：统计对象个数
class Button {
private:
    static int buttonCount;
public:
    Button() { buttonCount++; }
    ~Button() { buttonCount--; }
    static int getCount() { return buttonCount; }
};

// 场景2：类常量
class Math {
public:
    static const double PI;  // 类常量
    static const int MAX_SIZE = 100;  // 编译时常量
};

// 场景3：单例模式
class Singleton {
private:
    static Singleton *instance;  // 静态指针
    Singleton() { }  // 私有构造函数
public:
    static Singleton* getInstance() {
        if (instance == nullptr) {
            instance = new Singleton();
        }
        return instance;
    }
};
```

---

## 9. 友元

### 9.1 什么是友元？

**友元**是一种机制，允许某些函数或类访问另一个类的**私有成员**。

> ⚠️ **警告**：友元会破坏类的封装性，使用时需谨慎！

### 9.2 友元函数

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    Point(int a = 0, int b = 0) : x(a), y(b) { }
    
    // 声明友元函数
    friend void printPoint(const Point &p);
    
    // 友元函数可以访问私有成员
};

void printPoint(const Point &p) {
    // 虽然不是成员函数，但可以访问私有成员
    cout << "(" << p.x << ", " << p.y << ")" << endl;
}

int main() {
    Point p(10, 20);
    printPoint(p);  // 直接访问私有成员
    return 0;
}
```

### 9.3 友元类

```cpp
#include <iostream>
using namespace std;

class Printer {
public:
    void printAll(const Document &doc);  // 声明
};

class Document {
private:
    string content;  // 私有内容
    
public:
    Document(const string &c) : content(c) { }
    
    // Printer是Document的友元类
    friend class Printer;
};

// Printer可以访问Document的私有成员
void Printer::printAll(const Document &doc) {
    cout << "文档内容: " << doc.content << endl;  // 可以访问私有成员
}

int main() {
    Document doc("Hello World!");
    Printer printer;
    printer.printAll(doc);
    
    return 0;
}
```

### 9.4 友元的注意事项

```cpp
// ⚠️ 友元是单向的
class A {
    friend class B;  // B可以访问A，但A不能访问B
};

// ⚠️ 友元不具有传递性
class A {
    friend class B;
};
class B {
    friend class C;
};
// B是A的友元，C是B的友元，但C不能访问A！

// ⚠️ 友元破坏封装
// 使用友元前请三思：能否通过公有函数实现？
```

### 9.5 💡 什么时候用友元？

```cpp
// ✅ 适合使用友元的场景
class String {
    friend String operator+(const String &s1, const String &s2);
    // 需要访问内部实现，但又希望用运算符语法
};

// ✅ 适合使用友元的场景
class Matrix {
    friend Matrix multiply(const Matrix &a, const Matrix &b);
    // 某些操作需要直接访问内部数据
};

// ❌ 不适合使用友元的场景
class User {
    friend void printUser(const User &u);  // 直接提供public方法更好
public:
    void print() const;
};
```

---

## 10. 运算符重载

### 10.1 什么是运算符重载？

**运算符重载**让我们可以自定义运算符的行为，就像给运算符"赋能"一样。

```cpp
#include <iostream>
using namespace std;

class Complex {
private:
    double real, imag;

public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) { }
    
    // 重载 + 运算符（作为成员函数）
    Complex operator+(const Complex &c) {
        return Complex(real + c.real, imag + c.imag);
    }
    
    void display() {
        cout << real;
        if (imag >= 0) cout << " + " << imag << "i";
        else cout << " - " << -imag << "i";
        cout << endl;
    }
};

int main() {
    Complex c1(3, 4);
    Complex c2(1, 2);
    
    Complex c3 = c1 + c2;  // 就像普通加法一样用！
    c3.display();  // 4 + 6i
    
    return 0;
}
```

### 10.2 可重载的运算符

```
┌─────────────────────────────────────────────────────────────────┐
│                     可重载的运算符                                 │
├─────────────────────────────────────────────────────────────────┤
│  算术运算符:    +  -  *  /  %                                    │
│  位运算符:      &  |  ^  ~  <<  >>                               │
│  赋值运算符:    =  +=  -=  *=  /=  %=  &=  |=  ^=  <<=  >>=     │
│  比较运算符:    ==  !=  <  >  <=  >=                             │
│  逻辑运算符:    &&  ||  !                                        │
│  自增自减:      ++  --                                            │
│  其他运算符:    []  ()  ->  new  delete  new[]  delete[]         │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                     不可重载的运算符                              │
├─────────────────────────────────────────────────────────────────┤
│  作用域运算符:  ::                                                │
│  成员访问符:    .                                                │
│  条件运算符:    ?:                                               │
│  指针运算符:    sizeof                                            │
└─────────────────────────────────────────────────────────────────┘
```

### 10.3 重载方式：成员函数 vs 友元函数

```cpp
#include <iostream>
using namespace std;

class Vector {
private:
    int x, y;

public:
    Vector(int a = 0, int b = 0) : x(a), y(b) { }
    
    // 方式1：作为成员函数重载
    Vector operator+(const Vector &v) {
        return Vector(x + v.x, y + v.y);
    }
    
    // 方式2：友元函数重载（参数是左右两个操作数）
    friend Vector operator-(const Vector &v1, const Vector &v2);
    
    void display() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

// 友元函数定义
Vector operator-(const Vector &v1, const Vector &v2) {
    return Vector(v1.x - v2.x, v1.y - v2.y);
}

int main() {
    Vector v1(10, 20);
    Vector v2(3, 5);
    
    Vector v3 = v1 + v2;   // 成员函数重载
    Vector v4 = v1 - v2;   // 友元函数重载
    
    v3.display();  // (13, 25)
    v4.display();  // (7, 15)
    
    return 0;
}
```

### 10.4 重载输入输出运算符

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    Point(int a = 0, int b = 0) : x(a), y(b) { }
    
    // 声明为友元
    friend ostream& operator<<(ostream &os, const Point &p);
    friend istream& operator>>(istream &is, Point &p);
};

// 重载 << 运算符
ostream& operator<<(ostream &os, const Point &p) {
    os << "(" << p.x << ", " << p.y << ")";
    return os;  // 返回引用以支持链式调用
}

// 重载 >> 运算符
istream& operator>>(istream &is, Point &p) {
    is >> p.x >> p.y;
    return is;  // 返回引用以支持链式调用
}

int main() {
    Point p1(10, 20);
    
    // 使用重载的 <<
    cout << "p1 = " << p1 << endl;
    
    // 使用重载的 >>
    Point p2;
    cout << "输入p2的坐标: ";
    cin >> p2;
    cout << "p2 = " << p2 << endl;
    
    return 0;
}
```

### 10.5 重载赋值运算符

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class String {
private:
    char *str;
    int len;

public:
    String(const char *s = "") {
        len = strlen(s);
        str = new char[len + 1];
        strcpy(str, s);
    }
    
    // 析构函数
    ~String() {
        delete[] str;
    }
    
    // 重载赋值运算符（深拷贝）
    String& operator=(const String &s) {
        if (this != &s) {  // 防止自我赋值
            delete[] str;  // 释放旧内存
            len = s.len;
            str = new char[len + 1];
            strcpy(str, s.str);
        }
        return *this;
    }
    
    void display() {
        cout << str << endl;
    }
};

int main() {
    String s1("Hello");
    String s2("World");
    
    s2 = s1;  // 调用重载的 = 运算符
    s2.display();  // Hello
    
    // 链式赋值
    String s3;
    s3 = s2 = s1;  // s2 = s1 返回s2的引用
    
    return 0;
}
```

### 10.6 💡 运算符重载的规则

```cpp
// 1. 不能改变运算符的优先级和结合性
// a + b * c  始终按 (a + (b * c)) 计算

// 2. 不能改变运算符的操作数个数
// + 始终是二元运算符

// 3. 不能创建新的运算符
// ❌ operator**() 不允许

// 4. 保持运算符的语义
// + 最好用于"加法"或类似操作
// 不要把 + 重载成"减法"！

// 5. 某些运算符必须定义为成员函数
class A {
public:
    A operator=(const A&);      // 赋值
    A operator[](int);          // 下标
    A operator->();             // 指针访问
    A operator()(...);          // 函数调用
};
```

---

## 📝 本章小结

### 核心概念

| 概念 | 说明 |
|:---|:---|
| **结构体** | 组合不同类型数据的构造类型 |
| **共用体** | 所有成员共享同一块内存 |
| **类** | 封装数据和操作的抽象数据类型 |
| **构造函数** | 创建对象时自动调用，用于初始化 |
| **析构函数** | 对象销毁时自动调用，用于清理 |
| **复制构造函数** | 用已有对象初始化新对象 |
| **this指针** | 指向当前对象的隐含指针 |
| **静态成员** | 所有对象共享的成员 |
| **友元** | 可以访问私有成员的外部函数或类 |
| **运算符重载** | 自定义运算符的行为 |

### 重点难点

1. **构造/析构函数的调用时机和顺序**
2. **浅拷贝vs深拷贝**
3. **静态成员的定义和初始化**
4. **友元的使用场景和注意事项**
5. **运算符重载的两种方式**

---

## 🔍 常见问题

**Q: 构造函数和普通成员函数有什么区别？**
A: 构造函数在创建对象时自动调用，没有返回值，可以重载。

**Q: 什么时候需要自定义复制构造函数？**
A: 当类中有指针成员、需要深拷贝时，必须自定义复制构造函数。

**Q: 静态成员函数能访问非静态成员吗？**
A: 不能，因为静态成员函数没有this指针，不知道操作哪个对象。

**Q: 友元会破坏封装吗？**
A: 会，所以建议尽量少用友元，优先使用公有成员函数。

---

## 📚 练习题

1. 定义一个学生结构体，包含学号、姓名、三门课成绩，计算总分和平均分。
2. 定义一个复数类，实现加减乘除运算。
3. 设计一个字符串类，包含构造函数、析构函数、复制构造函数、重载+运算符。
4. 用静态成员实现一个计数器，统计创建了多少个对象。

---

*返回 [主目录](../README.md)*

---

## 11. 自由存储区对象的构造与析构

### 11.1 什么是自由存储区？

**自由存储区（Free Store）**：程序运行时动态分配的内存区域，与栈（Stack）相对应。

```
内存布局：
┌─────────────────┐ 高地址
│      代码区      │  存放程序代码
├─────────────────┤
│      静态区      │  全局变量、静态变量
├─────────────────┤
│      栈区        │  函数参数、局部变量（自动管理）
├─────────────────┤
│    自由存储区     │  new/delete 动态分配（手动管理）
│   (Heap堆区)     │
└─────────────────┘ 低地址
```

**为什么需要动态内存分配？**
- 需要的内存大小在编译时无法确定
- 需要在多个函数间共享数据
- 程序运行期间数据量变化较大

### 11.2 new 和 delete 操作符

```cpp
#include <iostream>
using namespace std;

// 基本数据类型
int *p1 = new int;        // 分配一个int空间
int *p2 = new int(100);   // 分配并初始化
int *p3 = new int[10];    // 分配数组

delete p1;    // 释放单个对象
delete p2;   // 释放单个对象
delete[] p3; // 释放数组（注意[]）

// C++11 nullptr
int *p4 = nullptr;  // 避免野指针
```

### 11.3 类对象的动态分配

```cpp
#include <iostream>
#include <string>
using namespace std;

class CGoods {
private:
    string Name;
    int Amount;
    float Price;
    float TotalValue;
    
public:
    // 无参构造函数
    CGoods() {
        cout << "调用默认构造函数" << endl;
        Name = "";
        Amount = 0;
        Price = 0;
        TotalValue = 0;
    }
    
    // 有参构造函数
    CGoods(string name, int amount, float price) {
        cout << "调用三参数构造函数" << endl;
        Name = name;
        Amount = amount;
        Price = price;
        TotalValue = price * amount;
    }
    
    // 析构函数
    ~CGoods() {
        cout << "调用析构函数: " << Name << endl;
    }
    
    void Show() {
        cout << Name << " - 数量:" << Amount 
             << " 单价:" << Price << endl;
    }
};
```

### 11.4 动态创建对象

```cpp
int main() {
    CGoods *pc1, *pc2, *pc3;
    
    // 1. 调用有参构造函数
    pc1 = new CGoods("夏利2000", 10, 118000);
    pc1->Show();
    
    // 2. 调用默认构造函数
    pc2 = new CGoods;
    pc2->Show();
    
    // 3. 动态创建对象数组
    // ⚠️ 注意：创建对象数组时，只能调用默认构造函数！
    int n;
    cout << "输入商品类数组元素数: ";
    cin >> n;
    pc3 = new CGoods[n];  // 调用n次默认构造函数
    
    // 释放内存
    delete pc1;       // 释放单个对象，调用析构函数
    delete pc2;       // 释放单个对象，调用析构函数
    delete[] pc3;     // 释放数组，调用n次析构函数
    
    return 0;
}
```

**运行结果示例**：
```
调用三参数构造函数
夏利2000 - 数量:10 单价:118000
调用默认构造函数
 - 数量:0 单价:0
调用默认构造函数
调用默认构造函数
调用默认构造函数
输入商品类数组元素数: 3
调用析构函数: 夏利2000
调用析构函数: 
调用析构函数: 
调用析构函数: 
调用析构函数: 
调用析构函数: 
```

### 11.5 ⚠️ 注意事项

1. **不要忘记 delete！**
   - `new` 分配的内存不会自动释放
   - 忘记 `delete` 会导致内存泄漏

2. **delete[] vs delete**
   - `new[]` 必须用 `delete[]` 释放
   - `new` 必须用 `delete` 释放
   - 混用会导致未定义行为

3. **避免野指针**
   ```cpp
   int *p = new int(10);
   delete p;
   p = nullptr;  // 释放后置空，避免野指针
   ```

4. **对象数组只能用默认构造函数**
   ```cpp
   class Test {
   public:
       Test() {}           // 必须有这个！
       Test(int x) {}
   };
   
   Test *p = new Test[10];  // OK，调用默认构造
   ```

### 11.6 new 失败的处理

```cpp
#include <new>  // 包含 std::nothrow

// 方式1：检查返回值
int *p = new (std::nothrow) int[100000000];
if (p == nullptr) {
    cout << "内存分配失败" << endl;
}

// 方式2：使用 try-catch
try {
    int *p = new int[100000000];
} catch (bad_alloc) {
    cout << "内存分配失败" << endl;
}
```

---

## 12. 浅复制与深复制

### 12.1 什么是浅复制？

**浅复制（Shallow Copy）**：默认的复制构造函数采用按成员复制的方式，只是简单地复制指针的值，不复制指针指向的内容。

```cpp
class Student {
public:
    char *pName;  // 指向动态内存的指针
    
    Student() {
        pName = nullptr;
    }
    
    Student(char *name) {
        if (pName = new char[strlen(name) + 1])
            strcpy(pName, name);
    }
    
    // ⚠️ 默认的复制构造函数（浅复制）
    Student(Student &s) {
        pName = s.pName;  // 只是复制了指针值！
    }
};
```

### 12.2 浅复制的问题

```
复制前：
┌─────────┐     ┌──────────────┐
│  obj1   │     │ "范英明"     │
│ pName ──┼────►│ (自由存储区)  │
└─────────┘     └──────────────┘

复制后（浅复制）：
┌─────────┐     ┌──────────────┐     ┌─────────┐
│  obj1   │     │ "范英明"     │     │  obj2   │
│ pName ──┼────►│ (自由存储区)  │◄────┼─ pName  │
└─────────┘     └──────────────┘     └─────────┘
                两个指针指向同一块内存！
                
问题1: obj1和obj2的数据会互相影响
问题2: delete时会导致双重释放！
```

### 12.3 深复制详解

**深复制（Deep Copy）**：在复制构造函数中，为新对象重新分配内存，并复制指针指向的内容。

```cpp
class Student {
public:
    char *pName;  // 指向动态内存的指针
    
    Student() {
        cout << "Constructor1（默认）" << endl;
        pName = nullptr;
    }
    
    Student(char *name) {
        cout << "Constructor2（有参）" << endl;
        if (pName = new char[strlen(name) + 1])
            strcpy(pName, name);
        cout << pName << endl;
    }
    
    // 析构函数
    ~Student() {
        cout << "Destructor（析构）" << endl;
        if (pName) {
            cout << "释放: " << pName << endl;
            delete[] pName;  // 释放字符串内存
        }
    }
    
    // ⭐ 深复制构造函数
    Student(Student &s) {
        cout << "Copy Constructor（复制构造）" << endl;
        if (s.pName) {
            // 为新对象分配独立的内存空间
            pName = new char[strlen(s.pName) + 1];
            // 复制字符串内容
            strcpy(pName, s.pName);
            cout << pName << endl;
        } else {
            pName = nullptr;
        }
    }
    
    // ⭐ 赋值运算符重载
    Student& operator=(Student &s) {
        cout << "Copy Assign Operator（赋值运算符）" << endl;
        
        // 防止自赋值
        if (this != &s) {
            // 释放原有内存（如果已分配）
            if (pName) {
                delete[] pName;
            }
            
            // 重新分配并复制
            if (s.pName) {
                pName = new char[strlen(s.pName) + 1];
                strcpy(pName, s.pName);
                cout << pName << endl;
            } else {
                pName = nullptr;
            }
        }
        return *this;
    }
};
```

### 12.4 深复制 vs 浅复制图解

```
深复制后：
┌─────────┐     ┌──────────────┐     ┌─────────┐     ┌──────────────┐
│  obj1   │     │ "范英明"     │     │  obj2   │     │ "范英明"     │
│ pName ──┼────►│ (自由存储区1) │     │ pName ──┼────►│ (自由存储区2) │
└─────────┘     └──────────────┘     └─────────┘     └──────────────┘
                独立的两块内存！
```

### 12.5 完整示例

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Student {
public:
    char *pName;
    
    Student() {
        cout << "Constructor1（默认）" << endl;
        pName = nullptr;
    }
    
    Student(char *name) {
        cout << "Constructor2（有参）" << endl;
        if (pName = new char[strlen(name) + 1])
            strcpy(pName, name);
    }
    
    ~Student() {
        cout << "Destructor（析构）" << endl;
        if (pName) {
            cout << "释放: " << pName << endl;
            delete[] pName;
        }
    }
    
    // 深复制构造函数
    Student(Student &s) {
        cout << "Copy Constructor（复制构造）" << endl;
        if (s.pName) {
            pName = new char[strlen(s.pName) + 1];
            strcpy(pName, s.pName);
        } else {
            pName = nullptr;
        }
    }
    
    // 赋值运算符重载
    Student& operator=(Student &s) {
        cout << "Copy Assign Operator（赋值运算符）" << endl;
        if (this != &s) {
            if (pName) delete[] pName;
            if (s.pName) {
                pName = new char[strlen(s.pName) + 1];
                strcpy(pName, s.pName);
            } else {
                pName = nullptr;
            }
        }
        return *this;
    }
    
    void Show() {
        if (pName)
            cout << "学生: " << pName << endl;
    }
};

int main() {
    cout << "=== 创建对象 ===" << endl;
    Student s1("范英明");
    Student s2("沈俊");
    Student s3;  // 默认构造
    
    cout << "\n=== 用已有对象初始化新对象（调用复制构造）===" << endl;
    Student s4 = s1;  // Student s4(s1);  调用复制构造函数
    
    cout << "\n=== 赋值操作（调用赋值运算符）===" << endl;
    s3 = s2;  // s3已经存在，用s2赋值给它
    
    cout << "\n=== 修改s4 ===" << endl;
    s4.Show();
    
    cout << "\n=== 函数结束，析构 ===" << endl;
    return 0;
}
```

**运行结果**：
```
=== 创建对象 ===
Constructor2（有参）
范英明
Constructor2（有参）
沈俊
Constructor1（默认）

=== 用已有对象初始化新对象（调用复制构造）===
Copy Constructor（复制构造）
范英明

=== 赋值操作（调用赋值运算符）===
Copy Assign Operator（赋值运算符）
沈俊

=== 修改s4 ===
学生: 范英明

=== 函数结束，析构 ===
Destructor（析构）
释放: 范英明
Destructor（析构）
释放: 沈俊
Destructor（析构）
释放: 沈俊
Destructor（析构）
释放: 范英明
```

### 12.6 何时需要深复制？

**需要深复制的情况**：
- 类中有指针成员
- 指针指向动态分配的内存
- 需要独立的数据副本

**不需要深复制的情况**：
- 只有基本类型成员（int, float, char等）
- 指针指向静态数据
- 共享数据是设计意图（如引用计数）

### 12.7 ⚠️ 注意事项

1. **赋值运算符重载要检查自赋值**
   ```cpp
   Student& operator=(Student &s) {
       if (this != &s) {  // 必须！
           // ...
       }
       return *this;
   }
   ```

2. **释放原有内存**
   ```cpp
   if (pName) delete[] pName;  // 先释放
   pName = new char[...];     // 再分配
   ```

3. **复制构造 vs 赋值运算符**
   ```cpp
   Student s2 = s1;  // 调用复制构造函数（创建新对象）
   s3 = s1;          // 调用赋值运算符（s3已存在）
   ```

4. **使用智能指针更安全**
   ```cpp
   #include <memory>
   
   class Student {
   public:
       std::unique_ptr<char[]> pName;  // 自动管理内存
       
       Student(const char *name) {
           pName = std::make_unique<char[]>(strlen(name) + 1);
           strcpy(pName.get(), name);
       }
       // 不需要手动析构、复制构造、赋值运算符！
   };
   ```

---

*返回 [主目录](../README.md)*
