# 第1章 C++ 基础知识

> 本章目标：掌握 C++ 程序的基本零件。先把螺丝刀认全，再去装机器人。

## 目录

1. C++ 程序基本结构
2. 标识符、关键字与命名
3. 基本数据类型
4. 变量、常量与初始化
5. 运算符与表达式
6. 类型转换
7. 输入输出
8. 常见坑
9. 本章小结

## 1. C++ 程序基本结构

```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "Hello C++" << endl;
    return 0;
}
```

- `#include` 引入头文件。
- `main` 是程序入口。
- `cout` 输出，`cin` 输入。
- `return 0` 表示程序正常结束。



## 2. 标识符、关键字与命名

标识符是变量名、函数名、类名等。命名规则：

- 由字母、数字、下划线组成。
- 不能以数字开头。
- 不能使用关键字，如 `int`、`class`、`return`。

好名字能救命。`sum` 比 `s` 更清楚，`studentCount` 比 `x1` 更像人写的。



## 3. 基本数据类型

### 3.1 什么是数据类型？

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


### 3.2 基本数据类型

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


### 3.3 无符号类型

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


### 3.4 字符型详解

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


### 3.5 💡 通俗总结

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


### 3.6 常见错误

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


### 3.7 练习

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



## 4. 变量、常量与初始化

### 4.1 什么是变量？

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

### 4.2 变量的定义与初始化

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

### 4.3 变量命名规范

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

### 4.4 const 与 #define

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

### 4.5 常量的最佳实践

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

### 4.6 💡 通俗理解

> **变量 vs 常量**：
> - **变量**：像你的钱包，里面的钱可以随时存取
> - **常量**：像刻在石头上的字，改不了
>
> **什么时候用常量？**
> - 配置参数（如税率、圆周率）
> - 物理常数（如光速、重力加速度）
> - 程序配置（如窗口大小、最大连接数）

### 4.7 常见错误

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

### 4.8 练习

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



## 5. 运算符与表达式

### 5.1 运算符分类一览

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

### 5.2 算术运算符

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

### 5.3 关系运算符与逻辑运算符

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

### 5.4 赋值运算符

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

### 5.5 条件运算符（三目运算符）

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

### 5.6 位运算符（了解即可）

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

### 5.7 sizeof运算符

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

### 5.8 运算符优先级

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

### 5.9 💡 运算符记忆口诀

> **算术优先级**："乘除取余在前，加减在后"
> **逻辑优先级**："非 > 与 > 或"
> **赋值优先级**："赋值最弱，最后执行"
> **终极建议**："括号在手，天下我有"

### 5.10 常见错误

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

### 5.11 练习

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



## 6. 类型转换

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



## 7. 输入输出

```cpp
int age;
cin >> age;
cout << "age = " << age << endl;
```

格式控制常用 `<iomanip>`：

```cpp
cout << fixed << setprecision(2) << 3.14159;
```



## 8. 常见坑

- 未初始化变量：里面可能是随机值。
- 整数除法：`1 / 2` 是 `0`。
- 字符和字符串：`'A'` 是字符，`"A"` 是字符串。
- 浮点数比较：不要直接用 `a == b` 判断小数是否相等。



## 9. 本章小结

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


[返回主目录](../README.md)
