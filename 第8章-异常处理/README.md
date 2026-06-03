# 第8章 异常处理

> 本章目标：理解程序出错时如何有秩序地处理。异常不是为了制造戏剧性，而是为了别让程序一摔就散架。

## 目录

1. 异常是什么
2. try、catch、throw
3. 多个 catch 与异常类型
4. 标准异常类
5. 自定义异常
6. noexcept 与析构函数
7. 使用建议
8. 本章小结

## 1. 异常是什么

异常表示程序遇到了正常流程处理不了的问题，比如文件打不开、下标越界、内存申请失败。

## 2. try、catch、throw

```cpp
try {
    if (denominator == 0) {
        throw runtime_error("除数不能为0");
    }
    cout << numerator / denominator;
} catch (const runtime_error& e) {
    cout << "错误: " << e.what();
}
```

- `throw` 抛出异常。
- `try` 包住可能出错的代码。
- `catch` 负责接住并处理异常。

## 3. 多个 catch 与异常类型

catch 从上往下匹配，具体类型放前面，通用类型放后面。

```cpp
try {
    // ...
} catch (const invalid_argument& e) {
    cout << e.what();
} catch (const exception& e) {
    cout << e.what();
}
```

不要一上来就 `catch (...)`，那像“我知道出事了，但不知道谁干的”。

## 4. 标准异常类

| 类型 | 说明 |
| --- | --- |
| `exception` | 标准异常基类 |
| `runtime_error` | 运行时错误 |
| `invalid_argument` | 参数不合法 |
| `out_of_range` | 越界 |
| `bad_alloc` | 内存分配失败 |

## 5. 自定义异常

```cpp
class ScoreError : public runtime_error {
public:
    ScoreError() : runtime_error("成绩必须在0到100之间") {}
};
```

多数情况下，直接用标准异常已经够用。自定义异常适合业务含义很明确的时候。

## 6. noexcept 与析构函数

`noexcept` 表示函数承诺不抛异常。

```cpp
void close() noexcept;
```

析构函数中尽量不要抛异常。对象销毁时再扔异常，就像收拾桌子时把桌子也掀了，现场会更难处理。

## 7. 使用建议

- 对无法在本层解决的问题使用异常。
- 对普通判断使用 `if`，不要用异常代替流程控制。
- 抛出有意义的错误信息。
- 捕获异常时优先用 `const 引用`。

## 8. 本章小结

- 异常用于处理非正常情况。
- `throw` 抛，`try` 包，`catch` 接。
- catch 顺序要从具体到一般。
- 析构函数里不要随便抛异常。
- 异常处理的目标是让程序有秩序地失败或恢复。

[返回主目录](../README.md)