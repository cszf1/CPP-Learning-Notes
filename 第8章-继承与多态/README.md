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

继承表示 `is-a` 关系。

```cpp
class Person {
public:
    string name;
};

class Student : public Person {
public:
    int score;
};
```

`Student` 是一种 `Person`，所以能复用 `Person` 的成员。

## 2. 继承方式

| 基类成员 | public 继承 | protected 继承 | private 继承 |
| --- | --- | --- | --- |
| public | public | protected | private |
| protected | protected | protected | private |
| private | 不可直接访问 | 不可直接访问 | 不可直接访问 |

最常用的是 public 继承。private 继承更像“借用实现”，初学时别乱用。

## 3. 派生类构造与析构

构造顺序：

1. 基类构造函数
2. 成员对象构造函数
3. 派生类构造函数体

析构顺序反过来。

```cpp
class Derived : public Base {
public:
    Derived(int x) : Base(x) {}
};
```

基类没有默认构造函数时，派生类必须在初始化列表里显式调用基类构造函数。

## 4. 赋值兼容与对象切片

public 继承下，派生类对象可以赋给基类对象、绑定到基类引用、让基类指针指向它。

```cpp
Student s;
Person p = s;      // 对象切片
Person& r = s;     // 保留真实对象
Person* ptr = &s;
```

对象切片会丢掉派生类新增部分。像把一张完整学生证只剪下姓名栏，信息当然少了。

## 5. 多重继承与虚基类

多重继承：

```cpp
class C : public A, public B {};
```

菱形继承问题：

```cpp
class Student : virtual public Person {};
class Teacher : virtual public Person {};
class Assistant : public Student, public Teacher {};
```

`virtual public` 让共同基类只保留一份，避免“一个人带两张身份证”。

## 6. 虚函数与运行时多态

```cpp
class Shape {
public:
    virtual double area() const { return 0; }
};

class Circle : public Shape {
    double r;
public:
    Circle(double r) : r(r) {}
    double area() const override { return 3.14 * r * r; }
};
```

通过基类指针或引用调用虚函数时，会根据对象真实类型决定调用哪个版本。

```cpp
Shape* p = new Circle(2);
cout << p->area();
delete p;
```

## 7. 纯虚函数与抽象类

```cpp
class Shape {
public:
    virtual double area() const = 0;
};
```

含纯虚函数的类是抽象类，不能直接创建对象。它像“合同模板”，派生类要把具体条款填完才能上岗。

## 8. 虚析构函数

多态基类的析构函数应为虚函数。

```cpp
class Base {
public:
    virtual ~Base() = default;
};
```

否则用基类指针删除派生类对象时，派生类析构函数可能不执行，资源清理会漏。

## 9. 本章小结

- 继承表达 `is-a`，组合表达 `has-a`。
- public 继承最常用。
- 构造先基类后派生，析构相反。
- 基类指针/引用配合虚函数实现多态。
- 抽象类定义接口，派生类实现细节。
- 多态基类一定注意虚析构函数。

[返回主目录](../README.md)