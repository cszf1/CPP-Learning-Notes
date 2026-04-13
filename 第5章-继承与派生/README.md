# 第5章 继承与派生
> ⭐ **重点章节** - 面向对象编程的核心特性

---

## 📋 目录
1. [继承的基本概念](#1-继承的基本概念)
2. [派生类的定义](#2-派生类的定义)
3. [三种继承方式](#3-三种继承方式)
4. [派生类的构造函数与析构函数](#4-派生类的构造函数与析构函数)
5. [赋值兼容规则](#5-赋值兼容规则)
6. [多重继承](#6-多重继承)
7. [虚基类解决菱形继承](#7-虚基类解决菱形继承)

---

## 1. 继承的基本概念

### 1.1 为什么需要继承？

想象一下，我们要设计一个学校管理系统：

**方案一：不使用继承**
```cpp
// 老师
class Teacher {
    string name;
    int age;
    string title;      // 职称
    void teach() { }
    void giveExam() { }
};

// 学生
class Student {
    string name;
    int age;
    int grade;        // 年级
    float score;      // 成绩
    void study() { }
    void takeExam() { }
};
```

**问题**：老师和学生都有 `name` 和 `age`，代码重复！

**方案二：使用继承**
```cpp
// 公共属性抽取到基类
class Person {
    string name;
    int age;
};

// 老师继承自Person
class Teacher : public Person {
    string title;
    void teach() { }
};

// 学生继承自Person
class Student : public Person {
    int grade;
    float score;
    void study() { }
};
```

**好处**：
- ✅ 代码复用：公共属性只写一次
- ✅ 层次分明：体现 is-a 关系（老师是Person，学生是Person）
- ✅ 易于维护：修改公共属性只需改一处

### 1.2 继承的术语

```
┌─────────────────────────────────────────────────────────────────┐
│                        继承的术语                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│     ┌─────────────────────────────────────────┐                 │
│     │           基类（Base Class）             │                 │
│     │           又称：父类、超类                │                 │
│     │           被继承的类                      │                 │
│     └─────────────────────────────────────────┘                 │
│                         │                                       │
│                    继承 (Inherit)                                │
│                         │                                       │
│                         ▼                                       │
│     ┌─────────────────────────────────────────┐                 │
│     │         派生类（Derived Class）          │                 │
│     │           又称：子类                      │                 │
│     │           继承得到的类                    │                 │
│     └─────────────────────────────────────────┘                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 1.3 💡 通俗理解

> **继承就像 "遗传"**
> - 孩子继承父母的基因（公共属性）
> - 但孩子也可以有自己的特点（特有属性）
> - 儿子继承了父亲的姓，但也可能有母亲的某些特征

```cpp
// 人类（基类）
class Person {
public:
    string name;   // 姓名 - 人都有
    int age;       // 年龄 - 人都有
    void eat() { }      // 吃饭 - 人都会
    void sleep() { }    // 睡觉 - 人都会
};

// 老师（派生类）- 继承自Person
class Teacher : public Person {
public:
    string subject;  // 教授的科目 - 老师特有
    void teach() { } // 教书 - 老师特有
};

// 学生（派生类）- 继承自Person
class Student : public Person {
public:
    float score;     // 分数 - 学生特有
    void study() { } // 学习 - 学生特有
};
```

### 1.4 代码示例

```cpp
#include <iostream>
#include <string>
using namespace std;

// 基类：人
class Person {
public:
    string name;
    int age;
    
    void showInfo() {
        cout << "姓名: " << name << ", 年龄: " << age << endl;
    }
};

// 派生类：学生（继承自Person）
class Student : public Person {
public:
    float score;
    
    void showInfo() {
        // 可以访问基类的成员
        cout << "姓名: " << name << ", 年龄: " << age;
        cout << ", 成绩: " << score << endl;
    }
};

int main() {
    // 创建派生类对象
    Student s;
    s.name = "张三";
    s.age = 18;
    s.score = 92.5;
    
    // 基类的成员也可以访问
    s.showInfo();
    
    return 0;
}
/*
输出：
姓名: 张三, 年龄: 18, 成绩: 92.5
*/
```

---

## 2. 派生类的定义

### 2.1 语法格式

```cpp
class 派生类名 : 继承方式 基类名 {
    // 新增的成员
};
```

### 2.2 继承方式

| 继承方式 | 关键字 | 说明 |
|:---:|:---:|:---|
| 公用继承 | `public` | 基类的 public → 派生类 public |
| 私有继承 | `private` | 基类的所有成员 → 派生类 private |
| 保护继承 | `protected` | 基类的 public/protected → 派生类 protected |

```cpp
class Base {
public:
    int publicData;    // 公有成员
protected:
    int protectedData;  // 保护成员
private:
    int privateData;    // 私有成员
};

// 公有继承
class DerivedPublic : public Base {
    void func() {
        publicData = 1;      // ✅ 可以访问
        protectedData = 2;   // ✅ 可以访问（变成protected）
        // privateData = 3;   // ❌ 不能访问
    }
};

// 私有继承
class DerivedPrivate : private Base {
    void func() {
        publicData = 1;      // ✅ 可以访问（变成private）
        protectedData = 2;   // ✅ 可以访问（变成private）
        // privateData = 3;   // ❌ 不能访问
    }
};

// 保护继承
class DerivedProtected : protected Base {
    void func() {
        publicData = 1;      // ✅ 可以访问（变成protected）
        protectedData = 2;   // ✅ 可以访问（还是protected）
        // privateData = 3;   // ❌ 不能访问
    }
};
```

### 2.3 继承成员的变化表

```
┌─────────────────────────────────────────────────────────────────┐
│              基类成员在不同继承方式下的变化                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  基类成员    │  public继承  │  protected继承  │  private继承     │
│  ───────────┼──────────────┼────────────────┼────────────────  │
│  public     │  public      │  protected      │  private         │
│  protected  │  protected   │  protected      │  private         │
│  private    │ 不可访问     │  不可访问        │  不可访问         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 2.4 代码示例

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    int publicVar;
protected:
    int protectedVar;
private:
    int privateVar;
};

class Derived : public Base {
public:
    void access() {
        publicVar = 1;      // ✅ OK
        protectedVar = 2;   // ✅ OK
        // privateVar = 3;  // ❌ 错误！不能访问
    }
};

int main() {
    Derived d;
    d.publicVar = 10;       // ✅ OK：public成员可以外部访问
    
    // d.protectedVar = 20; // ❌ 错误：protected成员不能外部访问
    // d.privateVar = 30;   // ❌ 错误：private成员不能外部访问
    
    return 0;
}
```

---

## 3. 三种继承方式详解

### 3.1 public 公有继承（最常用）

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    string name;
    void eat() { cout << name << " 在吃东西" << endl; }
    
protected:
    void sleep() { cout << name << " 在睡觉" << endl; }
    
private:
    void think() { cout << name << " 在思考" << endl; }
};

class Dog : public Animal {
public:
    void bark() { 
        cout << name << " 汪汪叫" << endl; 
    }
    
    void rest() {
        sleep();  // ✅ 派生类可以访问protected成员
        // think(); // ❌ 不能访问private成员
    }
};

int main() {
    Dog d;
    d.name = "旺财";
    d.eat();   // ✅ public成员：外部可以访问
    d.bark();  // ✅ 派生类新增的成员
    
    // d.sleep(); // ❌ protected成员：外部不能访问
    // d.think(); // ❌ private成员：谁都不能访问
    
    return 0;
}
```

### 3.2 private 私有继承

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    int publicData;
protected:
    int protectedData;
};

class Derived : private Base {
    // publicData 和 protectedData 变成 private
public:
    void func() {
        publicData = 1;      // ✅ 可以访问
        protectedData = 2;   // ✅ 可以访问
    }
};

int main() {
    Derived d;
    // d.publicData = 10;    // ❌ 错误：已经变成private
    return 0;
}
```

### 3.3 protected 保护继承

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    int publicData;
protected:
    int protectedData;
};

class Derived : protected Base {
    // publicData 和 protectedData 变成 protected
public:
    void func() {
        publicData = 1;      // ✅ 可以访问
        protectedData = 2;   // ✅ 可以访问
    }
};

class SubDerived : public Derived {
public:
    void func2() {
        publicData = 1;      // ✅ 可以访问：继承了protected
        protectedData = 2;   // ✅ 可以访问
    }
};

int main() {
    Derived d;
    // d.publicData = 10;    // ❌ 错误：变成protected
    return 0;
}
```

### 3.4 💡 什么时候用什么继承方式？

| 继承方式 | 使用场景 |
|:---:|:---|
| `public` | **最常用**！保持 is-a 关系，保留基类接口 |
| `protected` | 想让派生类能用，但外部不能用 |
| `private` | 只想复用实现，不想暴露接口（像组合一样） |

```cpp
// ✅ public继承：最常见的继承方式
class Shape { };          // 图形
class Circle : public Shape { };   // 圆是图形

// ✅ private继承：像组合一样复用实现
class Engine { };          // 发动机
class Car : private Engine { };    // 汽车使用发动机（不是"是"发动机）

// ⚠️ protected继承：少见，用于特殊设计
```

---

## 4. 派生类的构造函数与析构函数

### 4.1 调用顺序

**原则**：
- **创建对象时**：先调用基类构造函数，再调用派生类构造函数
- **销毁对象时**：先调用派生类析构函数，再调用基类析构函数

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    Base() { cout << "1. Base 构造函数" << endl; }
    ~Base() { cout << "6. Base 析构函数" << endl; }
};

class Middle : public Base {
public:
    Middle() { cout << "2. Middle 构造函数" << endl; }
    ~Middle() { cout << "5. Middle 析构函数" << endl; }
};

class Derived : public Middle {
public:
    Derived() { cout << "3. Derived 构造函数" << endl; }
    ~Derived() { cout << "4. Derived 析构函数" << endl; }
};

int main() {
    cout << "=== 创建对象 ===" << endl;
    Derived obj;
    cout << "=== 销毁对象 ===" << endl;
    
    return 0;
}
/*
输出：
=== 创建对象 ===
1. Base 构造函数
2. Middle 构造函数
3. Derived 构造函数
=== 销毁对象 ===
4. Derived 析构函数
5. Middle 析构函数
6. Base 析构函数
*/
```

### 4.2 构造函数的执行细节

**派生类构造函数必须**：
1. 调用基类构造函数初始化继承来的成员
2. 调用新增成员的构造函数

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
public:
    string name;
    int age;
    
    Person(string n, int a) : name(n), age(a) {
        cout << "Person 构造函数: " << name << endl;
    }
};

class Student : public Person {
public:
    float score;
    
    // 派生类构造函数：需要调用基类构造函数
    Student(string n, int a, float s) : Person(n, a), score(s) {
        cout << "Student 构造函数: " << score << endl;
    }
};

int main() {
    Student s("张三", 18, 92.5f);
    cout << "姓名: " << s.name << ", 年龄: " << s.age << ", 成绩: " << s.score << endl;
    
    return 0;
}
/*
输出：
Person 构造函数: 张三
Student 构造函数: 92.5
姓名: 张三, 年龄: 18, 成绩: 92.5
*/
```

### 4.3 初始化列表的顺序

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int x;
    A(int val) : x(val) {
        cout << "A: x = " << x << endl;
    }
};

class B : public A {
public:
    int y;
    B(int a, int b) : A(a), y(b) {
        cout << "B: y = " << y << endl;
    }
};

int main() {
    B obj(10, 20);
    return 0;
}
/*
输出：
A: x = 10
B: y = 20
*/
```

### 4.4 默认调用基类构造函数

如果基类有无参构造函数，派生类可以省略调用：

```cpp
class Base {
public:
    Base() { cout << "Base()" << endl; }
    Base(int x) { cout << "Base(int)" << endl; }
};

class Derived : public Base {
public:
    Derived() { 
        // 默认调用 Base()，如果有的话
        cout << "Derived()" << endl; 
    }
    
    Derived(int x) : Base(x) { 
        // 显式调用 Base(int)
        cout << "Derived(int)" << endl; 
    }
};
```

### 4.5 析构函数的执行

析构函数不需要显式调用，系统自动按逆序调用：

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    ~Base() { cout << "Base 析构" << endl; }
};

class Derived : public Base {
public:
    ~Derived() { cout << "Derived 析构" << endl; }
};

int main() {
    Derived *p = new Derived();
    delete p;  // 先调用Derived析构，再调用Base析构
    
    return 0;
}
/*
输出：
Derived 析构
Base 析构
*/
```

### 4.6 ⚠️ 注意事项

```cpp
// ❌ 错误：构造函数不能被继承
class Base {
public:
    Base(int x) { }
};

class Derived : public Base {
public:
    // ❌ 不能直接写 Derived(int x) { } 继承构造函数
    Derived(int x) : Base(x) { }  // ✅ 正确方式
};

// ❌ 错误：析构函数也不是这样调用的
class Derived : public Base {
public:
    ~Derived() { 
        // 默认会调用 Base::~Base()
        // 不需要也不能显式调用基类析构函数
    }
};
```

---

## 5. 赋值兼容规则

### 5.1 基本规则

**公有继承**下，派生类对象可以：
1. 赋值给基类对象（只复制基类部分）
2. 赋值给基类引用
3. 赋值给基类指针

```cpp
class Base { 
public: 
    int baseData; 
};

class Derived : public Base { 
public: 
    int derivedData; 
};

int main() {
    Derived d;
    d.baseData = 10;
    d.derivedData = 20;
    
    // 1. 派生类 → 基类对象
    Base b = d;           // 只复制baseData
    // b.baseData = 10, b没有derivedData
    
    // 2. 派生类 → 基类引用
    Base &ref = d;        // ref指向d的Base部分
    
    // 3. 派生类 → 基类指针
    Base *ptr = &d;       // ptr指向d的Base部分
    
    return 0;
}
```

### 5.2 图示理解

```
┌─────────────────────────────────────────────────────────────────┐
│                      赋值兼容规则                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│     派生类对象 D                                                 │
│     ┌──────────────────────────┐                                 │
│     │       Base 部分          │  ← 可以赋值给 Base             │
│     │       (baseData)        │                                 │
│     ├──────────────────────────┤                                 │
│     │     Derived 部分          │                                 │
│     │     (derivedData)        │                                 │
│     └──────────────────────────┘                                 │
│                                                                  │
│     Base b = d;    // ✅ 允许，但丢失Derived部分                   │
│     Base &ref = d; // ✅ 引用整个对象（通过Base视角）               │
│     Base *ptr = &d; // ✅ 指针指向对象（通过Base视角）              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.3 代码示例

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    int baseData;
    void showBase() { cout << "Base: " << baseData << endl; }
};

class Derived : public Base {
public:
    int derivedData;
    void showDerived() { 
        cout << "Base: " << baseData 
             << ", Derived: " << derivedData << endl; 
    }
};

int main() {
    Derived d;
    d.baseData = 100;
    d.derivedData = 200;
    
    // 1. 派生类赋值给基类对象（切片）
    Base b = d;
    cout << "b.baseData = " << b.baseData << endl;  // 100
    // b.derivedData;  // ❌ 错误！没有这个成员
    
    // 2. 派生类赋值给基类引用
    Base &ref = d;
    ref.baseData = 300;
    cout << "通过ref修改后，d.baseData = " << d.baseData << endl;  // 300
    
    // 3. 派生类赋值给基类指针
    Base *ptr = &d;
    ptr->baseData = 400;
    cout << "通过ptr修改后，d.baseData = " << d.baseData << endl;  // 400
    
    return 0;
}
```

### 5.4 ⚠️ 注意事项

```cpp
// ❌ 私有继承：不满足赋值兼容规则
class Base { };
class Derived : private Base { };  // 私有继承

Base b;
Derived d;
// Base &ref = d;  // ❌ 错误！私有继承不能这样用

// ⚠️ protected继承：对外不满足兼容规则
class Derived : protected Base { };  // 保护继承

Base b;
Derived d;
// Base &ref = d;  // ❌ 错误！保护继承不能这样用
```

---

## 6. 多重继承

### 6.1 什么是多重继承？

一个派生类继承自多个基类：

```cpp
class A { };
class B { };
class C : public A, public B { };  // C同时继承A和B
```

### 6.2 代码示例

```cpp
#include <iostream>
using namespace std;

// 父亲
class Father {
public:
    int height = 175;
    void fatherInfo() { cout << "父亲身高: " << height << endl; }
};

// 母亲
class Mother {
public:
    int IQ = 120;
    void motherInfo() { cout << "母亲智商: " << IQ << endl; }
};

// 孩子：同时继承父亲和母亲
class Child : public Father, public Mother {
public:
    void childInfo() { 
        cout << "孩子身高像父亲: " << height << endl;
        cout << "孩子智商像母亲: " << IQ << endl;
    }
};

int main() {
    Child c;
    c.fatherInfo();   // 继承自Father
    c.motherInfo();   // 继承自Mother
    c.childInfo();     // 自己的方法
    
    return 0;
}
```

### 6.3 多继承的构造函数

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int a;
    A(int x) : a(x) { cout << "A(" << a << ")"; }
};

class B {
public:
    int b;
    B(int x) : b(x) { cout << " B(" << b << ")"; }
};

class C : public A, public B {
public:
    int c;
    C(int x, int y, int z) : A(x), B(y), c(z) { 
        cout << " C(" << c << ")" << endl; 
    }
};

int main() {
    cout << "创建C对象: ";
    C obj(1, 2, 3);
    // 输出: A(1) B(2) C(3)
    return 0;
}
```

### 6.4 二义性问题

当多个基类有同名成员时，访问会产生二义性：

```cpp
#include <iostream>
using namespace std;

class Base1 {
public:
    int data = 10;
};

class Base2 {
public:
    int data = 20;
};

class Derived : public Base1, public Base2 {
public:
    void show() {
        // ❌ 二义性：data 是 Base1 的还是 Base2 的？
        // cout << data << endl;  // 编译错误！
        
        // ✅ 解决方法：指定作用域
        cout << "Base1.data = " << Base1::data << endl;
        cout << "Base2.data = " << Base2::data << endl;
    }
};

int main() {
    Derived d;
    d.show();
    return 0;
}
```

---

## 7. 虚基类解决菱形继承

### 7.1 什么是菱形继承？

```
        A (基类)
       / \
      B   C  (继承A)
       \ /
        D   (同时继承B和C)
```

```cpp
class A { int data; };

class B : public A { };  // B从A继承
class C : public A { };  // C也从A继承

class D : public B, public C { };  // D从B和C继承
// 问题：D对象有两份A的data！
```

### 7.2 问题演示

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int data = 100;
};

class B : public A { };  // B有一份A
class C : public A { };  // C有一份A

class D : public B, public C { };  // D有两份A！

int main() {
    D d;
    
    // ❌ 二义性：不知道访问哪一份A
    // cout << d.data << endl;  // 错误！
    
    // ✅ 明确指定
    cout << "B::data = " << d.B::data << endl;  // 100
    cout << "C::data = " << d.C::data << endl;  // 100
    
    // ⚠️ 实际上d有两份data，浪费内存！
    cout << "sizeof(d) = " << sizeof(d) << endl;  // 比预期大
    
    return 0;
}
```

### 7.3 虚基类：解决方案

使用 `virtual` 关键字，让基类只保留一份：

```cpp
class A { int data; };

// virtual关键字让B和C共享A
class B : virtual public A { };  
class C : virtual public A { };

class D : public B, public C { };  // D只有一份A！
```

### 7.4 代码示例

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int data = 100;
    A() { cout << "A 构造函数" << endl; }
};

// 使用虚基类：virtual
class B : virtual public A {
public:
    B() { cout << "B 构造函数" << endl; }
};

class C : virtual public A {
public:
    C() { cout << "C 构造函数" << endl; }
};

class D : public B, public C {
public:
    D() { cout << "D 构造函数" << endl; }
};

int main() {
    cout << "=== 创建D对象 ===" << endl;
    D d;
    
    cout << "=== 访问data ===" << endl;
    cout << "d.data = " << d.data << endl;  // ✅ 只有一个data！
    
    return 0;
}
/*
输出：
=== 创建D对象 ===
A 构造函数        // A只构造一次！
B 构造函数
C 构造函数
D 构造函数
=== 访问data ===
d.data = 100
*/
```

### 7.5 虚基类的构造函数规则

```cpp
// 虚基类的构造由最派生类直接调用
class D : public B, public C {
public:
    // 必须在这里调用虚基类A的构造函数
    D() : A(10), B(), C() { }
};
```

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int data;
    A(int x) : data(x) { cout << "A(" << data << ")"; }
};

class B : virtual public A {
public:
    B() : A(10) { cout << " B()"; }
};

class C : virtual public A {
public:
    C() : A(20) { cout << " C()"; }
};

// 最派生类D必须负责初始化虚基类A
class D : public B, public C {
public:
    D() : A(999), B(), C() { cout << " D()"; }
};

int main() {
    cout << "创建D: ";
    D d;
    cout << endl << "d.data = " << d.data << endl;
    return 0;
}
/*
输出：
创建D: A(999) B() C() D()
d.data = 999
*/
```

---

## 📝 本章小结

### 核心概念

| 概念 | 说明 |
|:---|:---|
| **继承** | 派生类获取基类特征和行为的过程 |
| **基类/父类** | 被继承的类 |
| **派生类/子类** | 继承得到的类 |
| **公有继承** | `public`，最常用，保持 is-a 关系 |
| **私有继承** | `private`，像组合一样复用实现 |
| **保护继承** | `protected`，对外隐藏，对派生类可见 |
| **多重继承** | 一个类继承多个基类 |
| **虚基类** | `virtual`，解决菱形继承问题 |

### 构造/析构顺序

```
创建时：基类构造 → ... → 派生类构造
销毁时：派生类析构 → ... → 基类析构
```

### 赋值兼容规则（public继承）

```cpp
Derived d;
Base b = d;        // ✅ 切片：只复制基类部分
Base &ref = d;     // ✅ 引用
Base *ptr = &d;    // ✅ 指针
```

### 虚基类解决菱形继承

```cpp
class A { };
class B : virtual public A { };
class C : virtual public A { };
class D : public B, public C { };  // 只有一份A
```

---

## 🔍 常见问题

**Q: 私有成员能被继承吗？**
A: 能被继承，但不能直接访问。派生类通过基类的公有/保护方法间接访问。

**Q: 构造函数能被继承吗？**
A: 不能。但派生类构造函数必须调用基类构造函数来初始化继承的成员。

**Q: 什么时候用私有继承？**
A: 当你只想复用基类的实现，但不想保持 is-a 关系时。这更像是"有一个"而不是"是一个"。

**Q: 虚基类为什么能解决菱形继承？**
A: 因为虚继承让所有虚继承的路径共享同一个基类子对象，只有一份。

---

## 📚 练习题

### 练习1：动物继承体系
设计一个动物继承体系：
- 基类 `Animal`：有名字、年龄，会呼吸、会吃东西
- 派生类 `Dog`：会吠叫、摇尾巴
- 派生类 `Cat`：会喵喵叫、抓老鼠

### 练习2：形状继承体系
设计一个形状继承体系：
- 基类 `Shape`：计算面积的方法（纯虚函数）
- 派生类 `Circle`：圆
- 派生类 `Rectangle`：矩形

### 练习3：解决二义性
```cpp
class Base1 { public: void func(); };
class Base2 { public: void func(); };
class Derived : public Base1, public Base2 { };
// 调用 func() 时如何解决二义性？
```

### 练习4：菱形继承
画出菱形继承的类图，并用虚基类解决。

---

*返回 [主目录](../README.md)*
