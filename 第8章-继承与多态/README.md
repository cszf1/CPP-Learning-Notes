# 第8章 继承与多态

> 本章目标：理解类之间如何复用和扩展。继承像家族关系，多态像同一个按钮，不同机器按下去做不同事。

## 目录

1. 继承与派生
2. 继承方式
3. 派生类构造与析构
4. 赋值兼容与对象切片
5. 多重继承与虚基类
6. 虚函数与运行时多态
7. 纯虚函数与抽象类
8. 虚析构函数
9. 本章小结

## 1. 继承与派生

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

## 2. 继承方式

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

### 2.5 三种继承方式详解

#### 2.5.1 public 公有继承（最常用）

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

#### 2.5.2 private 私有继承

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

#### 2.5.3 protected 保护继承

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

#### 2.5.4 💡 什么时候用什么继承方式？

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

## 3. 派生类构造与析构

### 3.1 调用顺序

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

### 3.2 构造函数的执行细节

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

### 3.3 初始化列表的顺序

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

### 3.4 默认调用基类构造函数

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

### 3.5 析构函数的执行

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

### 3.6 ⚠️ 注意事项

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

## 4. 赋值兼容与对象切片

### 4.1 基本规则

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

### 4.2 图示理解

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

### 4.3 代码示例

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

### 4.4 ⚠️ 注意事项

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

## 5. 多重继承与虚基类

#### 6.6.1 什么是多重继承？

一个派生类继承自多个基类：

```cpp
class A { };
class B { };
class C : public A, public B { };  // C同时继承A和B
```

#### 6.6.2 代码示例

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

#### 6.6.3 多继承的构造函数

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

#### 6.6.4 二义性问题

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

#### 6.6.2 虚基类解决菱形继承

#### 5.5.1 什么是菱形继承？

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

#### 5.5.2 问题演示

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

#### 5.5.3 虚基类：解决方案

使用 `virtual` 关键字，让基类只保留一份：

```cpp
class A { int data; };

// virtual关键字让B和C共享A
class B : virtual public A { };  
class C : virtual public A { };

class D : public B, public C { };  // D只有一份A！
```

#### 5.5.4 代码示例

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

#### 5.5.5 虚基类的构造函数规则

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

## 6. 虚函数与运行时多态

#### 6.7.1 什么是虚函数？

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

#### 6.7.2 virtual 关键字

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

#### 6.7.3 多态的调用方式

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

#### 6.7.4 virtual 的继承性

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

#### 6.7.5 ⚠️ 注意事项

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

## 7. 纯虚函数与抽象类

### 7.1 什么是纯虚函数？

**纯虚函数**是只有声明没有实现的虚函数，用 `= 0` 表示：

```cpp
class Shape {
public:
    // 纯虚函数：没有实现
    virtual double area() = 0;
};
```

### 7.2 抽象类

包含**纯虚函数**的类是**抽象类**：

```cpp
// 抽象类
class Shape {
public:
    virtual double area() = 0;  // 纯虚函数
    virtual ~Shape() { }        // 虚析构函数（后面讲）
};
```

### 7.3 抽象类的特点

| 特点 | 说明 |
|:---|:---|
| 不能实例化 | `Shape s;` ❌ 错误 |
| 可以定义指针/引用 | `Shape *ptr;` ✅ 可以 |
| 派生类必须实现纯虚函数 | 否则派生类也是抽象类 |
| 用于定义接口 | 提供统一的接口规范 |

### 7.4 代码示例

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

### 7.5 💡 抽象类的通俗理解

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

### 7.6 ⚠️ 派生类必须实现所有纯虚函数

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

## 8. 虚析构函数

### 8.1 为什么需要虚析构函数？

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

### 8.2 解决方案：虚析构函数

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

### 8.3 虚析构函数规则

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

### 8.4 代码示例：完整的清理机制

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

#### 6.7.6 override和final关键字

#### 6.6.1 override 关键字（C++11）

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

#### 6.6.2 为什么使用 override？

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

#### 6.6.3 final 关键字（C++11）

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

#### 6.6.4 代码示例

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

#### 6.6.5 ⚠️ 注意事项

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

#### 6.7.7 多态的实现原理

#### 6.7.1 虚函数表（vtable）

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

#### 6.7.2 多态的调用过程

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

#### 6.7.3 代码验证

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

#### 6.7.4 内联与虚函数

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

# 第9讲 继承与多态

> 东南大学 C++ 课件笔记 | 王文博 2026

---

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

### 附录A：课件补充 - 继承与派生

> 东南大学 C++ 课件笔记 | 王文博 2026

---

### 1. 继承与派生的概念

#### 1.1 继承与派生的产生

在定义一个类A时，若它使用了一个已定义类R的部分或全部成员，称类A继承了类R：
- **类R**：基类（或父类）
- **类A**：派生类（或子类）

一个派生类又可以作为另一个类的基类，一个基类可以派生出若干个派生类，这样便构成了**类树**。

#### 1.2 继承的现实世界例子

```
校内在册人员（基类）
├── 学生（派生类）
│   ├── 研究生
│   └── 本科生
└── 教职工（派生类）
    ├── 专职教师
    └── 行政人员
```

#### 1.3 继承与派生的目的

| 概念 | 目的 |
|------|------|
| **继承** | 实现代码重用 |
| **派生** | 当新的问题出现，原有程序无法有效解决时，对原有程序进行改造 |

#### 1.4 C++ 的两种继承方式

- **单一继承**：一个派生类只从一个基类派生
- **多重继承**：一个派生类有两个或多个基类

#### 1.5 派生的声明方式

```cpp
class 派生类名: [继承方式] 基类名
{
    派生类新增加的成员
};
```

---

### 2. 三种派生方式

| 派生方式 | 类内访问 | 类外访问 | 多层派生 |
|----------|----------|----------|----------|
| **public（公有）** | 基类public/protected保持原属性 | 可访问基类public成员 | 可继续派生 |
| **private（私有）** | 基类public/protected变为private | 不可访问基类任何成员 | 不可再派生（被"封死"） |
| **protected（保护）** | 基类public/protected变为protected | 不可访问基类任何成员 | 可继续派生 |

> 注意：若派生方式缺省，则默认为 **private**

#### 2.1 公有派生

```cpp
class Student {
private:
    int num;
    string name;
    char sex;
public:
    void Set_value() { num=1; name="王琳"; sex='w'; }
    void display() { cout << num << name << sex; }
};

class Student1: public Student {  // 公有派生
public:
    void display_new() {
        // 错误：不可直接访问基类私有成员
        // cout << num;  ❌
        // 正确：通过基类公有函数访问
        display();  ✓
    }
private:
    int age;
    string addr;
};
```

**规则**：基类的公用成员和保护成员在派生类中保持原有访问属性，基类的私有成员在派生类中**不可直接访问**。

#### 2.2 私有派生

```cpp
class Rectangle: private Point {  // 私有派生
public:
    void InitR(float x, float y, float w, float h) {
        W = w; H = h;
        InitP(x, y);  // 可调用基类公有函数
        // Move(xOff, yOff);  ❌ 私有派生后不可外部访问
    }
private:
    float W, H;
};
```

#### 2.3 保护派生

```cpp
class B: protected A {  // 保护派生
    // 基类的public和protected成员变为protected
    // 可在派生类中继续访问，但类外不可访问
};
```

#### 2.4 派生方式汇总表

| 基类成员 | public派生 | private派生 | protected派生 |
|----------|-----------|-------------|---------------|
| public | public | private | protected |
| protected | protected | private | protected |
| private | **不可直接访问** | **不可直接访问** | **不可直接访问** |

---

### 3. 派生类的构造函数与析构函数

#### 3.1 基本格式

```cpp
派生类构造函数名(总参数表列):
    基类构造函数名(参数表列)
{
    派生类中新增数据成员初始化语句
}
```

#### 3.2 执行顺序

```
构造顺序：
1. 调用基类构造函数
2. 调用子对象构造函数（如果有）
3. 执行派生类构造函数本身

析构顺序：（与构造相反）
1. 执行派生类析构函数
2. 执行子对象析构函数
3. 执行基类析构函数
```

#### 3.3 基类构造函数调用注意

1. **基类有缺省构造函数** → 可不显式调用，自动调用
2. **基类仅有有参构造函数** → 派生类**必须**显式调用
3. 派生类构造函数的参数列表中需包含基类构造函数的参数

#### 3.4 包含子对象的派生类构造函数

```cpp
class Student1: public Student {
    Student monitor;  // 子对象
    int age;
    string addr;
public:
    Student1(int n, string nam, int n1, string nam1, int a, string ad):
        Student(n, nam),      // 基类构造函数
        monitor(n1, nam1)     // 子对象构造函数
    {
        age = a;
        addr = ad;
    }
};
```

---

### 4. 多重继承

#### 4.1 概念

一个派生类有两个或多个基类：
```cpp
class D: public A, private B, protected C {
    // D新增加的成员
};
```

#### 4.2 多重继承派生类的构造函数

```cpp
class C: public A, private B {
    float Volume;
public:
    C(float a, float b, float c, float d): A(a, b, c), B(d) {
        Volume = Area() * GetHigh();
    }
};
```

#### 4.3 构造函数的执行顺序

**按声明派生类时基类出现的顺序**，而非构造函数参数表中的顺序！

```cpp
class Derived: public Base2, public Base1  // 先Base2后Base1
{
public:
    Derived(int a, int b): Base1(a), Base2(20) { }  // 按声明顺序调用
};
// 执行顺序：Base2构造函数 → Base1构造函数 → Derived构造函数
```

---

### 5. 虚基类（待补充）

---

### 6. 虚函数与多态（待补充）

---

### 7. 纯虚函数（待补充）

---

### 重点回顾

1. **三种派生方式**对基类成员访问属性的影响
2. **派生类构造函数**的格式和调用顺序
3. **多重继承**的声明方式和构造顺序
4. 构造函数和析构函数**不能被继承**

---

*笔记基于王文博老师课件整理，2026年5月*

### 附录B：课件补充 - 继承与多态（续）

> 东南大学 C++ 课件笔记 | 王文博 2026

---

1. [派生类的应用讨论 - 赋值兼容](#5-派生类的应用讨论)
2. [继承与组合（聚合）](#6-继承与组合聚合)
3. [派生类与模板](#7-派生类与模板)
4. [多态性与虚函数](#8-多态性与虚函数)
5. [纯虚函数与抽象类](#9-纯虚函数与抽象类)
6. [虚函数应用示例 - 单链表](#10-虚函数应用示例---单链表)

---

### 5. 派生类的应用讨论

##### 6.6.1 赋值兼容规则

公用派生类完整地继承了基类的功能，可将公有派生类的值赋给基类对象。

**四个具体表现：**

1. **派生类对象可向基类对象赋值**
   ```cpp
   Student a1;        // 基类对象
   Student1 b1;       // 公有派生类对象
   a1 = b1;           // 合法，舍弃派生类自己的成员
   ```
   > ⚠️ 单向不可逆：基类对象不包含派生类的成员

2. **派生类对象可替代基类对象的引用**
   ```cpp
   A a1;
   B b1;
   A& r = b1;         // r是b1中基类部分的别名
   ```

3. **函数参数为基类对象或引用时，实参可以是派生类对象**
   ```cpp
   void fun(A& r) { cout << r.num << endl; }
   fun(b1);            // 合法
   ```

4. **指向基类的指针可指向派生类对象**
   ```cpp
   Student *pt = &grad1;  // 合法
   ```
   > 只能访问从基类继承来的成员

##### 6.6.2 赋值兼容规则与复制构造函数

```cpp
// 基类复制构造函数
Person::Person(Person &ps) { /* 复制基类成员 */ }

// 派生类复制构造函数
Student::Student(Student &Std): Person(Std) {
    // 按赋值兼容规则，Std可为Person实参
    NoStudent = Std.NoStudent;
    // 复制其他成员...
}

// 派生类赋值操作符
Student& Student::operator=(Student &Std) {
    Person::operator=(Std);  // 调用基类赋值操作符
    NoStudent = Std.NoStudent;
    // ...
}
```

---

### 6. 继承与组合（聚合）

##### 6.7.1 概念

```cpp
class BirthDate { /* 生日类 */ };

class Teacher {
private:
    BirthDate birthday;  // 组合：对象作为数据成员
};

class Professor: public Teacher {
    // 继承：从Teacher得到num, name, sex等
    // 组合：从BirthDate得到year, month, day
};
```

##### 6.7.2 继承 vs 组合

| 维度 | 继承 | 组合 |
|------|------|------|
| 方向 | 纵向 | 横向 |
| 访问方式 | 直接使用基类成员 | `对象名.成员` |
| 基类数量 | 每个类只能继承一次 | 可以有多个对象成员 |

---

### 7. 派生类与模板

| | 类模板 | 派生类 |
|---|---|---|
| **共同点** | 代码复用、程序通用性 | |
| **区别** | 相互独立，未使用继承，追求运行效率 | 一般由简到繁，追求编程效率 |

---

### 8. 多态性与虚函数

#### 8.1 什么是多态性？

> 一个接口，多种方法：具有不同功能的函数可以用同一个函数名

#### 8.2 分类

| 类型 | 时机 | 示例 |
|------|------|------|
| **编译时多态** | 程序执行前确定 | 函数重载、运算符重载 |
| **运行时多态** | 执行过程中动态确定 | 虚函数 |

#### 8.3 虚函数

**定义格式：**
```cpp
virtual 返回类型 函数名(参数表);
```

**特点：**
- 虚函数必须是类的成员函数
- 不能是友元函数或静态成员函数
- 派生类中重定义时，函数名、返回类型、参数必须相同
- 一旦成为虚函数，所有派生类中始终保持虚函数特征

#### 8.4 虚函数示例

```cpp
class CShape {
public:
    virtual void display() { cout << "Shape\n"; }
};

class CCircle : public CShape {
public:
    void display() { cout << "Circle\n"; }  // 自动是虚函数
};

int main() {
    CShape *pShapeArray[3] = { &Shape, &Ellipse, &Circle };
    for(int i = 0; i < 3; i++)
        pShapeArray[i]->display();  // 运行时多态
}
```

#### 8.5 虚析构函数

**问题：** 通过基类指针删除派生类对象时，只调用基类析构函数

```cpp
class A {
public:
    virtual ~A() { cout << "A析构\n"; }  // 虚析构函数
};

class B : public A {
    int *p;
public:
    ~B() { delete p; cout << "B析构\n"; }
};

A *a = new B;
delete a;  // 调用B和A的析构函数
```

> 💡 **建议**：对每个析构函数都声明为virtual

---

### 9. 纯虚函数与抽象类

#### 9.1 纯虚函数

**定义格式：**
```cpp
virtual 返回类型 函数名(参数表) = 0;
```

> `=0` 不表示返回值为0，只是标识纯虚函数的标记

#### 9.2 抽象类

**定义：** 包含至少一个纯虚函数的类

**特点：**
- ❌ 不能定义对象
- ✅ 可以定义指针或引用
- 如果派生类没有重定义所有纯虚函数，仍然是抽象类

**示例：**
```cpp
class Shape {
public:
    virtual float area() = 0;      // 纯虚函数
    virtual float volume() = 0;    // 纯虚函数
    virtual void show() = 0;       // 纯虚函数
};

class Circle : public Shape {
    float radius;
public:
    float area() { return 3.14 * radius * radius; }
    void show() { /* ... */ }
};
```

#### 9.3 抽象类应用示例

```cpp
class Point : public Shape {
protected:
    float x, y;
public:
    float area() { return 0; }
    float volume() { return 0; }
    void show() { cout << "(" << x << "," << y << ")"; }
};

class Circle : public Point {
    float radius;
public:
    Circle(float x, float y, float r) : Point(x, y), radius(r) {}
    float area() { return 3.14 * radius * radius; }
};

class Cylinder : public Circle {
    float height;
public:
    Cylinder(float x, float y, float r, float h) : Circle(x, y, r), height(h) {}
    float area() { return 2 * 3.14 * radius * (radius + height); }
    float volume() { return area() * height / 2; }
};

int main() {
    Shape *pt;
    Point p(3.2, 4.5);
    Circle c(2.4, 1.2, 5.6);
    Cylinder cy(3.5, 6.4, 5.2, 10.5);
    
    pt = &p;  pt->show();  cout << pt->area() << endl;
    pt = &c;  pt->show();  cout << pt->area() << endl;
    pt = &cy; pt->show();  cout << pt->area() << endl;
}
```

---

### 10. 虚函数应用示例 - 单链表

#### 核心思想

使用抽象基类 `Object*` 作为数据域类型，通过虚函数实现多态。

```cpp
// 抽象数据类
class Object {
public:
    virtual bool operator>(Object&) = 0;
    virtual bool operator!=(Object&) = 0;
    virtual void Print() = 0;
    virtual ~Object() {}
};

// 字符串数据类
class StringObject : public Object {
    string sptr;
public:
    StringObject(string s) { sptr = s; }
    bool operator>(Object &obj) override {
        StringObject &temp = (StringObject&)obj;
        return sptr > temp.sptr;
    }
    void Print() override { cout << sptr << '\t'; }
};

// 复数数据类
class ComplexObject : public Object {
    float real, imag;
public:
    bool operator>(Object &obj) override {
        ComplexObject &temp = (ComplexObject&)obj;
        return (real*real + imag*imag) > (temp.real*temp.real + temp.imag*temp.imag);
    }
    void Print() override { cout << "(" << real << "," << imag << ")\t"; }
};

// 链表结点
class Node {
    Object *info;  // 指向抽象类
    Node *link;
public:
    void Linkinfo(Object *obj) { info = obj; }
};
```

---

### 重点回顾

| 概念 | 关键点 |
|------|--------|
| **赋值兼容** | 公有派生类可赋给基类对象/引用/指针 |
| **虚函数** | `virtual`，运行时多态 |
| **虚析构函数** | 确保派生类析构函数被调用 |
| **纯虚函数** | `= 0`，定义抽象接口 |
| **抽象类** | 不能实例化，用于设计层次结构 |

---

*笔记基于王文博老师课件整理，2026年5月*

## 9. 本章小结

- 继承表达 `is-a`，组合表达 `has-a`。
- public 继承最常用。
- 构造先基类后派生，析构相反。
- 基类指针/引用配合虚函数实现多态。
- 抽象类定义接口，派生类实现细节。
- 多态基类一定注意虚析构函数。

## 📚 练习题

## 🔍 常见问题

[返回主目录](../README.md)

[返回主目录](../README.md)
