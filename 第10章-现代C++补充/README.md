# 第10章 现代 C++ 补充

> 本章目标：把常用现代 C++ 工具串起来。它们不是炫技，是为了少写重复代码、少手动踩坑。

## 目录

1. STL 容器
2. 迭代器与算法
3. string
4. 异常处理
5. 命名空间
6. 智能指针
7. auto、范围 for、lambda
8. 本章小结

## 1. STL 容器

| 容器 | 特点 |
| --- | --- |
| `vector` | 连续存储，默认首选 |
| `list` | 双向链表，插入删除方便 |
| `deque` | 双端队列 |
| `set` | 有序、不重复 |
| `map` | 键值对 |

```cpp
vector<int> v = {3, 1, 2};
map<string, int> score;
score["Alice"] = 95;
```

## 2. 迭代器与算法

迭代器连接容器和算法。

```cpp
sort(v.begin(), v.end());
auto it = find(v.begin(), v.end(), 3);
```

常用算法在 `<algorithm>`，求和 `accumulate` 在 `<numeric>`。

```cpp
int sum = accumulate(v.begin(), v.end(), 0);
```

## 3. string

```cpp
string s = "hello";
s += " world";
cout << s.size();
cout << s.substr(0, 5);
```

`string` 比字符数组更安全。能用 `string` 时，别非要手动管理 `char[]`，除非题目要求。

## 4. 异常处理

```cpp
try {
    if (b == 0) throw runtime_error("除数不能为0");
    cout << a / b;
} catch (const exception& e) {
    cerr << e.what() << endl;
}
```

异常用于处理非正常情况，不要拿异常代替普通 `if`。否则程序像动不动就拉警报的宿舍门禁。

## 5. 命名空间

```cpp
namespace math {
    int add(int a, int b) { return a + b; }
}

cout << math::add(1, 2);
```

命名空间避免名字冲突。小程序可以 `using namespace std;`，大项目更推荐写 `std::cout`。

## 6. 智能指针

```cpp
#include <memory>

auto p = make_unique<int>(10);
```

| 类型 | 用法 |
| --- | --- |
| `unique_ptr` | 独占资源，优先使用 |
| `shared_ptr` | 多处共享资源 |
| `weak_ptr` | 观察 shared_ptr，避免循环引用 |

现代 C++ 中，能不用裸 `new/delete` 就尽量不用。

## 7. auto、范围 for、lambda

```cpp
auto x = 10;

for (const auto& item : v) {
    cout << item << endl;
}

sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});
```

`auto` 适合类型明显或很长的时候。别把所有类型都藏起来，代码不是猜谜游戏。

## 8. 本章小结

- STL 是工具箱，优先用成熟工具。
- 迭代器让算法能作用于不同容器。
- `string` 比 C 字符串更友好。
- 异常处理错误，不处理懒惰。
- 智能指针负责资源释放。
- lambda 适合短小临时逻辑。

[返回主目录](../README.md)