# 第3章 模板与泛型

> 本章目标：学会写“换一种类型也能用”的代码。模板像模具，换材料还能压出同样形状。

## 目录

1. 为什么需要模板
2. 函数模板
3. 类模板
4. 模板特化与默认参数
5. 常见错误
6. 本章小结

## 1. 为什么需要模板

如果没有模板，交换两个 `int`、两个 `double`、两个 `string` 可能要写三遍函数。模板能把“类型”变成参数。

```cpp
template <typename T>
void mySwap(T& a, T& b) {
    T temp = a;
    a = b;
    b = temp;
}
```

`T` 不是神秘符号，只是“先占个座，等调用时再换成真正类型”。

## 2. 函数模板

```cpp
template <typename T>
T maxValue(T a, T b) {
    return a > b ? a : b;
}
```

如果参数类型不同，编译器可能推断失败：

```cpp
cout << maxValue<double>(3, 4.5);
```

## 3. 类模板

类模板常用于容器，比如“存放任意类型的一组数据”。

```cpp
template <typename T>
class Box {
    T value;
public:
    Box(T v) : value(v) {}
    T get() const { return value; }
};

Box<int> a(10);
Box<string> b("hello");
```

## 4. 模板特化与默认参数

```cpp
template <typename T = int>
class Array {
    // ...
};
```

特化用于“某个类型需要特殊处理”。模板是通用方案，特化是“这位同学情况特殊，单独安排”。

## 5. 常见错误

- 模板定义通常放在头文件中，因为编译器需要看到完整定义才能生成代码。
- `typename` 和 `class` 在模板参数里大多数时候等价。
- 模板不是运行时魔法，而是编译期生成代码。
- 错误信息可能很长，别慌，先找自己写的那一行。

## 6. 本章小结

- 函数模板解决函数逻辑重复。
- 类模板解决类型可变的数据结构。
- 模板让代码更通用，但也会让编译错误更啰嗦。
- 能用标准库模板时，优先用标准库，比如 `vector<T>`。

[返回主目录](../README.md)