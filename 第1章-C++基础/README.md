# 第1章 C++基础回顾

> 📚 **重点章节** - 面向对象编程的地基，零基础也能看懂！

---

## 📋 目录

1. [数据类型](#1-数据类型)
2. [变量与常量](#2-变量与常量)
3. [运算符与表达式](#3-运算符与表达式)
4. [控制结构](#4-控制结构)
5. [数组与指针](#5-数组与指针)
6. [函数基础](#6-函数基础)
7. [本章小结](#7-本章小结)

---

## 1. 数据类型

### 1.1 什么是数据类型？

**通俗理解**：数据类型就是告诉计算机"这是什么种类的东西"。

> 就像超市里的商品需要分类：
> - 水果区放水果（不能放冰箱的衣服）
> - 服装区放衣服
> - 每种商品都有自己的"类型"
>
> 程序中的数据也一样：
> - 整数就是整数（int）
> - 小数就是小数（float/double）
> - 字符就是字符（char）

**为什么需要数据类型？**
1. 告诉计算机分配多少内存
2. 告诉计算机如何解释这块内存
3. 防止我们把"苹果"当"香蕉"用

### 1.2 基本数据类型

C++的基本数据类型可以分为四大类：

| 类型 | 关键字 | 占用空间 | 取值范围 | 用途 |
|------|--------|----------|----------|------|
| **整型** | `int` | 4字节 | -21亿 ~ 21亿 | 存储整数 |
| 短整型 | `short` | 2字节 | -3万 ~ 3万 | 小范围整数 |
| 长整型 | `long` | 4/8字节 | 更大范围 | 大整数 |
| 长长整型 | `long long` | 8字节 | 非常大 | 极大整数 |
| **浮点型** | `float` | 4字节 | ±3.4e38 | 单精度小数 |
| 双精度 | `double` | 8字节 | ±1.7e308 | 双精度小数 |
| **字符型** | `char` | 1字节 | -128 ~ 127 | 存储单个字符 |
| **布尔型** | `bool` | 1字节 | true/false | 真/假判断 |

```cpp
#include <iostream>
#include <climits>  // 查看数据类型范围
#include <cfloat>   // 查看浮点型范围
using namespace std;

int main() {
    // 整型家族
    int age = 20;                    // 最常用的整数类型
    short score = 90;                // 小范围整数
    long population = 1400000000L;   // 大整数（记得加L后缀）
    long long atoms = 1000000000000000000LL;  // 极大整数（加LL）
    
    // 浮点型家族
    float pi_float = 3.14f;          // 单精度（加f后缀）
    double pi_double = 3.141592653;  // 双精度（更精确）
    long double pi_ld = 3.14159265358979L;  // 长双精度
    
    // 字符型
    char grade = 'A';                // 用单引号
    char digit = '7';                // 字符'7'不是整数7
    
    // 布尔型
    bool isPassed = true;            // true = 1
    bool hasError = false;           // false = 0
    
    // 输出验证
    cout << "年龄: " << age << endl;
    cout << "圆周率(双精度): " << pi_double << endl;
    cout << "等级: " << grade << endl;
    cout << "是否通过: " << isPassed << endl;
    
    // 查看数据类型大小
    cout << "\n=== 数据类型大小 ===" << endl;
    cout << "int: " << sizeof(int) << " 字节" << endl;
    cout << "float: " << sizeof(float) << " 字节" << endl;
    cout << "double: " << sizeof(double) << " 字节" << endl;
    cout << "char: " << sizeof(char) << " 字节" << endl;
    cout << "bool: " << sizeof(bool) << " 字节" << endl;
    
    return 0;
}
```

### 1.3 无符号类型

有些数据类型可以加 `unsigned` 修饰，表示"只能是非负数"：

```cpp
#include <iostream>
using namespace std;

int main() {
    // 有符号 vs 无符号
    int signed_num = -10;           // 可以是负数：-21亿 ~ 21亿
    unsigned int unsigned_num = 10;  // 只能是正数：0 ~ 42亿
    
    // 为什么用无符号？
    // 当你知道一个数不会是负数时，用无符号可以存储更大的正数
    unsigned int positive_only = 4294967295;  // 比 int 最大值大
    
    // 字符也可以是无符号
    unsigned char uchar = 255;      // 0 ~ 255
    signed char schar = -128;       // -128 ~ 127
    
    cout << "有符号int最大值: " << INT_MAX << endl;
    cout << "无符号int最大值: " << UINT_MAX << endl;
    
    // ⚠️ 注意：无符号数在某些情况下会产生意外结果
    unsigned int u = 0;
    u--;  // 不会变成-1，而是变成UINT_MAX！
    cout << "无符号数减1后: " << u << endl;
    
    return 0;
}
```

### 1.4 字符型详解

字符型其实本质上是整数（ASCII码）：

```cpp
#include <iostream>
using namespace std;

int main() {
    char c1 = 'A';
    char c2 = 65;                   // 'A'的ASCII码就是65
    
    cout << "c1 = " << c1 << endl;  // 输出: A
    cout << "c2 = " << c2 << endl;  // 输出: A（因为ASCII码65就是'A'）
    cout << "(int)c1 = " << (int)c1 << endl;  // 65
    
    // 大小写转换
    char lower = 'a';
    char upper = 'A';
    cout << "'a'-'A' = " << lower - upper << endl;  // 32
    
    // 常见ASCII码
    cout << "'0'-'9': " << '0' << " ~ " << '9' << endl;  // 48 ~ 57
    cout << "'A'-'Z': " << 'A' << " ~ " << 'Z' << endl;  // 65 ~ 90
    cout << "'a'-'z': " << 'a' << " ~ " << 'z' << endl;  // 97 ~ 122
    
    return 0;
}
```

### 1.5 类型转换

C++中的类型转换分为两种：**隐式转换**和**显式转换**。

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 隐式转换（自动发生）
    int a = 3;
    double b = 4.5;
    double c = a + b;  // int自动转为double，结果是7.5
    
    // 隐式转换规则：
    // 小类型 → 大类型：安全，不会丢失数据
    // 大类型 → 小类型：可能丢失数据，编译器警告
    int i = 100;
    char ch = i;       // 可能丢失数据，危险！
    
    // 2. 显式转换（强制转换）
    double x = 3.99;
    int y = (int)x;    // 强制转int，结果是3（截断，不是四舍五入）
    cout << "(int)3.99 = " << y << endl;  // 3
    
    // C++风格的类型转换（推荐）
    double d = 3.7;
    int i1 = static_cast<int>(d);  // 3
    char* p = reinterpret_cast<char*>(&d);  // 重新解释内存
    
    // 3. 常见的转换场景
    // 字符串转数字
    int num = stoi("123");        // "123" → 123
    double pi = stod("3.14");    // "3.14" → 3.14
    
    // 数字转字符串
    string s1 = to_string(123);   // 123 → "123"
    string s2 = to_string(3.14); // 3.14 → "3.14"
    
    cout << "stoi: " << num << endl;
    cout << "to_string: " << s1 << endl;
    
    return 0;
}
```

### 1.6 💡 通俗总结

| 数据类型 | 一句话理解 | 典型用途 |
|---------|-----------|---------|
| `int` | 最常用的整数 | 年龄、成绩、数量 |
| `double` | 常用的小数 | 身高、体重、pi |
| `char` | 单个字符 | 字母、符号 |
| `bool` | 是/否判断 | 条件标志 |

> **记忆技巧**：
> - 表示"个数"用 `int`
> - 表示"精确值"用 `double`
> - 表示"单个字符"用 `char`
> - 表示"真假"用 `bool`

### 1.7 常见错误

```cpp
// ❌ 错误1：变量类型选择不当
int price = 9.99;          // 丢失小数部分，结果是9
double rate = 5;            // 可以，但最好用int

// ❌ 错误2：超出数据类型范围
int big = 2147483648;      // 超出int最大值，溢出！
unsigned int u_big = 4294967296;  // 超出unsigned int最大值，溢出！

// ❌ 错误3：混淆字符和字符串
char c = "A";              // ❌ "A"是字符串，不是char
char c = 'A';              // ✅ 用单引号

// ❌ 错误4：忘记后缀
float f = 3.14;            // ⚠️ C++中3.14是double，不是float
float f = 3.14f;           // ✅ 加f后缀才是float
```

### 1.8 练习

```cpp
// 练习1：判断下列代码的输出
int a = 5, b = 2;
cout << a / b << endl;         // 提示：两个整数相除
cout << (double)a / b << endl;  // 提示：一个转成double

// 练习2：计算下面各变量的值
char c = 'C';
int i = c;
cout << i << endl;              // ASCII码

// 练习3：选择合适的数据类型存储以下信息
// - 你的年龄
// - 圆周率（保留6位小数）
// - 是否在上课
// - 你的学号（可能很长）
```

---

## 2. 变量与常量

### 2.1 什么是变量？

**通俗理解**：变量就是程序中的"储物盒"，每个盒子都有自己的名字，里面可以放东西。

> 想象你的抽屉：
> - 抽屉1：放袜子（int num_socks）
> - 抽屉2：放书（string my_book）
> - 抽屉3：放钱（double money）
>
> 每个抽屉（变量）都有：
> - **名字**（叫什么）：方便找到
> - **类型**（放什么）：只能放指定类型的东西
> - **地址**（在哪）：内存中的位置
> - **值**（里面装的）：实际的数据

### 2.2 变量的定义与初始化

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 先声明，后赋值
    int age;
    age = 20;  // 第一次赋值叫"初始化"
    
    // 2. 声明时直接初始化（推荐）
    int score = 95;
    
    // 3. 拷贝初始化
    int a = 100;
    int b = a;    // 用a的值初始化b
    
    // 4. 列表初始化（现代C++推荐）
    int x{10};           // 列表初始化
    int y = {20};        // 也可以加=号
    int z{};             // 默认初始化为0
    
    // 5. ⚠️ 未初始化的变量
    int uninit;          // 随机值！危险！
    cout << "未初始化变量: " << uninit << endl;  // 垃圾值
    
    // 6. 常量定义
    const int MAX_STUDENTS = 50;    // 班级最多50人
    const double PI = 3.14159;
    const string SCHOOL_NAME = "电子科技大学";
    
    // MAX_STUDENTS = 60;  // ❌ 错误！常量不能修改
    
    cout << "年龄: " << age << endl;
    cout << "成绩: " << score << endl;
    cout << "PI: " << PI << endl;
    
    return 0;
}
```

### 2.3 变量命名规范

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // ✅ 合法的变量名
    int age = 20;              // 简单明了
    int studentAge = 20;       // 驼峰命名法（第二个单词首字母大写）
    int student_age = 20;      // 下划线命名法
    int _private = 1;          // 可以用下划线开头
    int totalScore = 100;      // 表达含义
    int i, j, k;               // 循环变量常用单个字母
    
    // ❌ 非法的变量名
    // int 1stPlace;            // 不能以数字开头
    // int my-score;            // 不能用连字符
    // int class;               // 不能用关键字
    
    // ⚠️ C++关键字（保留字）
    // int, float, double, char, if, else, for, while, return...
    // 这些词有特殊含义，不能用作变量名
    
    // 📝 命名建议
    int numberOfStudents = 45;  // ✅ 清晰表达含义
    int n = 45;                 // ⚠️ 除非是循环变量，否则不推荐
    
    // 常见命名风格
    int maxValue;               // 驼峰法（Java风格）
    int max_value;              // 下划线法（Python/C风格）
    int MAX_VALUE;              // 全大写（常量专用）
    
    return 0;
}
```

### 2.4 const 与 #define

**两种定义常量的方式**：

```cpp
#include <iostream>
using namespace std;

// 方式1：#define 宏定义（预处理阶段替换）
#define PI 3.14159
#define MAX_SIZE 100

// 方式2：const 常量（编译阶段检查，更安全）
const double GRAVITY = 9.8;
const int BUFFER_SIZE = 1024;

int main() {
    cout << "PI = " << PI << endl;
    cout << "重力加速度 = " << GRAVITY << endl;
    
    // ⚠️ #define 的问题
    // #define 没有类型检查
    // #define 容易被意外替换
    #define TABLE_SIZE 100
    int TABLE[TABLE_SIZE];  // 正常
    
    // const 的优势
    const int SIZE = 100;
    // const 有类型检查，编译器会帮你把关
    // const 遵循作用域规则，更安全
    // const 可以用于更复杂的数据
    
    // 位置1
    {
        const int LOCAL_CONST = 50;  // 只在这个大括号内有效
        cout << LOCAL_CONST << endl;
    }
    // cout << LOCAL_CONST << endl;  // ❌ 超出作用域，编译错误
    
    return 0;
}

// ⚠️ #define 没有作用域限制
void func() {
    #define OUTER 100  // OUTER 在整个文件中都有效
}
// 这可能导致意外的宏替换

// ✅ 推荐用 const 替代 #define
void func2() {
    const int INNER = 200;  // 只在这个函数内有效
}
```

### 2.5 常量的最佳实践

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 用 const 替代 #define
    const int MAX_RETRIES = 3;
    const double TAX_RATE = 0.13;
    
    // 2. 常量表达式（编译时计算）
    constexpr int ARRAY_SIZE = 100;  // 编译时就知道是100
    constexpr double PI = 3.141592653;
    
    // 3. enum 枚举类型（相关的常量）
    enum Color {
        RED = 0,      // 默认从0开始
        GREEN = 1,
        BLUE = 2,
        YELLOW = 3
    };
    
    Color favorite = GREEN;
    cout << "我喜欢的颜色编号: " << favorite << endl;
    
    // 枚举值自增
    enum Weekday {
        MONDAY,    // 0
        TUESDAY,   // 1
        WEDNESDAY, // 2
        THURSDAY,  // 3
        FRIDAY,    // 4
        SATURDAY,  // 5
        SUNDAY     // 6
    };
    
    Weekday today = FRIDAY;
    if (today == FRIDAY) {
        cout << "今天是周五！" << endl;
    }
    
    // 4. enum class（强类型枚举，更安全）
    enum class ErrorCode {
        SUCCESS = 0,
        FILE_NOT_FOUND = 404,
        OUT_OF_MEMORY = 500
    };
    
    ErrorCode err = ErrorCode::SUCCESS;
    // cout << err << endl;  // ❌ 不能直接输出，需要转换
    cout << "错误码: " << static_cast<int>(err) << endl;
    
    return 0;
}
```

### 2.6 💡 通俗理解

> **变量 vs 常量**：
> - **变量**：像你的钱包，里面的钱可以随时存取
> - **常量**：像刻在石头上的字，改不了
>
> **什么时候用常量？**
> - 配置参数（如税率、圆周率）
> - 物理常数（如光速、重力加速度）
> - 程序配置（如窗口大小、最大连接数）

### 2.7 常见错误

```cpp
// ❌ 错误1：忘记初始化
int score;
cout << score;  // 随机值！

// ❌ 错误2：修改常量
const int MAX = 100;
MAX = 200;  // ❌ 编译错误

// ❌ 错误3：变量名重复定义
int x = 10;
int x = 20;  // ❌ 同一个作用域内重复定义

// ❌ 错误4：作用域污染
for (int i = 0; i < 10; i++) { /* ... */ }
int i = 5;  // ⚠️ 这不会报错，但容易混淆

// ❌ 错误5：使用未声明的变量
cout << undeclared;  // ❌ 编译错误
```

### 2.8 练习

```cpp
// 练习1：判断以下变量定义的错误
int 1name;           // 问题在哪？
int my age;          // 问题在哪？
int int;             // 问题在哪？
int my-int;          // 问题在哪？

// 练习2：选择合适的数据类型和变量名
// - 存储一个班级的人数（最多60人）
// - 存储圆周率
// - 存储是否下雨（是/否）
// - 存储一本书的标题

// 练习3：指出以下代码的问题
const double PI = 3.14;
PI = 3.14159;
cout << PI << endl;
```

---

## 3. 运算符与表达式

### 3.1 运算符分类一览

C++中的运算符非常丰富：

```
┌─────────────────────────────────────────────────────────┐
│                      运算符分类                           │
├─────────────────────────────────────────────────────────┤
│  算术运算符:  +  -  *  /  %                              │
│  关系运算符:  <  >  <=  >=  ==  !=                      │
│  逻辑运算符:  &&  ||  !                                  │
│  位运算符:    &  |  ^  ~  <<  >>                        │
│  赋值运算符:  =  +=  -=  *=  /=  %=                     │
│  条件运算符:  ?:                                          │
│  逗号运算符:  ,                                          │
│  sizeof运算符: sizeof                                    │
└─────────────────────────────────────────────────────────┘
```

### 3.2 算术运算符

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 10, b = 3;
    
    // 基本算术运算
    cout << a << " + " << b << " = " << (a + b) << endl;  // 13
    cout << a << " - " << b << " = " << (a - b) << endl;  // 7
    cout << a << " * " << b << " = " << (a * b) << endl;  // 30
    cout << a << " / " << b << " = " << (a / b) << endl;  // 3（整数除法）
    cout << a << " % " << b << " = " << (a % b) << endl;  // 1（取余）
    
    // ⚠️ 整数除法会截断小数部分
    double x = 5, y = 2;
    cout << "5 / 2 = " << (x / y) << endl;    // 2.5
    cout << "5 / 2 = " << (5 / 2) << endl;    // 2（两个整数相除）
    
    // 取余运算的应用
    int num = 37;
    cout << num << " 是" << ((num % 2 == 0) ? "偶数" : "奇数") << endl;
    cout << num << " 能被3整除: " << (num % 3 == 0 ? "是" : "否") << endl;
    
    // 负数取余（了解即可）
    cout << "-10 % 3 = " << (-10 % 3) << endl;   // -1
    cout << "10 % -3 = " << (10 % -3) << endl;   // 1
    
    // 自增自减运算符
    int i = 5;
    cout << "i++ = " << i++ << endl;  // 先用后加：输出5，然后i变成6
    cout << "++i = " << ++i << endl;  // 先加后用：i先变成7，输出7
    
    int j = 5;
    cout << "j-- = " << j-- << endl;  // 先用后减：输出5，然后j变成4
    cout << "--j = " << --j << endl;  // 先减后用：j先变成3，输出3
    
    return 0;
}
```

### 3.3 关系运算符与逻辑运算符

```cpp
#include <iostream>
using namespace std;

int main() {
    // 关系运算符（比较大小）
    int a = 10, b = 20;
    cout << "a == b: " << (a == b) << endl;  // 0（false）
    cout << "a != b: " << (a != b) << endl;  // 1（true）
    cout << "a < b: " << (a < b) << endl;    // 1
    cout << "a > b: " << (a > b) << endl;    // 0
    cout << "a <= b: " << (a <= b) << endl;  // 1
    cout << "a >= b: " << (a >= b) << endl;  // 0
    
    // ⚠️ 注意：== 是判断相等，= 是赋值！
    int x = 5;
    if (x = 10) {  // ⚠️ 这里把10赋给了x，不是判断x是否等于10！
        cout << "x被赋值为10" << endl;  // 这个分支总是执行
    }
    
    // 逻辑运算符
    int age = 25;
    double gpa = 3.5;
    
    // && 逻辑与（两个都真才为真）
    bool pass = (age >= 18) && (gpa >= 3.0);
    cout << "通过审核: " << pass << endl;  // 1
    
    // || 逻辑或（至少一个为真就为真）
    bool scholarship = (gpa >= 3.8) || (age < 20);
    cout << "获得奖学金资格: " << scholarship << endl;
    
    // ! 逻辑非（真假反转）
    bool isWeekend = false;
    cout << "不是周末: " << !isWeekend << endl;  // 1
    
    // 短路求值
    int m = 0, n = 10;
    if (m != 0 && n / m > 5) {  // 如果m==0，第一个条件为false，整体直接为false
        cout << "这种情况不会输出" << endl;      // n/m不会被执行，避免了除零错误
    }
    
    // 逻辑运算的真值表
    cout << "\n=== 逻辑运算真值表 ===" << endl;
    cout << "true && true = " << (true && true) << endl;   // 1
    cout << "true && false = " << (true && false) << endl; // 0
    cout << "false && true = " << (false && true) << endl; // 0
    cout << "false && false = " << (false && false) << endl; // 0
    cout << "true || true = " << (true || true) << endl;   // 1
    cout << "true || false = " << (true || false) << endl;  // 1
    cout << "false || true = " << (false || true) << endl; // 1
    cout << "false || false = " << (false || false) << endl; // 0
    cout << "!true = " << (!true) << endl;  // 0
    cout << "!false = " << (!false) << endl; // 1
    
    return 0;
}
```

### 3.4 赋值运算符

```cpp
#include <iostream>
using namespace std;

int main() {
    // 基本赋值
    int a = 10;
    
    // 复合赋值运算符
    a += 5;    // a = a + 5 = 15
    cout << "a += 5: " << a << endl;
    
    a -= 3;    // a = a - 3 = 12
    cout << "a -= 3: " << a << endl;
    
    a *= 2;    // a = a * 2 = 24
    cout << "a *= 2: " << a << endl;
    
    a /= 4;    // a = a / 4 = 6
    cout << "a /= 4: " << a << endl;
    
    a %= 5;    // a = a % 5 = 1
    cout << "a %= 5: " << a << endl;
    
    // 位运算复合赋值
    int b = 12;  // 二进制: 1100
    b &= 10;     // 1100 & 1010 = 1000 (8)
    cout << "b &= 10: " << b << endl;
    
    b = 12;
    b |= 10;     // 1100 | 1010 = 1110 (14)
    cout << "b |= 10: " << b << endl;
    
    b = 12;
    b ^= 10;     // 1100 ^ 1010 = 0110 (6)
    cout << "b ^= 10: " << b << endl;
    
    // 移位复合赋值
    int c = 3;  // 11
    c <<= 2;    // 1100 (12)
    cout << "c <<= 2: " << c << endl;
    
    c = 12;
    c >>= 2;    // 3
    cout << "c >>= 2: " << c << endl;
    
    // 链式赋值
    int x, y, z;
    x = y = z = 100;  // 从右向左依次赋值
    cout << "x=" << x << " y=" << y << " z=" << z << endl;
    
    return 0;
}
```

### 3.5 条件运算符（三目运算符）

```cpp
#include <iostream>
using namespace std;

int main() {
    // 基本语法：条件 ? 值1 : 值2
    // 如果条件为真，整个表达式取值1；否则取值2
    
    int a = 10, b = 20;
    
    // 求最大值
    int max = (a > b) ? a : b;
    cout << "最大值: " << max << endl;
    
    // 求最小值
    int min = (a < b) ? a : b;
    cout << "最小值: " << min << endl;
    
    // 判断奇偶
    int num = 15;
    string result = (num % 2 == 0) ? "偶数" : "奇数";
    cout << num << " 是" << result << endl;
    
    // 嵌套使用（不推荐，太复杂）
    int score = 78;
    char grade = (score >= 90) ? 'A' :
                 (score >= 80) ? 'B' :
                 (score >= 70) ? 'C' :
                 (score >= 60) ? 'D' : 'F';
    cout << score << "分对应等级: " << grade << endl;
    
    // ⚠️ 条件运算符的优先级问题
    int i = 3, j = 5;
    // cout << (i > j ? i : j);  // 正确
    // cout << i > j ? i : j;    // ❌ 错误：等同于 (cout > j) ? i : j
    
    // 三目运算符的返回值特性
    int m = 5, n = 10;
    int* p = &(m > n ? m : n);  // 正确：返回的是变量的引用
    cout << "*p = " << *p << endl;
    
    return 0;
}
```

### 3.6 位运算符（了解即可）

```cpp
#include <iostream>
using namespace std;

int main() {
    // 位运算符操作的是二进制位
    int a = 12;  // 二进制: 1100
    int b = 10;  // 二进制: 1010
    
    cout << "a = " << a << " (二进制: " << bitset<8>(a) << ")" << endl;
    cout << "b = " << b << " (二进制: " << bitset<8>(b) << ")" << endl;
    
    // 按位与 &: 两个位都为1时结果为1
    int and_result = a & b;  // 1100 & 1010 = 1000 (8)
    cout << "a & b = " << and_result << endl;
    
    // 按位或 |: 至少一个为1时结果为1
    int or_result = a | b;   // 1100 | 1010 = 1110 (14)
    cout << "a | b = " << or_result << endl;
    
    // 按位异或 ^: 两个位不同时结果为1
    int xor_result = a ^ b;  // 1100 ^ 1010 = 0110 (6)
    cout << "a ^ b = " << xor_result << endl;
    
    // 按位取反 ~: 0变1，1变0
    int not_a = ~a;          // ~1100 = ...11110011
    cout << "~a = " << not_a << endl;
    
    // 左移 <<: 二进制位左移，低位补0
    int left_shift = a << 2;  // 1100 << 2 = 110000 (48) = 12 * 4
    cout << "a << 2 = " << left_shift << endl;
    
    // 右移 >>: 二进制位右移
    int right_shift = a >> 2;  // 1100 >> 2 = 11 (3) = 12 / 4
    cout << "a >> 2 = " << right_shift << endl;
    
    // 位运算的常用技巧
    // 1. 判断奇偶：x & 1 == 1 则为奇数
    int x = 7;
    cout << "x是" << ((x & 1) ? "奇数" : "偶数") << endl;
    
    // 2. 交换两个数（不用临时变量）
    int m = 5, n = 8;
    m = m ^ n;
    n = m ^ n;  // n = (m^n)^n = m
    m = m ^ n;  // m = (m^n)^m = n
    cout << "交换后: m=" << m << ", n=" << n << endl;
    
    // 3. 获取第n位
    int num = 12;  // 1100
    int bit = (num >> 2) & 1;  // 获取第3位（从0开始）
    cout << "第3位是: " << bit << endl;
    
    return 0;
}
```

### 3.7 sizeof运算符

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    // sizeof 返回类型或变量占用的字节数
    
    // 基本类型大小
    cout << "=== 基本类型大小 ===" << endl;
    cout << "char: " << sizeof(char) << " 字节" << endl;
    cout << "short: " << sizeof(short) << " 字节" << endl;
    cout << "int: " << sizeof(int) << " 字节" << endl;
    cout << "long: " << sizeof(long) << " 字节" << endl;
    cout << "long long: " << sizeof(long long) << " 字节" << endl;
    cout << "float: " << sizeof(float) << " 字节" << endl;
    cout << "double: " << sizeof(double) << " 字节" << endl;
    cout << "bool: " << sizeof(bool) << " 字节" << endl;
    
    // 变量大小
    int x = 100;
    cout << "\n=== 变量大小 ===" << endl;
    cout << "sizeof(x): " << sizeof(x) << " 字节" << endl;
    cout << "sizeof(x+1.0): " << sizeof(x + 1.0) << " 字节" << endl;
    
    // 数组大小
    int arr[10];
    cout << "\n=== 数组大小 ===" << endl;
    cout << "sizeof(arr): " << sizeof(arr) << " 字节" << endl;
    cout << "数组元素个数: " << sizeof(arr) / sizeof(arr[0]) << endl;
    
    // 字符串大小
    string s = "Hello";
    cout << "\n=== 字符串大小 ===" << endl;
    cout << "string对象大小: " << sizeof(s) << " 字节" << endl;
    cout << "s.length(): " << s.length() << " 字符" << endl;
    
    return 0;
}
```

### 3.8 运算符优先级

```cpp
#include <iostream>
using namespace std;

int main() {
    // 运算符优先级：先乘除后加减
    int result = 2 + 3 * 4;      // 2 + 12 = 14
    cout << "2 + 3 * 4 = " << result << endl;
    
    // 括号可以改变优先级
    result = (2 + 3) * 4;        // 5 * 4 = 20
    cout << "(2 + 3) * 4 = " << result << endl;
    
    // 复杂表达式
    bool flag = true;
    int a = 10, b = 20, c = 30;
    int val = a + b * c - a / b + a % b;
    // 计算顺序: 10 + 20*30 - 10/20 + 10%20 = 10 + 600 - 0 + 10 = 620
    
    // ⚠️ 当不确定时，用括号！
    bool ok = (a > 5) && (b < 30);  // 清晰明了
    
    // 常用优先级（从上到下递减）
    // 1. () 括号最高
    // 2. ! ++ -- sizeof
    // 3. * / %
    // 4. + -
    // 5. < <= > >=
    // 6. == !=
    // 7. &&  // 逻辑与优先级低于关系运算
    // 8. ||  // 逻辑或优先级最低
    // 9. ?:  条件运算符
    // 10. = += -= *= /= %=
    
    // 实际建议：多用括号，少记优先级
    
    return 0;
}
```

### 3.9 💡 运算符记忆口诀

> **算术优先级**："乘除取余在前，加减在后"
> **逻辑优先级**："非 > 与 > 或"
> **赋值优先级**："赋值最弱，最后执行"
> **终极建议**："括号在手，天下我有"

### 3.10 常见错误

```cpp
// ❌ 错误1：混淆 == 和 =
int x = 5;
if (x = 10) {  // ⚠️ 把10赋给了x，不是比较！
    cout << "总是执行" << endl;
}

// ✅ 正确写法
if (x == 10) { }

// ❌ 错误2：整数除法丢失精度
double avg = 5 / 2;  // 结果是2.0，不是2.5！
double avg_correct = 5.0 / 2;  // 2.5

// ❌ 错误3：除零错误
int y = 10;
int z = y / 0;  // ❌ 运行时错误！

// ❌ 错误4：自增自减的副作用
int i = 5;
int j = i++ + ++i + i++;  // ⚠️ 未定义行为！不要这样写
```

### 3.11 练习

```cpp
// 练习1：计算表达式值
int a = 5, b = 2, c = 3;
cout << a + b * c << endl;      // ?
cout << (a + b) * c << endl;    // ?
cout << a % b + c << endl;       // ?

// 练习2：判断逻辑表达式的值
cout << (5 > 3 && 2 < 4) << endl;  // ?
cout << (5 > 3 || 2 > 4) << endl;  // ?
cout << !(5 > 3) << endl;          // ?

// 练习3：交换两个整数（不引入第三个变量）
int x = 5, y = 10;
// 用异或或其他方法交换它们的值

// 练习4：判断年份是否为闰年
// 闰年条件：能被4整除但不能被100整除，或能被400整除
```

---

## 4. 控制结构

### 4.1 程序的三种基本结构

> 想象做菜的流程：
> - **顺序结构**：按步骤一步一步来（洗菜→切菜→炒菜→装盘）
> - **选择结构**：根据情况选择（如果没盐就出去买）
> - **循环结构**：重复做某事（搅拌100次）

### 4.2 if-else 选择结构

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 简单if语句
    int score = 85;
    
    if (score >= 60) {
        cout << "及格！" << endl;
    }
    
    // 2. if-else 语句
    if (score >= 90) {
        cout << "优秀" << endl;
    } else {
        cout << "需要继续努力" << endl;
    }
    
    // 3. if-else if-else 链
    if (score >= 90) {
        cout << "等级：A" << endl;
    } else if (score >= 80) {
        cout << "等级：B" << endl;
    } else if (score >= 70) {
        cout << "等级：C" << endl;
    } else if (score >= 60) {
        cout << "等级：D" << endl;
    } else {
        cout << "等级：F（不及格）" << endl;
    }
    
    // 4. 嵌套if语句
    int age = 25;
    bool hasTicket = true;
    
    if (age >= 18) {
        if (hasTicket) {
            cout << "允许入场" << endl;
        } else {
            cout << "需要购票" << endl;
        }
    } else {
        cout << "年龄不符合要求" << endl;
    }
    
    // 5. 条件运算符替代简单if-else
    int a = 10, b = 20;
    int max = (a > b) ? a : b;  // 用三目运算符找最大值
    cout << "最大值: " << max << endl;
    
    // ⚠️ if语句的常见错误
    // if (score >= 60);  // ⚠️ 多了一个分号，if什么都不做
    // {
    //     cout << "及格" << endl;  // 这行总是执行
    // }
    
    return 0;
}
```

### 4.3 switch 多分支结构

```cpp
#include <iostream>
using namespace std;

int main() {
    // switch语句用于处理离散值的判断
    
    char grade = 'B';
    
    switch (grade) {
        case 'A':
            cout << "90-100分，优秀！" << endl;
            break;  // 不要忘记break！
        case 'B':
            cout << "80-89分，良好" << endl;
            break;
        case 'C':
            cout << "70-79分，中等" << endl;
            break;
        case 'D':
            cout << "60-69分，及格" << endl;
            break;
        case 'F':
            cout << "60分以下，不及格" << endl;
            break;
        default:
            cout << "无效的成绩等级" << endl;
    }
    
    // switch的注意事项：
    // 1. 表达式必须是整数、字符或枚举类型
    // 2. case后必须是常量表达式
    // 3. 不要忘记break，否则会"穿透"
    
    // 穿透示例（有时候可以利用）
    int day = 3;
    switch (day) {
        case 1:
        case 2:
        case 3:
        case 4:
        case 5:
            cout << "工作日" << endl;
            break;
        case 6:
        case 7:
            cout << "周末" << endl;
            break;
    }
    
    // ⚠️ 常见错误
    // switch (score) {  // ❌ score是double，不能用于switch
    //     case 90.0:  // ❌ 不能用小数
    // }
    
    return 0;
}
```

### 4.4 for 循环

```cpp
#include <iostream>
#include <cmath>
using namespace std;

int main() {
    // for循环的基本结构
    // for(初始化; 条件判断; 更新) { 循环体 }
    
    // 1. 打印1到10
    cout << "=== 打印1到10 ===" << endl;
    for (int i = 1; i <= 10; i++) {
        cout << i << " ";
    }
    cout << endl;
    
    // 2. 计算1+2+...+100
    int sum = 0;
    for (int i = 1; i <= 100; i++) {
        sum += i;
    }
    cout << "1+2+...+100 = " << sum << endl;
    
    // 3. 计算阶乘
    int n = 5;
    long long factorial = 1;
    for (int i = 1; i <= n; i++) {
        factorial *= i;
    }
    cout << n << "! = " << factorial << endl;
    
    // 4. 打印九九乘法表
    cout << "\n=== 九九乘法表 ===" << endl;
    for (int i = 1; i <= 9; i++) {
        for (int j = 1; j <= i; j++) {
            cout << j << "×" << i << "=" << i*j << "\t";
        }
        cout << endl;
    }
    
    // 5. for循环的变体
    // 省略初始化
    int i = 1;
    for (; i <= 5; i++) {
        cout << i << " ";
    }
    cout << endl;
    
    // 省略条件（死循环，需要在内部用break退出）
    int count = 0;
    for (;;) {
        count++;
        if (count > 5) break;
        cout << count << " ";
    }
    cout << endl;
    
    // 多个循环变量
    for (int i = 0, j = 10; i < j; i++, j--) {
        cout << "(" << i << "," << j << ") ";
    }
    cout << endl;
    
    return 0;
}
```

### 4.5 while 循环

```cpp
#include <iostream>
#include <cstdlib>
#include <ctime>
using namespace std;

int main() {
    // while循环：先判断条件，再执行循环体
    
    // 1. 打印1到5
    cout << "=== while循环 ===" << endl;
    int i = 1;
    while (i <= 5) {
        cout << i << " ";
        i++;
    }
    cout << endl;
    
    // 2. 计算各位数字之和
    int num = 12345;
    int digit_sum = 0;
    while (num > 0) {
        digit_sum += num % 10;  // 取最后一位
        num /= 10;               // 去掉最后一位
    }
    cout << "各位数字之和: " << digit_sum << endl;
    
    // 3. 猜数字游戏
    cout << "\n=== 猜数字游戏 ===" << endl;
    srand(time(0));  // 随机数种子
    int target = rand() % 100 + 1;  // 1-100的随机数
    int guess;
    int attempts = 0;
    
    cout << "我已经想好了一个1-100的数字，请猜：" << endl;
    
    while (true) {
        cin >> guess;
        attempts++;
        
        if (guess > target) {
            cout << "太大了，再试一次：" << endl;
        } else if (guess < target) {
            cout << "太小了，再试一次：" << endl;
        } else {
            cout << "恭喜你！猜对了！" << endl;
            cout << "你一共猜了 " << attempts << " 次" << endl;
            break;  // 猜对了，退出循环
        }
    }
    
    // 4. while vs for 的选择
    // 已知循环次数 → 用 for
    for (int j = 0; j < 5; j++) { }  // 明确知道执行5次
    
    // 不知道循环次数 → 用 while
    // while (条件满足) { }  // 不知道要循环多少次
    
    return 0;
}
```

### 4.6 do-while 循环

```cpp
#include <iostream>
using namespace std;

int main() {
    // do-while：先执行循环体，再判断条件
    // 特点：至少会执行一次
    
    // 1. 基本用法
    cout << "=== do-while循环 ===" << endl;
    int i = 1;
    do {
        cout << i << " ";
        i++;
    } while (i <= 5);
    cout << endl;
    
    // 2. 菜单选择示例
    cout << "\n=== 菜单系统 ===" << endl;
    int choice;
    
    do {
        cout << "\n请选择操作：" << endl;
        cout << "1. 查看成绩" << endl;
        cout << "2. 添加成绩" << endl;
        cout << "3. 删除成绩" << endl;
        cout << "0. 退出" << endl;
        cout << "您的选择: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "显示成绩..." << endl;
                break;
            case 2:
                cout << "添加成绩..." << endl;
                break;
            case 3:
                cout << "删除成绩..." << endl;
                break;
            case 0:
                cout << "感谢使用，再见！" << endl;
                break;
            default:
                cout << "无效的选择，请重试" << endl;
        }
    } while (choice != 0);
    
    // ⚠️ while vs do-while 的区别
    int n = 10;
    cout << "\nwhile循环：" << endl;
    while (n < 5) {
        cout << "不会执行" << endl;  // 条件不满足，一次都不执行
    }
    
    n = 10;
    cout << "do-while循环：" << endl;
    do {
        cout << "会执行一次" << endl;  // 先执行，再判断
    } while (n < 5);
    
    return 0;
}
```

### 4.7 break 与 continue

```cpp
#include <iostream>
using namespace std;

int main() {
    // break：跳出整个循环
    // continue：跳过本次循环，进入下一次
    
    // 1. break示例：找出第一个能被7整除的数
    cout << "=== break示例 ===" << endl;
    for (int i = 1; i <= 100; i++) {
        if (i % 7 == 0) {
            cout << "第一个能被7整除的数是: " << i << endl;
            break;  // 找到就退出循环
        }
    }
    
    // 2. continue示例：打印1-10，跳过3的倍数
    cout << "\n=== continue示例 ===" << endl;
    for (int i = 1; i <= 10; i++) {
        if (i % 3 == 0) {
            continue;  // 跳过3的倍数
        }
        cout << i << " ";
    }
    cout << endl;
    
    // 3. break在嵌套循环中的应用
    cout << "\n=== 嵌套循环中的break ===" << endl;
    for (int i = 1; i <= 3; i++) {
        for (int j = 1; j <= 3; j++) {
            cout << "(" << i << "," << j << ") ";
            if (j == 2) {
                break;  // 只跳出内层循环
            }
        }
        cout << endl;
    }
    
    // 4. 配合switch的break
    cout << "\n=== switch配合break ===" << endl;
    for (int i = 1; i <= 5; i++) {
        switch (i) {
            case 3:
                cout << "跳过3" << endl;
                continue;  // 跳过i=3的情况
            case 5:
                cout << "遇到5，跳出" << endl;
                break;      // 退出for循环
        }
        cout << "处理数字: " << i << endl;
    }
    
    return 0;
}
```

### 4.8 循环的典型应用

```cpp
#include <iostream>
#include <cmath>
using namespace std;

int main() {
    // 1. 累加求和
    int sum = 0;
    for (int i = 1; i <= 100; i++) {
        sum += i;
    }
    cout << "1+2+...+100 = " << sum << endl;
    
    // 2. 求最大值和最小值
    int arr[] = {34, 67, 23, 89, 12, 56, 78};
    int max = arr[0], min = arr[0];
    for (int i = 1; i < 7; i++) {
        if (arr[i] > max) max = arr[i];
        if (arr[i] < min) min = arr[i];
    }
    cout << "最大值: " << max << ", 最小值: " << min << endl;
    
    // 3. 穷举法：鸡兔同笼
    // 鸡兔共35只，94条腿，问鸡兔各几只？
    cout << "\n=== 鸡兔同笼问题 ===" << endl;
    for (int chicken = 0; chicken <= 35; chicken++) {
        int rabbit = 35 - chicken;
        if (chicken * 2 + rabbit * 4 == 94) {
            cout << "鸡: " << chicken << "只, 兔: " << rabbit << "只" << endl;
        }
    }
    
    // 4. 水仙花数（三位数各位立方和等于本身）
    cout << "\n=== 水仙花数 ===" << endl;
    for (int n = 100; n <= 999; n++) {
        int a = n / 100;       // 百位
        int b = n / 10 % 10;   // 十位
        int c = n % 10;        // 个位
        if (a*a*a + b*b*b + c*c*c == n) {
            cout << n << " ";
        }
    }
    cout << endl;
    
    // 5. 素数判定
    cout << "\n=== 100以内的素数 ===" << endl;
    for (int n = 2; n <= 100; n++) {
        bool isPrime = true;
        for (int i = 2; i <= sqrt(n); i++) {
            if (n % i == 0) {
                isPrime = false;
                break;
            }
        }
        if (isPrime) cout << n << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 4.9 💡 循环选择指南

| 循环类型 | 特点 | 适用场景 |
|---------|------|---------|
| for | 循环次数明确 | 遍历、计数 |
| while | 先判断后执行 | 次数不确定 |
| do-while | 先执行后判断 | 至少执行一次 |

> **记忆口诀**：
> - 次数确定用 **for**
> - 次数不确定用 **while**
> - 至少一次用 **do-while**

### 4.10 常见错误

```cpp
// ❌ 错误1：死循环
// for (int i = 1; i > 0; i++) { }  // i永远大于0，无限循环
// while (true) { }  // 死循环

// ❌ 错误2：分号位置错误
// for (int i = 0; i < 5; i++);  // 多了一个分号
// {
//     cout << i << endl;  // 只执行一次，因为i未定义
// }

// ❌ 错误3：循环变量类型选择不当
// for (int i = 0.1; i <= 1.0; i += 0.1)  // ⚠️ 浮点数比较不精确

// ❌ 错误4：循环计数器溢出
// for (int i = 0; i < 2147483647; i++) { }  // ⚠️ 可能溢出
```

### 4.11 练习

```cpp
// 练习1：打印菱形
//    *
//   ***
//  *****
// *******
//  *****
//   ***
//    *

// 练习2：求斐波那契数列前20项
// 1, 1, 2, 3, 5, 8, 13, ...

// 练习3：输入一个数，判断是否是回文数
// 12321是回文数，12345不是

// 练习4：求最大公约数和最小公倍数
// 提示：辗转相除法

// 练习5：百钱买百鸡
// 公鸡5文钱一只，母鸡3文钱一只，小鸡1文钱三只
// 用100文钱买100只鸡，问公鸡、母鸡、小鸡各几只？
```

---

## 5. 数组与指针

### 5.1 数组的概念

**通俗理解**：数组就是"连排的储物柜"，每个柜子都有编号（索引），可以存放同类物品。

> 想象宿舍的床位：
> - 床位1、床位2、床位3...一排排的
> - 每个床位只能睡一个人（相同类型）
> - 床位编号从0开始（第一张床是0号，不是1号）
>
> C++数组也是一样：
> - 一组连续存储的数据
> - 每个元素类型相同
> - 通过索引（从0开始）访问

### 5.2 一维数组

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 数组的定义
    // 类型 数组名[长度];
    int scores[5];  // 定义5个整数的数组，未初始化
    double prices[3];
    char grades[4];
    
    // 2. 初始化方式
    // 方式1：定义时初始化
    int nums1[5] = {1, 2, 3, 4, 5};
    
    // 方式2：部分初始化（未初始化的元素默认为0）
    int nums2[5] = {1, 2};      // nums2 = {1, 2, 0, 0, 0}
    
    // 方式3：省略长度（编译器自动推断）
    int nums3[] = {10, 20, 30};  // 长度为3
    
    // 方式4：全部初始化为0
    int nums4[5] = {0};          // nums4 = {0, 0, 0, 0, 0}
    int nums5[5] = {};           // 也是全0
    
    // 3. 数组的访问
    int arr[5] = {10, 20, 30, 40, 50};
    cout << "第一个元素: " << arr[0] << endl;   // 10
    cout << "第三个元素: " << arr[2] << endl;   // 30
    cout << "最后一个元素: " << arr[4] << endl; // 50
    
    // ⚠️ 重要：数组下标从0开始！
    // arr[5] 是错误的！有效范围是 0 ~ 4
    
    // 4. 遍历数组
    cout << "\n=== 遍历数组 ===" << endl;
    for (int i = 0; i < 5; i++) {
        cout << "arr[" << i << "] = " << arr[i] << endl;
    }
    
    // 5. 计算数组长度
    int size = sizeof(arr) / sizeof(arr[0]);
    cout << "\n数组长度: " << size << endl;
    
    // 6. 使用循环输入数组
    cout << "\n请输入5个整数:" << endl;
    for (int i = 0; i < 5; i++) {
        cin >> arr[i];
    }
    
    // 7. 求最大值和最小值
    int max = arr[0], min = arr[0];
    for (int i = 1; i < 5; i++) {
        if (arr[i] > max) max = arr[i];
        if (arr[i] < min) min = arr[i];
    }
    cout << "最大值: " << max << ", 最小值: " << min << endl;
    
    // 8. 数组倒置
    cout << "倒置前: ";
    for (int i = 0; i < 5; i++) cout << arr[i] << " ";
    cout << endl;
    
    for (int i = 0; i < 5 / 2; i++) {
        int temp = arr[i];
        arr[i] = arr[4 - i];
        arr[4 - i] = temp;
    }
    cout << "倒置后: ";
    for (int i = 0; i < 5; i++) cout << arr[i] << " ";
    cout << endl;
    
    return 0;
}
```

### 5.3 二维数组

```cpp
#include <iostream>
using namespace std;

int main() {
    // 二维数组：可以理解为"表格"或"矩阵"
    
    // 1. 定义和初始化
    // 类型 数组名[行数][列数];
    int matrix[3][4];  // 3行4列的数组，未初始化
    
    // 定义时初始化
    int scores[2][3] = {
        {90, 85, 92},   // 第一行
        {78, 88, 95}    // 第二行
    };
    
    // 可以省略内层大括号
    int nums[2][3] = {1, 2, 3, 4, 5, 6};
    
    // 可以只初始化部分
    int arr[3][3] = {
        {1, 2},
        {3, 4, 5}
        // 第三行默认全0
    };
    
    // 2. 访问元素
    cout << "第1行第2列: " << scores[0][1] << endl;  // 85
    cout << "第2行第3列: " << scores[1][2] << endl;  // 95
    
    // 3. 遍历二维数组
    cout << "\n=== 遍历二维数组 ===" << endl;
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++) {
            cout << scores[i][j] << "\t";
        }
        cout << endl;
    }
    
    // 4. 计算二维数组的大小
    cout << "\n=== 数组大小 ===" << endl;
    cout << "总字节数: " << sizeof(scores) << endl;
    cout << "行数: " << sizeof(scores) / sizeof(scores[0]) << endl;
    cout << "列数: " << sizeof(scores[0]) / sizeof(scores[0][0]) << endl;
    cout << "元素总数: " << sizeof(scores) / sizeof(scores[0][0]) << endl;
    
    // 5. 矩阵加法示例
    cout << "\n=== 矩阵加法 ===" << endl;
    int A[2][3] = {{1, 2, 3}, {4, 5, 6}};
    int B[2][3] = {{6, 5, 4}, {3, 2, 1}};
    int C[2][3];
    
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++) {
            C[i][j] = A[i][j] + B[i][j];
            cout << C[i][j] << " ";
        }
        cout << endl;
    }
    
    // 6. 打印九九乘法表
    cout << "\n=== 九九乘法表 ===" << endl;
    int table[9][9];
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j <= i; j++) {
            table[i][j] = (i + 1) * (j + 1);
            cout << (j + 1) << "×" << (i + 1) << "=" << table[i][j] << "\t";
        }
        cout << endl;
    }
    
    return 0;
}
```

### 5.4 指针基础

**通俗理解**：指针就是"地址"，它告诉你某个东西在哪里。

> 想象你家的门牌号：
> - **变量** = 房子
> - **变量的值** = 房子里的人
> - **指针** = 门牌号（地址）
> - **解引用** = 拿着门牌号找到房子，看里面的人

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 指针的定义
    int num = 10;
    int* ptr;  // 定义一个指向int的指针
    
    // 2. 指针的赋值（取地址符 &）
    ptr = &num;  // &num 表示变量num的地址
    
    // 3. 指针的访问（解引用符 *）
    cout << "num的值: " << num << endl;      // 10
    cout << "num的地址: " << &num << endl;   // 0x...
    cout << "ptr指向的值: " << *ptr << endl;  // 10（解引用）
    cout << "ptr本身的值（地址）: " << ptr << endl;
    
    // 4. 不同类型的指针
    double pi = 3.14159;
    double* ptr_d = &pi;
    cout << "\ndouble类型指针:" << endl;
    cout << "pi的值: " << pi << endl;
    cout << "*ptr_d: " << *ptr_d << endl;
    
    char ch = 'A';
    char* ptr_c = &ch;
    cout << "\nchar类型指针:" << endl;
    cout << "ch的值: " << ch << endl;
    cout << "*ptr_c: " << *ptr_c << endl;
    
    // 5. 指针的大小
    cout << "\n=== 指针大小 ===" << endl;
    cout << "int* 大小: " << sizeof(int*) << " 字节" << endl;
    cout << "double* 大小: " << sizeof(double*) << " 字节" << endl;
    cout << "char* 大小: " << sizeof(char*) << " 字节" << endl;
    // 64位系统中，指针都是8字节
    
    // 6. 指针的运算
    int arr[] = {10, 20, 30, 40, 50};
    int* p = arr;  // 数组名就是首元素的地址
    cout << "\n=== 指针运算 ===" << endl;
    cout << "*p = " << *p << endl;      // 10
    p++;                                 // 指向下一个元素
    cout << "*p = " << *p << endl;      // 20
    p += 2;                              // 再向后移2个元素
    cout << "*p = " << *p << endl;      // 50
    
    // 7. 指针与数组的关系
    cout << "\n=== 指针与数组 ===" << endl;
    cout << "arr[0] = " << arr[0] << endl;
    cout << "*(arr+0) = " << *(arr+0) << endl;
    cout << "arr[2] = " << arr[2] << endl;
    cout << "*(arr+2) = " << *(arr+2) << endl;
    // arr[i] 等价于 *(arr+i)
    
    return 0;
}
```

### 5.5 指针与函数

```cpp
#include <iostream>
using namespace std;

// 1. 传值调用 vs 传址调用
void swap_value(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
    // ❌ 这样无法交换，因为a和b是副本
}

void swap_pointer(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
    // ✅ 通过指针可以真正交换
}

int main() {
    int x = 10, y = 20;
    cout << "交换前: x=" << x << ", y=" << y << endl;
    
    swap_value(x, y);
    cout << "swap_value后: x=" << x << ", y=" << y << endl;  // 10, 20（未交换）
    
    swap_pointer(&x, &y);
    cout << "swap_pointer后: x=" << x << ", y=" << y << endl;  // 20, 10（已交换）
    
    // 2. 指针作为函数返回值
    int arr[] = {5, 2, 8, 1, 9};
    int* find_max(int* arr, int n) {
        int* max_ptr = arr;
        for (int i = 1; i < n; i++) {
            if (*(arr+i) > *max_ptr) {
                max_ptr = arr + i;
            }
        }
        return max_ptr;
    }
    
    int* max_pos = find_max(arr, 5);
    cout << "最大值的地址: " << max_pos << endl;
    cout << "最大值: " << *max_pos << endl;
    
    // 3. 指针数组
    cout << "\n=== 指针数组 ===" << endl;
    const char* names[] = {"Alice", "Bob", "Charlie"};
    for (int i = 0; i < 3; i++) {
        cout << names[i] << endl;
    }
    
    return 0;
}
```

### 5.6 动态内存分配

```cpp
#include <iostream>
using namespace std;

int main() {
    // 1. 静态数组 vs 动态数组
    // 静态数组：大小在编译时确定
    int static_arr[5] = {1, 2, 3, 4, 5};
    
    // 动态数组：大小在运行时确定
    int n;
    cout << "请输入数组长度: ";
    cin >> n;
    
    // 使用 new 分配内存
    int* dynamic_arr = new int[n];
    
    // 使用数组
    for (int i = 0; i < n; i++) {
        dynamic_arr[i] = i + 1;
    }
    
    cout << "动态数组内容: ";
    for (int i = 0; i < n; i++) {
        cout << dynamic_arr[i] << " ";
    }
    cout << endl;
    
    // ⚠️ 使用完毕后必须释放内存！
    delete[] dynamic_arr;
    dynamic_arr = nullptr;  // 释放后指向nullptr是好习惯
    
    // 2. 动态分配单个变量
    int* p = new int(100);  // 分配并初始化为100
    cout << "*p = " << *p << endl;
    delete p;
    p = nullptr;
    
    // 3. 动态二维数组
    cout << "\n=== 动态二维数组 ===" << endl;
    int rows = 3, cols = 4;
    
    // 分配
    int** matrix = new int*[rows];
    for (int i = 0; i < rows; i++) {
        matrix[i] = new int[cols];
    }
    
    // 使用
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            matrix[i][j] = (i + 1) * (j + 1);
            cout << matrix[i][j] << "\t";
        }
        cout << endl;
    }
    
    // 释放（先释放内层，再释放外层）
    for (int i = 0; i < rows; i++) {
        delete[] matrix[i];
    }
    delete[] matrix;
    matrix = nullptr;
    
    // ⚠️ 内存泄漏示例
    // void leak() {
    //     int* p = new int[100];
    //     // ... 使用p
    //     // return;  // 函数结束，但没有delete！
    //     // p指向的内存永远丢失了
    // }
    
    return 0;
}
```

### 5.7 💡 指针总结

> **指针的核心概念**：
> - **指针变量**：存储地址的变量
> - **`&`运算符**：取地址
> - **`*`运算符**：解引用（取指针指向的值）
> - **指针算术**：`p++`、`p+i` 等
> - **`new`/`delete`**：动态内存分配/释放
>
> **数组与指针的关系**：
> - 数组名 = 首元素地址
> - `arr[i]` = `*(arr+i)`
> - 指针可以像数组一样用下标访问

### 5.8 常见错误

```cpp
// ❌ 错误1：空指针解引用
int* p = nullptr;
cout << *p << endl;  // ❌ 程序崩溃！

// ❌ 错误2：未初始化的指针
int* p2;              // ⚠️ 野指针，指向未知地址
// *p2 = 100;         // ❌ 危险！可能修改了任意内存

// ❌ 错误3：数组越界
int arr[5] = {1, 2, 3, 4, 5};
cout << arr[5] << endl;  // ❌ 越界！arr[5]不存在

// ❌ 错误4：内存泄漏
void bad_function() {
    int* p = new int[100];
    // ... 使用p
    // 忘记 delete[] p;
}

// ❌ 错误5：重复释放内存
int* p = new int[10];
delete[] p;
// delete[] p;  // ❌ 重复释放，未定义行为！

// ❌ 错误6：野指针
int* p = new int[10];
delete[] p;
p[0] = 100;  // ❌ p已释放，但仍然使用它
```

### 5.9 练习

```cpp
// 练习1：找出数组中的第二大数

// 练习2：实现字符串反转（不用库函数）

// 练习3：实现strcmp函数（比较两个字符串）

// 练习4：使用指针实现冒泡排序

// 练习5：动态分配一个3x3矩阵，输入数据后转置输出
```

---

## 6. 函数基础

### 6.1 函数的概念

**通俗理解**：函数就是程序的"积木块"，每个积木块完成一个特定任务。

> 想象做汉堡的流程：
> - 需要准备面包、肉饼、蔬菜、酱料
> - 需要把它们组合在一起
> - 需要有人来"调用"这个做汉堡的过程
>
> 程序中的函数也一样：
> - 有输入（参数）
> - 有处理过程
> - 有输出（返回值）

### 6.2 函数的定义和调用

```cpp
#include <iostream>
using namespace std;

// 函数的定义
// 返回类型 函数名(参数列表) { 函数体 }

// 1. 无返回值无参数的函数
void printMenu() {
    cout << "========== 菜单 ==========" << endl;
    cout << "1. 查看成绩" << endl;
    cout << "2. 添加成绩" << endl;
    cout << "3. 退出" << endl;
    cout << "=========================" << endl;
}

// 2. 有返回值无参数的函数
int getRandomNumber() {
    return 42;  // 返回一个固定的值
}

// 3. 无返回值有参数的函数
void printNTimes(char c, int n) {
    for (int i = 0; i < n; i++) {
        cout << c;
    }
    cout << endl;
}

// 4. 有返回值有参数的函数
int max(int a, int b) {
    return (a > b) ? a : b;
}

// 5. 多返回值（通过引用参数）
void divide(int a, int b, int& quotient, int& remainder) {
    quotient = a / b;
    remainder = a % b;
}

int main() {
    // 调用函数
    printMenu();
    
    int num = getRandomNumber();
    cout << "随机数: " << num << endl;
    
    printNTimes('*', 20);
    
    int m = 10, n = 3;
    cout << m << "和" << n << "的最大值: " << max(m, n) << endl;
    
    int q, r;
    divide(17, 5, q, r);
    cout << "17 ÷ 5 = " << q << " ... " << r << endl;
    
    return 0;
}
```

### 6.3 函数原型（声明）

```cpp
#include <iostream>
using namespace std;

// 函数原型（声明）- 告诉编译器函数存在
int add(int a, int b);           // 返回两个整数的和
double multiply(double x, double y);  // 返回两个浮点数的乘积
void greet(string name);          // 无返回值

// 也可以只写参数类型，不写参数名
int calculate(int, int, int);     // 合法的函数原型

// main函数必须放在能看到它调用的函数声明的位置
// 或者函数定义在调用之前

int main() {
    cout << "10 + 20 = " << add(10, 20) << endl;
    cout << "3.14 * 2 = " << multiply(3.14, 2) << endl;
    greet("Alice");
    return 0;
}

// 函数定义
int add(int a, int b) {
    return a + b;
}

double multiply(double x, double y) {
    return x * y;
}

void greet(string name) {
    cout << "Hello, " << name << "!" << endl;
}

// 不符合规则的例子：
// void test() {
//     foo();  // ❌ 编译器不知道foo是什么！
// }
// void foo() { }  // foo定义在test之后
```

### 6.4 参数传递

```cpp
#include <iostream>
using namespace std;

// 1. 值传递（传值）
void byValue(int x) {
    x = 100;  // 只修改副本
}

// 2. 指针传递（传地址）
void byPointer(int* x) {
    *x = 100;  // 修改原值
}

// 3. 引用传递（传引用）
void byReference(int& x) {
    x = 100;  // 修改原值
}

// 4. 常量引用（不能修改）
void printArray(const int arr[], int n) {
    for (int i = 0; i < n; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;
    // arr[i] = 0;  // ❌ 编译错误，不能修改
}

int main() {
    int num = 10;
    
    cout << "=== 值传递 ===" << endl;
    cout << "调用前: " << num << endl;
    byValue(num);
    cout << "调用后: " << num << endl;  // 仍然是10
    
    cout << "\n=== 指针传递 ===" << endl;
    cout << "调用前: " << num << endl;
    byPointer(&num);
    cout << "调用后: " << num << endl;  // 变成100
    
    num = 10;  // 恢复原值
    cout << "\n=== 引用传递 ===" << endl;
    cout << "调用前: " << num << endl;
    byReference(num);
    cout << "调用后: " << num << endl;  // 变成100
    
    // 值传递 vs 引用传递的选择
    // 1. 如果不需要修改原变量：用值传递（安全）
    // 2. 如果需要修改原变量：用引用传递（更简洁）
    // 3. 如果传递大型对象：用const引用（省去复制开销）
    
    int arr[] = {1, 2, 3, 4, 5};
    cout << "\n=== 常量引用传递数组 ===" << endl;
    printArray(arr, 5);
    
    return 0;
}
```

### 6.5 函数重载

**概念**：同一个作用域内，可以定义多个同名函数，只要参数列表不同。

```cpp
#include <iostream>
#include <string>
using namespace std;

// 1. 参数个数不同
int add(int a, int b) {
    return a + b;
}

int add(int a, int b, int c) {
    return a + b + c;
}

// 2. 参数类型不同
double add(double a, double b) {
    return a + b;
}

// 3. 参数顺序不同（本质是类型组合不同）
string add(string a, string b) {
    return a + b;
}

// ❌ 以下不能作为重载（参数名不同不算重载）
// int add(int x, int y) { return x + y; }  // ❌ 与第一个冲突

// ⚠️ 注意：函数重载与默认参数可能产生歧义
void print(int x) {
    cout << "int: " << x << endl;
}

void print(double x) {
    cout << "double: " << x << endl;
}

int main() {
    // 编译器根据实参类型选择合适的函数
    cout << add(1, 2) << endl;           // 调用 int add(int, int)
    cout << add(1, 2, 3) << endl;         // 调用 int add(int, int, int)
    cout << add(1.5, 2.5) << endl;        // 调用 double add(double, double)
    
    print(10);                           // int: 10
    print(3.14);                         // double: 3.14
    
    // ⚠️ 自动类型转换可能导致歧义
    // print('A');  // char可以转int也可以转double，优先转int
    
    return 0;
}
```

### 6.6 内联函数

```cpp
#include <iostream>
using namespace std;

// 普通函数：有调用开销
int max1(int a, int b) {
    return (a > b) ? a : b;
}

// 内联函数：编译时展开，无调用开销
// 适用场景：函数体小、调用频繁
inline int max2(int a, int b) {
    return (a > b) ? a : b;
}

int main() {
    int x = 10, y = 20;
    
    // 普通函数调用
    int result1 = max1(x, y);
    
    // 内联函数调用
    // 编译器可能将其展开为: int result2 = (x > y) ? x : y;
    int result2 = max2(x, y);
    
    // ⚠️ 内联函数的限制
    // 1. 函数体要简单（几行代码）
    // 2. 不能有循环、switch、递归
    // 3. 不能是虚函数
    // 4. 只是一个"建议"，编译器可能忽略
    
    return 0;
}
```

### 6.7 递归函数

**通俗理解**：递归就是"自己调用自己"，像俄罗斯套娃。

> 想象你站在两面镜子之间：
> - 镜子1照出镜子2的像
> - 镜子2照出镜子1的像
> - 无限循环...
>
> 递归也是一样：
> - 函数调用自己
> - 需要有终止条件
> - 每次调用都要向终止条件靠近

```cpp
#include <iostream>
using namespace std;

// 1. 递归求阶乘
long long factorial(int n) {
    if (n <= 1) {           // 终止条件
        return 1;
    }
    return n * factorial(n - 1);  // 递归调用
}

// 2. 递归求斐波那契数列
long long fibonacci(int n) {
    if (n <= 1) {           // 终止条件
        return n;
    }
    return fibonacci(n - 1) + fibonacci(n - 2);  // 递归调用
}

// 3. 递归求和
int sum(int n) {
    if (n <= 0) return 0;  // 终止条件
    return n + sum(n - 1);  // 递归调用
}

// 4. 递归求数组和
int arraySum(int arr[], int n) {
    if (n <= 0) return 0;           // 终止条件
    return arr[n - 1] + arraySum(arr, n - 1);  // 递归调用
}

// 5. 递归反转字符串
void reverseString(string& s, int left, int right) {
    if (left >= right) return;  // 终止条件
    swap(s[left], s[right]);   // 交换
    reverseString(s, left + 1, right - 1);  // 递归调用
}

int main() {
    // 测试阶乘
    cout << "5! = " << factorial(5) << endl;  // 120
    
    // 测试斐波那契数列
    cout << "前10项斐波那契数列: ";
    for (int i = 0; i < 10; i++) {
        cout << fibonacci(i) << " ";
    }
    cout << endl;
    
    // 测试求和
    cout << "1+2+...+100 = " << sum(100) << endl;  // 5050
    
    // 测试数组求和
    int arr[] = {1, 2, 3, 4, 5};
    cout << "数组求和: " << arraySum(arr, 5) << endl;  // 15
    
    // 测试字符串反转
    string str = "Hello";
    reverseString(str, 0, str.length() - 1);
    cout << "反转后: " << str << endl;  // olleH
    
    return 0;
}
```

### 6.8 函数的默认参数

```cpp
#include <iostream>
using namespace std;

// 默认参数：右侧参数可以提供默认值
void printInfo(string name, int age = 18, double gpa = 3.5) {
    cout << name << ", " << age << "岁, GPA: " << gpa << endl;
}

// ⚠️ 默认参数的规则
// 1. 默认参数必须放在参数列表的右侧
// 2. 如果函数有声明（原型），默认参数放在声明中
// 3. 同一个函数，默认参数只能指定一次

// 正确示例
void func1(int a, int b = 10, int c = 20) { }

// ❌ 错误示例
// void func2(int a = 5, int b, int c = 10) { }  // ❌ 非右侧参数不能有默认值
// void func3(int a = 5, int b = 10) { }
// void func3(int a = 1, int b = 2) { }  // ❌ 重复定义默认参数

int main() {
    // 调用时使用默认参数
    printInfo("Alice");                    // 使用全部默认参数
    printInfo("Bob", 20);                  // 使用gpa的默认值
    printInfo("Charlie", 21, 3.8);         // 不使用任何默认值
    
    return 0;
}
```

### 6.9 函数指针

```cpp
#include <iostream>
using namespace std;

// 函数指针：指向函数的指针
int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }
int multiply(int a, int b) { return a * b; }

int main() {
    // 1. 定义函数指针
    // 返回类型 (*指针名)(参数类型列表)
    int (*ptr)(int, int);
    
    // 2. 指向函数
    ptr = add;
    cout << "ptr(10, 5) = " << ptr(10, 5) << endl;  // 15
    
    ptr = subtract;
    cout << "ptr(10, 5) = " << ptr(10, 5) << endl;  // 5
    
    ptr = multiply;
    cout << "ptr(10, 5) = " << ptr(10, 5) << endl;  // 50
    
    // 3. 函数指针作为参数（回调函数）
    int calculate(int x, int y, int (*op)(int, int)) {
        return op(x, y);
    }
    
    cout << "\n=== 使用函数指针作为参数 ===" << endl;
    cout << "10 + 5 = " << calculate(10, 5, add) << endl;
    cout << "10 - 5 = " << calculate(10, 5, subtract) << endl;
    cout << "10 * 5 = " << calculate(10, 5, multiply) << endl;
    
    // 4. typedef简化函数指针
    typedef int (*BinaryOp)(int, int);
    BinaryOp op = add;
    cout << "\nop(100, 200) = " << op(100, 200) << endl;
    
    return 0;
}
```

### 6.10 💡 函数设计原则

> **好函数的标准**：
> 1. **单一职责**：一个函数只做一件事
> 2. **命名清晰**：函数名要能说明它的功能
> 3. **参数适量**：参数太多考虑用结构体
> 4. **不要太长**：超过一屏考虑拆分
> 5. **避免副作用**：尽量不修改全局变量

### 6.11 常见错误

```cpp
// ❌ 错误1：函数声明与定义不匹配
// int add(int a, int b);  // 声明：返回int
// double add(int a, int b) { }  // 定义：返回double，❌冲突

// ❌ 错误2：忘记返回值
// int max(int a, int b) {
//     if (a > b) return a;
//     // ⚠️ 如果a<=b，什么都不返回！
// }

// ❌ 错误3：递归没有终止条件
// long long badRecursion() {
//     return badRecursion();  // ❌ 无限递归，栈溢出
// }

// ❌ 错误4：默认参数放错位置
// void func(int a = 5, int b) { }  // ❌ 编译错误

// ❌ 错误5：数组作为参数退化为指针
// void process(int arr[10]) {
//     sizeof(arr);  // ⚠️ 这不是数组大小，是指针大小！
// }
```

### 6.12 练习

```cpp
// 练习1：编写函数判断闰年
// bool isLeapYear(int year);
// 闰年条件：能被4整除但不能被100整除，或能被400整除

// 练习2：编写递归函数求字符串长度（不用strlen）

// 练习3：编写函数实现字符串连接（不用strcat）
// char* myStrcat(char* dest, const char* src);

// 练习4：编写函数用二分查找在有序数组中查找元素
// int binarySearch(int arr[], int n, int target);

// 练习5：汉诺塔问题
// void hanoi(int n, char from, char to, char aux);
```

---

## 7. 本章小结

### 核心知识点回顾

```
┌────────────────────────────────────────────────────────────────┐
│                    第1章 C++基础知识点                          │
├────────────────────────────────────────────────────────────────┤
│ 1. 数据类型                                                    │
│    - 整型(int)、浮点型(double)、字符型(char)、布尔型(bool)      │
│    - 类型转换：隐式转换 vs 显式转换                              │
│                                                                │
│ 2. 变量与常量                                                  │
│    - 变量：可变的存储空间                                       │
│    - 常量：const、constexpr、enum                              │
│    - 命名规范：驼峰法、下划线法                                  │
│                                                                │
│ 3. 运算符与表达式                                              │
│    - 算术、关系、逻辑、条件运算符                                │
│    - 运算符优先级：多用括号！                                    │
│                                                                │
│ 4. 控制结构                                                    │
│    - if-else、switch                                          │
│    - for、while、do-while                                      │
│    - break、continue                                           │
│                                                                │
│ 5. 数组与指针                                                  │
│    - 一维数组、二维数组                                         │
│    - 指针：地址、解引用、指针运算                                │
│    - 动态内存：new/delete                                       │
│                                                                │
│ 6. 函数基础                                                    │
│    - 定义、声明、调用                                           │
│    - 参数传递：值传递、引用传递                                 │
│    - 函数重载、默认参数、内联函数                                │
│    - 递归                                                       │
└────────────────────────────────────────────────────────────────┘
```

### 学习建议

1. **多动手**：每个知识点都要自己敲代码验证
2. **多思考**：理解"为什么"比"是什么"更重要
3. **多练习**：从简单题目开始，逐步增加难度
4. **善用调试**：学会用IDE的调试功能单步执行

### 下一章预告

下一章我们将学习**文件与流**，让程序能够永久保存数据！

主要内容：
- 文件流类（ifstream、ofstream、fstream）
- 文本文件读写
- 二进制文件读写
- 文件定位与随机访问

---

*返回 [主目录](../README.md)*
