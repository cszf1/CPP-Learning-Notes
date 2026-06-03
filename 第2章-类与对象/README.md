# 第2章 类与对象

> 本章目标：理解 class 是如何把“数据”和“操作数据的函数”装进一个整体的。

## 目录

1. struct 与 class
2. 类、对象和访问权限
3. 构造函数与析构函数
4. 拷贝构造、赋值与深拷贝
5. this、static、friend
6. 运算符重载
7. 本章小结

## 1. struct 与 class

`struct` 和 `class` 都能定义类型。主要区别是默认访问权限：

| 关键字 | 默认访问权限 |
| --- | --- |
| `struct` | public |
| `class` | private |

可以把 `class` 想成“带门禁的结构体”：不是不给看，而是要通过规定入口。

## 2. 类、对象和访问权限

类是图纸，对象是按图纸造出来的东西。

```cpp
class Student {
private:
    string name;
    int score;
public:
    void setScore(int s) { score = s; }
    int getScore() const { return score; }
};
```

`private` 用来保护内部数据，`public` 提供外部接口。不要把所有成员都 public，那就像给宿舍门装了门锁但钥匙挂在门上。

## 3. 构造函数与析构函数

构造函数负责对象出生，析构函数负责对象离场。

```cpp
class Student {
    string name;
    int score;
public:
    Student(string n, int s) : name(n), score(s) {}
    ~Student() = default;
};
```

成员初始化列表比在函数体里赋值更推荐。

## 4. 拷贝构造、赋值与深拷贝

默认拷贝会逐成员复制。普通成员没问题，遇到动态内存就可能出事。

```cpp
class Buffer {
    int* data;
public:
    Buffer(int n) : data(new int[n]{}) {}
    ~Buffer() { delete[] data; }
};
```

如果直接默认拷贝，两个对象可能指向同一块内存，最后析构时重复释放。像两个人拿同一张电影票进场，检票口会沉默，但问题迟早来。

需要自己写深拷贝，或者更现代地使用 `vector`、`string`、智能指针，让资源自己管理。

## 5. this、static、friend

`this` 是当前对象的指针：

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

`static` 成员属于类，不属于某一个对象。`friend` 能访问私有成员，很方便，但别滥用。朋友太多，隐私就像公开课。

## 6. 运算符重载

```cpp
class Complex {
    double real, imag;
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
};
```

原则：重载应符合直觉。`+` 最好表示相加，不要让它删除文件、清空数组，这属于惊吓式编程。

## 7. 本章小结

- 类是图纸，对象是成品。
- `private` 管状态，`public` 给接口。
- 构造负责初始化，析构负责清理。
- 有动态资源时，要考虑拷贝构造、赋值运算符和析构函数。
- 运算符重载要让代码更好懂，不要炫技。

[返回主目录](../README.md)