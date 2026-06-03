# 第5章 类与对象

> 本章目标：学会用类描述现实问题。类像图纸，对象像按图纸造出来的成品。

## 目录

1. 结构体 struct
2. 类 class 与访问权限
3. 对象、对象数组与对象指针
4. 构造函数
5. 析构函数
6. 复制构造函数与深拷贝
7. this 指针
8. 静态成员
9. 友元
10. 运算符重载
11. 本章小结

## 1. 结构体 struct

结构体把多个数据合成一个整体。

```cpp
struct Student {
    int id;
    string name;
    double score;
};
```

它适合描述“有多个属性的东西”。一个学生不是三个散落的变量，而是一个整体。

## 2. 类 class 与访问权限

```cpp
class Student {
private:
    int id;
    double score;
public:
    void setScore(double s) { score = s; }
    double getScore() const { return score; }
};
```

`class` 默认 private，`struct` 默认 public。private 不是为了神秘，是为了保护对象内部状态。

## 3. 对象、对象数组与对象指针

```cpp
Student s;
Student group[30];
Student* p = &s;
```

访问成员：

- 对象：`s.getScore()`
- 指针：`p->getScore()`

## 4. 构造函数

构造函数负责初始化对象。

```cpp
class Point {
    double x, y;
public:
    Point(double x0 = 0, double y0 = 0) : x(x0), y(y0) {}
};
```

成员初始化列表更推荐，尤其是 `const` 成员、引用成员、对象成员。

## 5. 析构函数

析构函数在对象生命周期结束时自动调用。

```cpp
class FileGuard {
public:
    ~FileGuard() {
        // 清理资源
    }
};
```

如果对象申请了动态内存，析构函数里要释放。资源有借有还，调试才不难。

## 6. 复制构造函数与深拷贝

复制构造函数在“用一个对象创建另一个对象”时调用。

```cpp
class A {
public:
    A(const A& other);
};
```

浅拷贝只复制指针值，两个对象指向同一块内存；深拷贝会重新申请空间并复制内容。

```cpp
class Buffer {
    int* data;
    int n;
public:
    Buffer(int n) : data(new int[n]{}), n(n) {}
    Buffer(const Buffer& other) : data(new int[other.n]), n(other.n) {
        for (int i = 0; i < n; ++i) data[i] = other.data[i];
    }
    ~Buffer() { delete[] data; }
};
```

## 7. this 指针

`this` 指向当前对象。

```cpp
class Counter {
    int value = 0;
public:
    Counter& add() {
        ++value;
        return *this;
    }
};
```

返回 `*this` 可以支持链式调用。

## 8. 静态成员

静态成员属于类，不属于某个对象。

```cpp
class Student {
public:
    static int count;
};
```

适合保存“所有对象共享”的信息，比如对象数量。

## 9. 友元

友元函数可以访问类的私有成员。

```cpp
class Point {
    double x, y;
    friend double distance(const Point& a, const Point& b);
};
```

友元像特别通行证，好用但别发太多，否则门禁系统就成摆设了。

## 10. 运算符重载

```cpp
class Complex {
    double r, i;
public:
    Complex(double r = 0, double i = 0) : r(r), i(i) {}
    Complex operator+(const Complex& other) const {
        return Complex(r + other.r, i + other.i);
    }
};
```

重载应符合直觉。`+` 做相加，`==` 做比较，不要写出让读者需要占卜的代码。

## 11. 本章小结

- 类封装数据和操作。
- 构造函数初始化，析构函数清理。
- 动态资源要考虑深拷贝。
- `this` 表示当前对象。
- 静态成员共享，友元慎用。
- 运算符重载要自然、可读。

[返回主目录](../README.md)