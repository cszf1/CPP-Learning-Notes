# 第10章 命名空间与 C++ 新特性

> 本章目标：认识现代 C++ 常用写法，让代码少一点重复，多一点清爽。新特性不是为了显得高级，而是为了少写容易错的代码。

## 目录

1. 命名空间
2. 智能指针
3. auto
4. 范围 for
5. lambda 表达式
6. 其他常用新特性
7. 本章小结

## 1. 命名空间

命名空间用来避免名字冲突。

```cpp
namespace math {
    int add(int a, int b) { return a + b; }
}

cout << math::add(1, 2);
```

`std` 就是标准库的命名空间。初学可以写 `using namespace std;`，但在大型项目里更推荐写 `std::cout`，避免名字打架。

## 2. 智能指针

智能指针帮你管理动态内存。

```cpp
#include <memory>

auto p = make_unique<int>(10);
```

| 类型 | 含义 |
| --- | --- |
| `unique_ptr` | 独占所有权，不能随便复制 |
| `shared_ptr` | 共享所有权，引用计数 |
| `weak_ptr` | 弱引用，解决 shared_ptr 循环引用 |

优先使用 `unique_ptr`。只有确实需要共享所有权时，再用 `shared_ptr`。

## 3. auto

```cpp
auto x = 10;
auto y = 3.14;
auto it = v.begin();
```

`auto` 适合类型很长或很明显的时候。不要为了省事把所有地方都写 auto，代码不是猜谜节目。

## 4. 范围 for

```cpp
vector<int> nums = {1, 2, 3};

for (int x : nums) {
    cout << x << endl;
}

for (int& x : nums) {
    x *= 2;
}
```

只读大对象时用常量引用：

```cpp
for (const string& name : names) {
    cout << name << endl;
}
```

## 5. lambda 表达式

lambda 是临时小函数，适合和 STL 算法一起用。

```cpp
vector<int> nums = {3, 1, 4, 2};
sort(nums.begin(), nums.end(), [](int a, int b) {
    return a > b;
});
```

捕获列表决定 lambda 能用外部哪些变量。

```cpp
int limit = 10;
auto bigger = [limit](int x) { return x > limit; };
```

## 6. 其他常用新特性

| 特性 | 用途 |
| --- | --- |
| `nullptr` | 空指针，替代 `NULL` |
| `enum class` | 更安全的枚举 |
| `using` | 类型别名，比 `typedef` 更清楚 |
| `constexpr` | 编译期常量或计算 |
| 结构化绑定 | 一次拆多个值 |

```cpp
auto [key, value] = pair<string, int>{"age", 18};
```

## 7. 本章小结

- 命名空间解决名字冲突。
- 智能指针减少手动 `new/delete`。
- `auto` 能简化长类型，但别滥用。
- 范围 for 让遍历更自然。
- lambda 适合写短小的临时逻辑。
- 现代 C++ 的方向是：少写重复代码，少犯低级错误。

[返回主目录](../README.md)