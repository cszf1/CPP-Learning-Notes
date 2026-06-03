# 第5章 继承与派生

> 本章目标：理解类之间如何复用代码，以及什么时候不该用继承。继承不是万能胶，粘错地方会越粘越乱。

## 目录

1. 继承的基本概念
2. 三种继承方式
3. 派生类构造与析构
4. 赋值兼容规则
5. 多重继承与虚基类
6. 继承和组合
7. 本章小结

## 1. 继承的基本概念

继承表达的是 `is-a` 关系：学生是人，圆是图形。

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

## 2. 三种继承方式

| 基类成员 | public 继承 | protected 继承 | private 继承 |
| --- | --- | --- | --- |
| public | public | protected | private |
| protected | protected | protected | private |
| private | 不可直接访问 | 不可直接访问 | 不可直接访问 |

最常用的是 `public` 继承。它表示“派生类仍然是基类的一种”。

## 3. 派生类构造与析构

构造顺序：先基类，再成员对象，最后派生类自己。析构顺序反过来。

```cpp
class Base {
public:
    Base() { cout << "Base\n"; }
    ~Base() { cout << "~Base\n"; }
};

class Derived : public Base {
public:
    Derived() { cout << "Derived\n"; }
    ~Derived() { cout << "~Derived\n"; }
};
```

输出顺序像进教室：长辈先进，晚辈后进；离开时晚辈先走，长辈最后关灯。

## 4. 赋值兼容规则

公有继承下，派生类对象可以当作基类使用：

```cpp
Student s;
Person& p = s;
Person* ptr = &s;
```

但只能看到基类那部分。把派生类赋给基类对象会发生对象切片：派生类新增成员被切掉。

## 5. 多重继承与虚基类

```cpp
class Student : virtual public Person {};
class Teacher : virtual public Person {};
class TeachingAssistant : public Student, public Teacher {};
```

虚基类可以解决菱形继承中“祖先被复制两份”的问题。

## 6. 继承和组合

继承表示 `is-a`，组合表示 `has-a`。

```cpp
class Engine {};
class Car {
    Engine engine;
};
```

如果只是“拥有一个对象”，优先组合。别让“车继承发动机”，听起来就像方向盘想当车门。

## 7. 本章小结

- 继承用于表达真正的 `is-a` 关系。
- public 继承最常见，private/protected 继承要谨慎。
- 构造顺序：基类先，派生类后；析构相反。
- 公有派生类可以当基类用，但对象切片要注意。
- 多重继承能用，但别把关系网织成一团。

[返回主目录](../README.md)