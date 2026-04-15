# 第6章 多态与虚函数
> ⭐ **重点章节** - 面向对象编程的精髓

---

## 📋 目录
1. [多态的基本概念](#1-多态的基本概念)
2. [虚函数](#2-虚函数)
3. [纯虚函数和抽象类](#3-纯虚函数和抽象类)
4. [虚析构函数](#4-虚析构函数)
5. [override和final关键字](#5-override和final关键字)
6. [多态的实现原理](#6-多态的实现原理)

---

## 1. 多态的基本概念

### 1.1 为什么需要多态？

**场景**：编写一个游戏，有不同类型的图形（圆、矩形、三角形），需要计算它们的面积。

**不用多态的方式**：
```cpp
#include <iostream>
using namespace std;

class Circle {
public:
    double radius;
    double area() { return 3.14159 * radius * radius; }
};

class Rectangle {
public:
    double width, height;
    double area() { return width * height; }
};

class Triangle {
public:
    double base, height;
    double area() { return 0.5 * base * height; }
};

// 使用：需要判断类型
void printArea(Object *obj) {
    if (type == Circle) {
        // 强制转换
    } else if (type == Rectangle) {
        // 强制转换
    }
    // ❌ 糟糕！每加一个图形都要修改这个函数
}
```

**使用多态的方式**：
```cpp
#include <iostream>
using namespace std;

// 基类：图形
class Shape {
public:
    // 基类声明虚函数
    virtual double area() { return 0; }
};

// 派生类：圆
class Circle : public Shape {
public:
    double radius;
    double area() override { return 3.14159 * radius * radius; }
};

// 派生类：矩形
class Rectangle : public Shape {
public:
    double width, height;
    double area() override { return width * height; }
};

// ✅ 多态：同一个接口，不同实现
void printArea(Shape *shape) {
    cout << "面积: " << shape->area() << endl;
    // 不需要知道具体是什么类型！
    // 系统会根据实际对象类型调用正确的函数
}

int main() {
    Circle c;
    c.radius = 5;
    
    Rectangle r;
    r.width = 4;
    r.height = 3;
    
    printArea(&c);  // 输出: 面积: 78.5397
    printArea(&r);  // 输出: 面积: 12
    
    return 0;
}
```

### 1.2 多态的分类

```
┌─────────────────────────────────────────────────────────────────┐
│                        多态的两种类型                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────┐  ┌─────────────────────────┐     │
│  │    编译时多态            │  │    运行时多态            │     │
│  │  (Compile-time Polymorphism) │  │  (Runtime Polymorphism) │     │
│  ├─────────────────────────┤  ├─────────────────────────┤     │
│  │  • 函数重载              │  │  • 虚函数               │     │
│  │  • 运算符重载            │  │  • override             │     │
│  │                         │  │                         │     │
│  │  在编译时确定调用哪个    │  │  在运行时确定调用哪个    │     │
│  │  函数                    │  │  函数                   │     │
│  └─────────────────────────┘  └─────────────────────────┘     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 1.3 💡 通俗理解多态

> **多态就像"一个命令，不同的反应"**
> 
> - 老师说"去考试"：班长去布置考场，学霸去考试，学渣去抄答案
> - 同样是"去考试"这个命令，不同角色有不同的行为
> - **重要的是发出命令的人不需要知道每个人的具体行为**

```cpp
// 就像一个接口，多个实现
class Animal {
public:
    virtual void speak() { }  // 接口：发出声音
};

class Dog : public Animal {
public:
    void speak() override { cout << "汪汪汪" << endl; }
};

class Cat : public Animal {
public:
    void speak() override { cout << "喵喵喵" << endl; }
};

class Duck : public Animal {
public:
    void speak() override { cout << "嘎嘎嘎" << endl; }
};

void makeSpeak(Animal *animal) {
    animal->speak();  // 不需要知道具体是什么动物！
}

int main() {
    Dog d;
    Cat c;
    Duck duck;
    
    makeSpeak(&d);    // 汪汪汪
    makeSpeak(&c);    // 喵喵喵
    makeSpeak(&duck); // 嘎嘎嘎
    
    return 0;
}
```

### 1.4 代码示例：完整的图形系统

```cpp
#include <iostream>
using namespace std;

// 基类：图形
class Shape {
public:
    // 虚函数：用于计算面积
    virtual double getArea() { 
        return 0; 
    }
    
    // 虚函数：用于获取名称
    virtual string getName() {
        return "图形";
    }
    
    // 基类可以提供默认实现
    void print() {
        cout << getName() << " 面积: " << getArea() << endl;
    }
};

// 圆
class Circle : public Shape {
public:
    double radius;
    
    Circle(double r) : radius(r) { }
    
    double getArea() override {
        return 3.14159 * radius * radius;
    }
    
    string getName() override {
        return "圆";
    }
};

// 矩形
class Rectangle : public Shape {
public:
    double width, height;
    
    Rectangle(double w, double h) : width(w), height(h) { }
    
    double getArea() override {
        return width * height;
    }
    
    string getName() override {
        return "矩形";
    }
};

int main() {
    // 创建不同类型的图形
    Circle c(5);
    Rectangle r(4, 3);
    
    // 使用基类指针调用（多态）
    Shape *shapes[] = {&c, &r};
    
    for (int i = 0; i < 2; i++) {
        shapes[i]->print();  // 调用的是实际类型的函数！
    }
    
    return 0;
}
/*
输出：
圆 面积: 78.5397
矩形 面积: 12
*/
```

---

## 2. 虚函数

### 2.1 什么是虚函数？

**虚函数**是在基类中声明为 `virtual` 的成员函数，允许派生类**重写(override)**它，从而实现运行时多态。

```cpp
class Base {
public:
    virtual void func() {  // virtual 关键字
        cout << "Base::func()" << endl;
    }
};

class Derived : public Base {
public:
    void func() override {  // 重写基类的虚函数
        cout << "Derived::func()" << endl;
    }
};
```

### 2.2 virtual 关键字

```cpp
class Animal {
public:
    // 虚函数：在基类中声明
    virtual void speak() {
        cout << "动物叫" << endl;
    }
    
    // 非虚函数：派生类不能重写
    void breathe() {
        cout << "呼吸" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() override {  // ✅ 重写虚函数
        cout << "汪汪汪" << endl;
    }
    
    void breathe() {  // 这不是重写，是新函数
        cout << "狗在呼吸" << endl;
    }
};
```

### 2.3 多态的调用方式

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void func() {
        cout << "Base::func()" << endl;
    }
};

class Derived : public Base {
public:
    void func() override {
        cout << "Derived::func()" << endl;
    }
};

int main() {
    Derived d;
    Base *ptr = &d;  // 基类指针指向派生类对象
    
    // 关键：通过指针调用虚函数
    ptr->func();  // 调用的是 Derived::func()！
    
    return 0;
}
/*
输出：
Derived::func()
*/
```

### 2.4 virtual 的继承性

一旦函数在基类中被声明为 `virtual`，派生类中的同名函数**自动也是虚函数**（可以不加 `override`，但建议加上）。

```cpp
class A {
public:
    virtual void func() { }  // 虚函数
};

class B : public A {
public:
    void func() { }  // 自动也是虚函数（可以不加virtual）
};

class C : public B {
public:
    void func() { }  // 也是虚函数
};
```

### 2.5 ⚠️ 注意事项

```cpp
// ❌ 错误1：只有虚函数才能被重写
class Base {
    void func() { }  // 普通函数
};
class Derived : public Base {
    void func() { }  // 这是新函数，不是重写！
};

// ❌ 错误2：参数必须完全一致
class Base {
    virtual void func(int x) { }
};
class Derived : public Base {
    virtual void func(double x) { }  // ❌ 不是重写！参数不同
};

// ✅ 错误3：静态函数不能是虚函数
class Base {
    static virtual void func() { }  // ❌ 错误！
};

// ✅ 正确
class Derived : public Base {
    void func() override { }  // ✅ 正确的重写
};
```

---

## 3. 纯虚函数和抽象类

### 3.1 什么是纯虚函数？

**纯虚函数**是只有声明没有实现的虚函数，用 `= 0` 表示：

```cpp
class Shape {
public:
    // 纯虚函数：没有实现
    virtual double area() = 0;
};
```

### 3.2 抽象类

包含**纯虚函数**的类是**抽象类**：

```cpp
// 抽象类
class Shape {
public:
    virtual double area() = 0;  // 纯虚函数
    virtual ~Shape() { }        // 虚析构函数（后面讲）
};
```

### 3.3 抽象类的特点

| 特点 | 说明 |
|:---|:---|
| 不能实例化 | `Shape s;` ❌ 错误 |
| 可以定义指针/引用 | `Shape *ptr;` ✅ 可以 |
| 派生类必须实现纯虚函数 | 否则派生类也是抽象类 |
| 用于定义接口 | 提供统一的接口规范 |

### 3.4 代码示例

```cpp
#include <iostream>
#include <string>
using namespace std;

// 抽象类：形状
class Shape {
public:
    // 纯虚函数：计算面积
    virtual double area() = 0;
    
    // 纯虚函数：获取名称
    virtual string name() = 0;
    
    // 提供公共功能
    void print() {
        cout << name() << " 面积: " << area() << endl;
    }
    
    // 虚析构函数（必须）
    virtual ~Shape() { }
};

// 具体类：圆
class Circle : public Shape {
private:
    double radius;
public:
    Circle(double r) : radius(r) { }
    
    double area() override {
        return 3.14159 * radius * radius;
    }
    
    string name() override {
        return "圆";
    }
};

// 具体类：矩形
class Rectangle : public Shape {
private:
    double width, height;
public:
    Rectangle(double w, double h) : width(w), height(h) { }
    
    double area() override {
        return width * height;
    }
    
    string name() override {
        return "矩形";
    }
};

int main() {
    // ✅ 可以定义抽象类的指针
    Shape *shapes[3];
    
    Circle c(5);
    Rectangle r(4, 3);
    Circle c2(3);
    
    shapes[0] = &c;
    shapes[1] = &r;
    shapes[2] = &c2;
    
    // 使用多态
    for (int i = 0; i < 3; i++) {
        shapes[i]->print();  // 调用的是实际类型的函数
    }
    
    // ❌ 不能创建抽象类的对象
    // Shape s;  // 错误！
    // Shape *p = new Shape();  // 错误！
    
    return 0;
}
/*
输出：
圆 面积: 78.5397
矩形 面积: 12
圆 面积: 28.2743
*/
```

### 3.5 💡 抽象类的通俗理解

> **抽象类就像"菜单"**
> - 菜单定义了菜名和价格
> - 但菜单本身不能吃（不能实例化）
> - 具体菜品（派生类）才是能吃的东西

```cpp
// 菜单（抽象类）
class Dish {
public:
    virtual string getName() = 0;  // 菜名
    virtual double getPrice() = 0;  // 价格
};

// 具体菜品
class TomatoEgg : public Dish {
public:
    string getName() override { return "西红柿炒蛋"; }
    double getPrice() override { return 15.0; }
};

class MapoTofu : public Dish {
public:
    string getName() override { return "麻婆豆腐"; }
    double getPrice() override { return 20.0; }
};
```

### 3.6 ⚠️ 派生类必须实现所有纯虚函数

```cpp
class Base {
public:
    virtual void func1() = 0;
    virtual void func2() = 0;
};

// ✅ 完全实现：不再是抽象类
class Derived1 : public Base {
public:
    void func1() override { }
    void func2() override { }
};

// ❌ 只实现一个：还是抽象类
class Derived2 : public Base {
public:
    void func1() override { }
    // func2() 没有实现，Derived2还是抽象类
};
```

---

## 4. 虚析构函数

### 4.1 为什么需要虚析构函数？

**问题场景**：用 `new` 创建派生类对象，用基类指针删除

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    Base() { cout << "Base 构造函数" << endl; }
    
    ~Base() { 
        cout << "Base 析构函数" << endl; 
    }
};

class Derived : public Base {
public:
    Derived() { 
        cout << "Derived 构造函数" << endl;
        data = new int(100);  // 分配内存
    }
    
    ~Derived() {
        cout << "Derived 析构函数" << endl;
        delete data;  // 释放内存
    }
    
private:
    int *data;
};

int main() {
    Base *ptr = new Derived();  // 基类指针指向派生类对象
    delete ptr;  // ⚠️ 只调用了Base的析构函数！
    
    return 0;
}
/*
输出（错误）：
Base 构造函数
Derived 构造函数
Base 析构函数        // ❌ 缺少 Derived 析构函数！
// 内存泄漏！data指向的内存没有释放
*/
```

### 4.2 解决方案：虚析构函数

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    Base() { cout << "Base 构造函数" << endl; }
    
    virtual ~Base() {  // virtual 析构函数
        cout << "Base 析构函数" << endl;
    }
};

class Derived : public Base {
public:
    Derived() { 
        cout << "Derived 构造函数" << endl;
        data = new int(100);
    }
    
    ~Derived() {
        cout << "Derived 析构函数" << endl;
        delete data;
    }
    
private:
    int *data;
};

int main() {
    Base *ptr = new Derived();
    delete ptr;  // ✅ 正确调用了Derived析构函数
    
    return 0;
}
/*
输出（正确）：
Base 构造函数
Derived 构造函数
Derived 析构函数     // ✅ 正确！
Base 析构函数
*/
```

### 4.3 虚析构函数规则

```cpp
// ✅ 只要有可能被继承，就应该把析构函数声明为虚函数
class Base {
public:
    virtual ~Base() { }  // 虚析构函数
};

// ✅ 抽象类的虚析构函数也需要实现
class Abstract {
public:
    virtual void func() = 0;
    virtual ~Abstract() { }  // 必须有实现
};

// ⚠️ 如果析构函数不是虚函数，delete基类指针不会调用派生类析构函数
```

### 4.4 代码示例：完整的清理机制

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Animal {
protected:
    char *name;
public:
    Animal(const char *n) {
        name = new char[strlen(n) + 1];
        strcpy(name, n);
        cout << "Animal 构造函数: " << name << endl;
    }
    
    // ✅ 虚析构函数：确保正确清理
    virtual ~Animal() {
        cout << "Animal 析构函数: " << name << endl;
        delete[] name;
    }
    
    virtual void speak() = 0;  // 纯虚函数
};

class Dog : public Animal {
private:
    char *breed;
public:
    Dog(const char *n, const char *b) : Animal(n) {
        breed = new char[strlen(b) + 1];
        strcpy(breed, b);
        cout << "Dog 构造函数: " << breed << endl;
    }
    
    ~Dog() {
        cout << "Dog 析构函数: " << breed << endl;
        delete[] breed;
    }
    
    void speak() override {
        cout << name << " 说: 汪汪汪" << endl;
    }
};

int main() {
    Animal *animals[2];
    animals[0] = new Dog("旺财", "金毛");
    animals[1] = new Dog("大黄", "中华田园犬");
    
    for (int i = 0; i < 2; i++) {
        animals[i]->speak();
    }
    
    cout << "--- 清理 ---" << endl;
    for (int i = 0; i < 2; i++) {
        delete animals[i];  // ✅ 正确调用析构函数
    }
    
    return 0;
}
/*
输出：
Animal 构造函数: 旺财
Dog 构造函数: 金毛
Animal 构造函数: 大黄
Dog 构造函数: 中华田园犬
旺财 说: 汪汪汪
大黄 说: 汪汪汪
--- 清理 ---
Dog 析构函数: 金毛
Animal 析构函数: 旺财
Dog 析构函数: 中华田园犬
Animal 析构函数: 大黄
*/
```

---

## 5. override和final关键字

### 5.1 override 关键字（C++11）

`override` 表示这个函数**必须**重写基类的虚函数，编译器会检查。

```cpp
class Base {
public:
    virtual void func() { }
};

class Derived : public Base {
public:
    void func() override { }  // ✅ 正确：确实重写了
};

class Wrong : public Base {
public:
    void func(int x) override { }  // ❌ 错误！
    // 编译器会报错，因为参数不匹配
};
```

### 5.2 为什么使用 override？

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void func() {
        cout << "Base::func()" << endl;
    }
    
    virtual void process() {
        cout << "Base::process()" << endl;
    }
};

class Derived : public Base {
public:
    // ❌ 拼写错误！本意是想重写 func，但写成了 fuuc
    // 没有 override 关键字，编译器不会报错
    void fuuc() {  // 这是新函数，不是重写！
        cout << "Derived::fuuc()" << endl;
    }
    
    // ✅ 正确的拼写，加了 override
    void process() override {
        cout << "Derived::process()" << endl;
    }
};

int main() {
    Base *ptr = new Derived();
    ptr->func();    // 调用 Base::func()！不是Derived的！
    ptr->process(); // 调用 Derived::process()
    
    delete ptr;
    return 0;
}
```

### 5.3 final 关键字（C++11）

`final` 表示这个函数**不能**被重写，或者这个类**不能**被继承。

```cpp
// 1. 虚函数不能被重写
class Base {
public:
    virtual void func() final {  // 不能被重写
        cout << "Base::func()" << endl;
    }
};

class Derived : public Base {
public:
    // ❌ 错误！func 不能被重写
    void func() override { }  // 编译错误！
};

// 2. 类不能被继承
class Base2 final { };  // 不能被继承

class Derived2 : public Base2 { };  // ❌ 错误！
```

### 5.4 代码示例

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void func1() {
        cout << "Base::func1()" << endl;
    }
    
    virtual void func2() final {
        cout << "Base::func2() - final" << endl;
    }
};

class Derived : public Base {
public:
    void func1() override {  // ✅ 可以重写
        cout << "Derived::func1()" << endl;
    }
    
    // ❌ 不能重写：final
    // void func2() override { }
};

// ⚠️ 编译会报错：
// error: overriding final function 'virtual void Base::func2()'
```

```cpp
// final 类
class Animal final {
public:
    virtual void speak() = 0;
};

// ❌ 错误：不能继承 final 类
// class Dog : public Animal { };
```

### 5.5 ⚠️ 注意事项

```cpp
// ⚠️ override 和 final 可以一起用吗？可以
class Base {
    virtual void func() { }
};

class Derived : public Base {
    // 这是最后的实现，不能再被重写
    void func() override final {  // ✅
        cout << "final override" << endl;
    }
};

class SubDerived : public Derived {
    // ❌ 错误：func 已经 final 了
    // void func() override { }
};
```

---

## 6. 多态的实现原理

### 6.1 虚函数表（vtable）

每个包含虚函数的类都有一个**虚函数表**：

```
┌─────────────────────────────────────────────────────────────────┐
│                    虚函数表（vtable）                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   对象内存布局：                          类的虚函数表：           │
│   ┌────────────────┐                     ┌────────────────┐     │
│   │   vptr (指向   │ ─────────────────▶  │  func1() ──▶   │     │
│   │   虚函数表)    │                     │  func2() ──▶   │     │
│   ├────────────────┤                     │  func3() ──▶   │     │
│   │   成员1        │                     └────────────────┘     │
│   ├────────────────┤                                            │
│   │   成员2        │                                            │
│   └────────────────┘                                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 6.2 多态的调用过程

```cpp
Base *ptr = new Derived();
ptr->func();  // 调用虚函数
```

```
1. 通过 ptr 找到对象
2. 通过对象的 vptr 找到虚函数表
3. 在虚函数表中查找 func
4. 调用实际类型（Derived）的 func
```

### 6.3 代码验证

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    int a;
    virtual void func() { cout << "Base::func()" << endl; }
};

class Derived : public Base {
public:
    int b;
    void func() override { cout << "Derived::func()" << endl; }
};

int main() {
    Base b;
    Derived d;
    
    cout << "Base size: " << sizeof(b) << endl;  // 16 = 8(vptr) + 4(a) + 4(对齐)
    cout << "Derived size: " << sizeof(d) << endl;  // 24 = 8(vptr) + 4(a) + 4(b) + 4(对齐)
    
    return 0;
}
```

### 6.4 内联与虚函数

```cpp
class Base {
public:
    virtual void func() { cout << "Base"; }  // 虚函数不能inline
};

class Derived : public Base {
public:
    void func() override { cout << "Derived"; }  // 也不能inline
};
```

---

## 📝 本章小结

### 核心概念

| 概念 | 说明 |
|:---|:---|
| **多态** | 同一个接口，不同实现 |
| **编译时多态** | 函数重载、运算符重载 |
| **运行时多态** | 虚函数、override |
| **虚函数** | `virtual`，允许派生类重写 |
| **纯虚函数** | `virtual void func() = 0`，没有实现 |
| **抽象类** | 包含纯虚函数的类，不能实例化 |
| **虚析构函数** | `virtual ~Base()`，确保正确清理 |
| **override** | 显式声明重写，编译器检查 |
| **final** | 禁止重写或禁止继承 |

### 多态的使用场景

```cpp
// 1. 统一接口
void process(Shape *shape) {
    shape->draw();  // 不管是什么图形
}

// 2. 回调机制
class Button {
    virtual void onClick() = 0;
};

// 3. 插件系统
class Plugin {
    virtual void execute() = 0;
};
```

### 多态的必要条件

```
┌─────────────────────────────────────────┐
│           实现运行时多态需要：            │
├─────────────────────────────────────────┤
│  ✅ 基类声明虚函数（virtual）            │
│  ✅ 派生类重写该函数（override）          │
│  ✅ 通过基类指针/引用调用                │
└─────────────────────────────────────────┘
```

---

## 🔍 常见问题

**Q: 什么情况下函数不会被多态调用？**
A: 通过对象（不是指针/引用）调用时：`Derived d; d.func();`

**Q: 构造函数可以是虚函数吗？**
A: 不能！构造函数在对象创建时调用，此时还没有vptr。

**Q: 析构函数为什么要声明为虚函数？**
A: 为了通过基类指针删除派生类对象时能正确调用派生类析构函数。

**Q: 纯虚函数可以有实现吗？**
A: 可以！派生类不强制要求实现，但基类可以提供默认实现。

**Q: override和final有什么区别？**
A: `override`检查是否正确重写，`final`阻止被重写。

---

## 📚 练习题

### 练习1：图形面积计算
使用抽象类和多态实现计算不同图形的面积。

### 练习2：动物园
```cpp
class Animal {
    // 添加 speak() 纯虚函数
};

class Dog, Cat, Duck : public Animal {
    // 实现各自的声音
};

// 实现 feed(Animal *animal) 函数
```

### 练习3：虚析构函数
创建一个有内存分配的基类和派生类，验证虚析构函数的作用。

### 练习4：override检测
编写错误的override代码，观察编译器报错。

---

*返回 [主目录](../README.md)*
