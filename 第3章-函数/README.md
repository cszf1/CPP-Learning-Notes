# 第3章 函数

> 本章目标：把程序拆成小块。函数就像收纳盒，东西分类放，找起来不至于翻遍全屋。

## 目录

1. 函数声明、定义与调用
2. 参数传递
3. 函数返回值
4. 默认参数
5. 函数重载
6. 内联函数
7. 作用域与生存期
8. 递归
9. 本章小结

## 1. 函数声明、定义与调用

```cpp
int add(int a, int b); // 声明

int add(int a, int b) { // 定义
    return a + b;
}

int main() {
    cout << add(1, 2);
}
```

声明告诉编译器“有这个函数”，定义告诉编译器“它具体怎么干活”。

## 2. 参数传递

值传递：传副本，函数里改不到外面。

```cpp
void f(int x) { x = 10; }
```

引用传递：传别名，能改原变量。

```cpp
void swapInt(int& a, int& b) {
    int t = a;
    a = b;
    b = t;
}
```

指针参数：传地址，也能改原变量。

```cpp
void setZero(int* p) {
    if (p != nullptr) *p = 0;
}
```

大对象只读时用 `const 引用`：

```cpp
void printName(const string& name);
```

## 3. 函数返回值

函数可以返回计算结果：

```cpp
double area(double r) {
    return 3.14159 * r * r;
}
```

不要返回局部变量的引用或指针。局部变量出了函数就下班了，你还拿它工牌刷门禁，当然出问题。

## 4. 默认参数

```cpp
void printLine(char ch = '-', int n = 20) {
    for (int i = 0; i < n; ++i) cout << ch;
}
```

默认参数通常写在函数声明处，且从右往左设置。

## 5. 函数重载

同名函数，参数列表不同。

```cpp
int maxValue(int a, int b);
double maxValue(double a, double b);
```

重载看参数类型、个数、顺序，不只看返回值。

## 6. 内联函数

`inline` 建议编译器把函数调用展开，适合短小函数。

```cpp
inline int square(int x) { return x * x; }
```

别把大函数写成内联。把整本书塞进便利贴，不叫高效，叫为难便利贴。

## 7. 作用域与生存期

- 局部变量：函数或代码块内有效。
- 全局变量：整个文件可见，但要少用。
- 静态局部变量：只初始化一次，函数结束后值仍保留。

```cpp
void count() {
    static int n = 0;
    cout << ++n << endl;
}
```

## 8. 递归

函数自己调用自己，必须有结束条件。

```cpp
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

递归像俄罗斯套娃，一层套一层；没有出口就不是套娃，是事故。

## 9. 本章小结

- 函数让程序更清晰、更容易复用。
- 参数传递要分清值、引用、指针。
- 默认参数从右往左设置。
- 重载不能只靠返回值区分。
- 递归必须有边界条件。

[返回主目录](../README.md)