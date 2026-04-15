# 第8章 异常处理

> ⭐ C++重要特性：让程序优雅地处理"意外情况"

---

## 📋 目录

1. [异常的概念和必要性](#1-异常的概念和必要性)
2. [try-catch基本语法](#2-try-catch基本语法)
3. [throw抛出异常](#3-throw抛出异常)
4. [多个catch块和异常类型匹配](#4-多个catch块和异常类型匹配)
5. [标准异常类](#5-标准异常类)
6. [自定义异常类](#6-自定义异常类)
7. [异常规范noexcept](#7-异常规范noexcept)
8. [异常与析构函数](#8-异常与析构函数)
9. [实际应用场景](#9-实际应用场景)
10. [本章小结](#10-本章小结)

---

## 1. 异常的概念和必要性

### 1.1 什么是异常

**概念讲解：**

在现实生活中，"异常"就是**不正常的情况**。比如：
- 去医院看病，发现号已经挂完了（异常）
- 去银行取钱，发现卡被吞了（异常）
- 点外卖，发现送来的菜是辣的（异常，但你没点辣的）

在程序中，**异常**就是程序在运行过程中遇到的**意外情况**或**错误状态**。比如：
- 除法运算时，除数为0
- 访问数组时，下标越界
- 打开文件时，文件不存在
- 内存不足，无法分配

**通俗比喻：**

想象你在做饭，正常流程是：
1. 打开冰箱 → 拿出食材 → 切菜 → 炒菜 → 盛盘 → 吃饭

但如果冰箱是空的怎么办？如果你不会切菜怎么办？如果锅着火怎么办？

这些"意外情况"就是**异常**。C++的异常处理机制就是让我们能够优雅地应对这些意外。

### 1.2 为什么要处理异常

**传统错误处理方式的缺点：**

```cpp
// 方式一：使用返回值表示错误
int divide(int a, int b) {
    if (b == 0) {
        return -1;  // -1表示错误
    }
    return a / b;
}

// 问题：调用者必须检查每个返回值，很容易忘记检查
int result = divide(10, 0);  // 返回-1，但如果你不检查...
cout << result << endl;      // 输出-1，程序继续运行，可能导致更大的问题
```

```cpp
// 方式二：使用全局错误码
int g_error_code = 0;

void do_something() {
    // 设置错误码
    g_error_code = 1;
    // ...
}

// 问题：全局变量容易被遗忘，多线程环境下不安全
// 调用者很容易忘记检查错误码
```

**异常处理的优势：**

```cpp
// 方式三：使用异常处理
double divide(int a, int b) {
    if (b == 0) {
        throw "除数不能为零！";  // 抛出异常
    }
    return static_cast<double>(a) / b;
}

// 调用者可以选择是否捕获异常
// 如果不捕获，程序会自动终止，并显示错误信息
// 如果捕获，程序可以优雅地处理错误
```

**异常处理的三大优势：**

1. **分离错误处理代码**：正常代码和错误处理代码分开，代码更清晰
2. **错误传播自然**：异常可以自动向上层传播，不需要逐层检查返回值
3. **强制处理**：如果调用者不捕获异常，程序会终止，这逼着你处理错误

### 1.3 异常处理的基本思想

```
正常流程：                                异常流程：
                                          
  主函数                                    主函数
    ↓                                        ↓
  调用函数A                               调用函数A
    ↓                                        ↓
  调用函数B                               调用函数B
    ↓                                         ↓
  调用函数C                               调用函数C ──抛出异常──→ 
    ↓                                              ↓
  继续执行...                                 被捕获！
                                              ↓
                                         处理错误
                                              ↓
                                         继续执行...
```

**图解说明：**
- 正常情况下，程序从上往下顺序执行
- 当函数C发现问题时，可以"扔"出一个异常
- 异常会沿着调用链向上"飘"，直到被某个地方"接住"
- 如果没人接住，程序就会崩溃（但会显示错误信息）

### 1.4 常见的程序异常情况

```cpp
// 1. 算术异常
int a = 10, b = 0;
int c = a / b;  // 除以0！程序可能崩溃或得到错误结果

// 2. 数组越界
int arr[5] = {1, 2, 3, 4, 5};
cout << arr[10];  // 访问不存在的元素！危险！

// 3. 空指针访问
int* ptr = nullptr;
*ptr = 100;  // 解引用空指针！程序崩溃

// 4. 文件操作失败
ifstream file("nonexistent.txt");
string content;
file >> content;  // 读取失败！

// 5. 内存分配失败
int* big_array = new int[1000000000000];  // 可能分配失败
```

### 1.5 异常处理的基本框架

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 这是C++异常处理的三部曲：try、throw、catch
// 想象成：尝试(try)做某事 -> 扔出(throw)异常 -> 抓住(catch)处理

int main() {
    cout << "=== C++异常处理入门 ===" << endl;
    
    // 示例：模拟一个可能出错的场景
    int age = -5;  // 年龄不能是负数！
    
    try {
        // 在try块中，放可能出错的代码
        cout << "检查年龄..." << endl;
        
        if (age < 0) {
            // 如果发现问题，就throw（扔出）一个异常
            throw runtime_error("年龄不能为负数！");
        }
        
        // 如果没有抛出异常，继续执行这里
        cout << "年龄正常：" << age << endl;
    }
    catch (runtime_error& e) {
        // catch块负责"接住"异常并处理
        // e.what()会返回throw时传入的字符串
        cout << "出错了：" << e.what() << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== C++异常处理入门 ===
检查年龄...
出错了：年龄不能为负数！
程序结束
```

### 1.6 概念总结

| 术语 | 解释 | 类比 |
|------|------|------|
| 异常 | 程序运行时的错误情况 | 现实中的意外事件 |
| throw | 抛出异常的关键字 | 发现问题后"喊出来" |
| try | 尝试执行的代码块 | "试着做这件事" |
| catch | 捕获并处理异常 | "我来处理这个问题" |
| 异常对象 | 携带错误信息的对象 | 错误的"身份证" |

### 1.7 常见错误

**错误1：忘记处理异常**
```cpp
// 这个程序可能会崩溃
int main() {
    int* ptr = new int[1000000000000];
    *ptr = 100;  // 如果内存分配失败，ptr是nullptr，这里会崩溃
    return 0;
}
```

**错误2：异常被默默忽略**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (...) {
    // 空catch块！异常被默默忽略了
    // 这是非常不好的做法
}
```

**错误3：异常用于控制正常流程**
```cpp
// 非常糟糕的写法！
try {
    for (int i = 0; i < 100; i++) {
        if (i == 50) {
            throw 999;  // 用异常来退出循环！
        }
    }
}
catch (int e) {
    cout << "退出循环" << endl;
}
```

### 1.8 练习题

**练习1.1：理解异常概念**
什么是异常？为什么要使用异常处理？它相比传统错误处理有什么优势？

**练习1.2：识别异常情况**
以下代码中，哪些地方可能发生异常？
```cpp
int main() {
    int a = 10, b = 0;
    cout << a / b << endl;
    
    int arr[3] = {1, 2, 3};
    cout << arr[5] << endl;
    
    int* p = nullptr;
    cout << *p << endl;
    
    return 0;
}
```

**练习1.3：异常处理流程**
描述try-throw-catch的执行流程：
1. 当程序执行到throw时，会发生什么？
2. catch块什么时候会被执行？
3. 如果没有匹配的catch块，会发生什么？

---

## 2. try-catch基本语法

### 2.1 语法结构详解

**基本语法：**

```cpp
try {
    // 可能抛出异常的代码
    // 正常逻辑写在这里
}
catch (异常类型1 变量名) {
    // 处理异常类型1
}
catch (异常类型2 变量名) {
    // 处理异常类型2
}
// ... 可以有多个catch块
```

**简单记忆：**
- `try` = 尝试（做这件事）
- `catch` = 抓住（处理问题）
- 格式：`try { 可能出错 } catch { 处理错误 }`

### 2.2 最简单的异常处理

```cpp
#include <iostream>
#include <string>
using namespace std;

// 示例：用户输入验证
int main() {
    cout << "=== 最简单的异常处理 ===" << endl;
    
    int age;
    cout << "请输入您的年龄：";
    cin >> age;
    
    try {
        // 在try块中检查输入
        if (age < 0) {
            // 抛出异常：年龄不能是负数
            throw "年龄不能是负数！";
        }
        if (age > 150) {
            // 抛出异常：年龄太大了
            throw "年龄数值不合理！";
        }
        
        // 如果没有抛出异常，执行到这里
        cout << "您的年龄是：" << age << endl;
    }
    catch (const char* error_msg) {
        // 捕获字符串类型的异常
        cout << "错误：" << error_msg << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行示例1（正常输入）：**
```
=== 最简单的异常处理 ===
请输入您的年龄：25
您的年龄是：25
程序结束
```

**运行示例2（异常输入）：**
```
=== 最简单的异常处理 ===
请输入您的年龄：-5
错误：年龄不能是负数！
程序结束
```

### 2.3 try-catch的执行流程

```
开始
  ↓
进入try块
  ↓
┌─────────────────────────┐
│ 代码正常执行完毕？        │
└─────────────────────────┘
     ↓yes            ↓no（发生throw）
     ↓               ↓
离开try块        抛出异常（throw）
     ↓               ↓
  执行catch块      跳转到匹配的catch
  （如果有的话）        ↓
     ↓          执行对应的catch块
离开catch块
     ↓
程序继续
```

### 2.4 try-catch的详细执行过程

```cpp
#include <iostream>
using namespace std;

void test() {
    cout << "函数开始" << endl;
    
    try {
        cout << "try块开始" << endl;
        
        int a = 10, b = 0;
        if (b == 0) {
            cout << "发现b为0，抛出异常" << endl;
            throw runtime_error("除数不能为零");
        }
        
        cout << "try块结束（不会执行到这里）" << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获到异常：" << e.what() << endl;
    }
    
    cout << "函数结束" << endl;
}

int main() {
    cout << "=== try-catch执行流程 ===" << endl;
    test();
    cout << "main函数结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== try-catch执行流程 ===
函数开始
try块开始
发现b为0，抛出异常
捕获到异常：除数不能为零
函数结束
main函数结束
```

**流程解读：**
1. 进入try块
2. 发现b为0，执行throw
3. 立即跳转到catch块
4. 执行catch块中的处理代码
5. 继续执行try-catch之后的代码

### 2.5 try块的作用域

**关键点：try块创建一个作用域**
- 在try块中声明的变量，catch块中**不能直接访问**
- 异常处理后，程序继续从catch块之后执行

```cpp
#include <iostream>
using namespace std;

int main() {
    try {
        int x = 10;
        cout << "x = " << x << endl;
        throw runtime_error("测试异常");
    }
    catch (runtime_error& e) {
        // 注意：这里无法访问x变量！
        // cout << x << endl;  // 错误：x未声明
        cout << "捕获异常：" << e.what() << endl;
    }
    
    // x同样不能在这里访问
    return 0;
}
```

### 2.6 多个连续try-catch

```cpp
#include <iostream>
using namespace std;

void process_age(int age) {
    if (age < 0) {
        throw runtime_error("年龄不能为负数");
    }
    if (age > 150) {
        throw runtime_error("年龄不合理");
    }
    cout << "年龄正常：" << age << endl;
}

void process_balance(double balance) {
    if (balance < 0) {
        throw runtime_error("余额不能为负数");
    }
    cout << "余额正常：" << balance << endl;
}

int main() {
    cout << "=== 多个独立的try-catch ===" << endl;
    
    // 第一个try-catch
    try {
        process_age(-5);
    }
    catch (runtime_error& e) {
        cout << "年龄处理错误：" << e.what() << endl;
    }
    
    cout << "--- 继续执行 ---\n" << endl;
    
    // 第二个try-catch
    try {
        process_balance(-100);
    }
    catch (runtime_error& e) {
        cout << "余额处理错误：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== 多个独立的try-catch ===
年龄处理错误：年龄不能为负数
--- 继续执行 ---

余额处理错误：余额不能为负数

程序结束
```

### 2.7 try-catch与函数调用

```cpp
#include <iostream>
using namespace std;

// 函数可能抛出异常
double safe_divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return static_cast<double>(a) / b;
}

int main() {
    cout << "=== try-catch与函数 ===" << endl;
    
    try {
        // 调用可能抛出异常的函数
        double result = safe_divide(10, 2);
        cout << "10 / 2 = " << result << endl;
        
        // 再次调用，这次会触发异常
        result = safe_divide(10, 0);
        cout << "这里不会执行" << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    cout << "程序继续执行" << endl;
    return 0;
}
```

**运行结果：**
```
=== try-catch与函数 ===
10 / 2 = 5
捕获异常：除数不能为零
程序继续执行
```

**关键理解：**
- 异常可以跨越函数边界传播
- 如果函数内部抛出异常，会自动向外传播，直到被catch捕获
- 这大大简化了错误处理

### 2.8 嵌套的try-catch

```cpp
#include <iostream>
using namespace std;

void inner_function() {
    cout << "inner_function: 准备抛出异常" << endl;
    throw runtime_error("内部异常");
}

void outer_function() {
    try {
        cout << "outer_function: 调用inner_function" << endl;
        inner_function();
        cout << "outer_function: inner_function执行完毕" << endl;
    }
    catch (runtime_error& e) {
        cout << "outer_function: 捕获到异常-" << e.what() << endl;
        // 可以选择重新抛出
        // throw;  
    }
}

int main() {
    cout << "=== 嵌套try-catch ===" << endl;
    
    try {
        outer_function();
        cout << "main: outer_function执行完毕" << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到异常-" << e.what() << endl;
    }
    
    cout << "程序结束" << endl;
    return 0;
}
```

**运行结果：**
```
=== 嵌套try-catch ===
outer_function: 调用inner_function
inner_function: 准备抛出异常
outer_function: 捕获到异常-内部异常
main: outer_function执行完毕
程序结束
```

**解读：**
- inner_function抛出的异常被outer_function的catch捕获
- main函数中的catch块不会被执行
- 这展示了异常的传播和捕获机制

### 2.9 常见错误

**错误1：异常未被捕获，程序终止**
```cpp
#include <iostream>
using namespace std;

void cause_problem() {
    throw runtime_error("这个异常没被捕获！");
}

int main() {
    cause_problem();  // 没有try-catch包裹
    cout << "这行不会执行" << endl;  // 不会打印
    return 0;
}
```

**输出：**
```
terminate called after throwing an instance of 'std::runtime_error'
  what(): 这个异常没被捕获！
已放弃(吐核)
```

**错误2：在try块外抛出异常**
```cpp
// 这个异常不会被任何catch捕获
int x = 10;
if (x < 0) {
    throw runtime_error("x不能为负");  // 在try块外面
}

try {
    // ...
}
catch (...) {
    // 捕获不到上面的throw！
}
```

**错误3：忘记try块包裹**
```cpp
#include <iostream>
using namespace std;

int main() {
    // 错误：直接在函数中throw，但不在try块中
    throw runtime_error("直接在函数中抛出");
    // 上面的异常会导致程序崩溃
    return 0;
}
```

### 2.10 练习题

**练习2.1：基本语法练习**
写一个程序，从键盘输入两个整数，如果第二个数为0，抛出异常并提示"除数不能为零"。

**练习2.2：执行流程分析**
下面程序的输出是什么？
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "1. 开始" << endl;
    
    try {
        cout << "2. try开始" << endl;
        if (true) {
            throw runtime_error("异常!");
        }
        cout << "3. try结束" << endl;
    }
    catch (runtime_error& e) {
        cout << "4. catch: " << e.what() << endl;
    }
    
    cout << "5. 结束" << endl;
    return 0;
}
```

**练习2.3：函数中的异常**
写一个函数`check_age(int age)`，如果年龄不在0-150之间，抛出异常。在main函数中调用并捕获异常。

---

## 3. throw抛出异常

### 3.1 throw的基本语法

**语法格式：**
```cpp
throw 表达式;
// 或者
throw 异常对象;
```

**可以抛出的类型：**
- 整数：`throw 404;`
- 字符串：`throw "错误信息";`
- 标准异常对象：`throw runtime_error("错误信息");`
- 自定义异常对象
- 任何数据类型

### 3.2 抛出不同类型的异常

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

int main() {
    cout << "=== 抛出不同类型的异常 ===" << endl;
    
    // 1. 抛出整数
    try {
        throw 404;
    }
    catch (int code) {
        cout << "捕获整数异常，代码：" << code << endl;
    }
    
    // 2. 抛出字符串
    try {
        throw "文件未找到";
    }
    catch (const char* msg) {
        cout << "捕获字符串异常：" << msg << endl;
    }
    
    // 3. 抛出string对象
    try {
        throw string("数据库连接失败");
    }
    catch (string& msg) {
        cout << "捕获string异常：" << msg << endl;
    }
    
    // 4. 抛出标准异常对象
    try {
        throw runtime_error("运行时错误示例");
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 抛出不同类型的异常 ===
捕获整数异常，代码：404
捕获字符串异常：文件未找到
捕获string异常：数据库连接失败
捕获runtime_error：运行时错误示例
```

### 3.3 异常的传播机制

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 异常可以自动向上传播
void level3() {
    cout << "level3: 准备抛出异常" << endl;
    throw runtime_error("3层深度的异常");
}

void level2() {
    cout << "level2: 调用level3" << endl;
    level3();
    cout << "level2: level3返回（不会执行）" << endl;
}

void level1() {
    cout << "level1: 调用level2" << endl;
    level2();
    cout << "level1: level2返回（不会执行）" << endl;
}

int main() {
    cout << "=== 异常的传播 ===" << endl;
    
    try {
        level1();
        cout << "main: level1返回（不会执行）" << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到异常-" << e.what() << endl;
    }
    
    cout << "程序继续执行" << endl;
    return 0;
}
```

**运行结果：**
```
=== 异常的传播 ===
level1: 调用level2
level2: 调用level3
level3: 准备抛出异常
main: 捕获到异常-3层深度的异常
程序继续执行
```

**传播机制图解：**
```
调用栈（从上到下）：

main()
  ↓
level1()
  ↓
level2()
  ↓
level3() ──throw──→ 异常对象

异常沿着调用链向上传播：
level3() → level2() → level1() → main()
                                    ↓
                              被catch捕获！
```

### 3.4 重新抛出异常

**场景：** 内层catch只能处理部分异常，剩下的交给外层处理

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void process_file(const string& filename) {
    try {
        if (filename.empty()) {
            throw runtime_error("文件名为空");
        }
        if (filename == "no_permission.txt") {
            throw runtime_error("没有文件访问权限");
        }
        // 正常处理文件...
        cout << "成功处理文件：" << filename << endl;
    }
    catch (runtime_error& e) {
        // 只能处理部分问题
        if (filename.empty()) {
            cout << "本地处理空文件名异常" << endl;
            // 空文件名问题已处理，不再重新抛出
        }
        else {
            // 其他问题交给上层处理
            cout << "本层无法处理，重新抛出" << endl;
            throw;  // 关键：重新抛出当前异常
        }
    }
}

int main() {
    cout << "=== 重新抛出异常 ===" << endl;
    
    // 测试1：空文件名
    cout << "\n--- 测试1：空文件名 ---" << endl;
    try {
        process_file("");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    // 测试2：权限问题
    cout << "\n--- 测试2：权限问题 ---" << endl;
    try {
        process_file("no_permission.txt");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    // 测试3：正常文件
    cout << "\n--- 测试3：正常文件 ---" << endl;
    try {
        process_file("data.txt");
    }
    catch (runtime_error& e) {
        cout << "main: 捕获-" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 重新抛出异常 ===

--- 测试1：空文件名 ---
本地处理空文件名异常

--- 测试2：权限问题 ---
本层无法处理，重新抛出
main: 捕获-没有文件访问权限

--- 测试3：正常文件 ---
成功处理文件：data.txt
```

### 3.5 throw的位置与时机

**何时抛出异常：**

1. **函数参数不合法时**
```cpp
void set_age(int age) {
    if (age < 0 || age > 150) {
        throw invalid_argument("年龄必须在0-150之间");
    }
    // ...
}
```

2. **操作失败时**
```cpp
int* allocate_memory(size_t size) {
    if (size == 0) {
        throw invalid_argument("分配内存大小不能为0");
    }
    int* ptr = new int[size];
    if (!ptr) {
        throw bad_alloc();
    }
    return ptr;
}
```

3. **业务逻辑违反时**
```cpp
void withdraw(double balance, double amount) {
    if (amount > balance) {
        throw runtime_error("余额不足，无法取款");
    }
    // ...
}
```

### 3.6 异常抛出与返回值的对比

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 方式1：使用返回值表示错误
int divide_with_return(int a, int b, int& result) {
    if (b == 0) {
        return -1;  // -1表示失败
    }
    result = a / b;
    return 0;  // 0表示成功
}

// 方式2：使用异常
double divide_with_exception(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return static_cast<double>(a) / b;
}

int main() {
    cout << "=== 异常 vs 返回值 ===" << endl;
    
    // 使用返回值
    int result1;
    if (divide_with_return(10, 2, result1) == 0) {
        cout << "方式1成功：10 / 2 = " << result1 << endl;
    }
    
    if (divide_with_return(10, 0, result1) == -1) {
        cout << "方式1失败：除数为0" << endl;
    }
    
    // 使用异常
    try {
        double result2 = divide_with_exception(10, 2);
        cout << "方式2成功：10 / 2 = " << result2 << endl;
        
        result2 = divide_with_exception(10, 0);
    }
    catch (runtime_error& e) {
        cout << "方式2失败：" << e.what() << endl;
    }
    
    return 0;
}
```

**两种方式的对比：**

| 方面 | 返回值方式 | 异常方式 |
|------|-----------|----------|
| 调用者忘记检查 | 可能导致错误传播 | 程序会崩溃（强制检查） |
| 代码复杂度 | 需要逐层检查返回值 | 可以集中处理 |
| 适用场景 | 常见错误，可预期 | 异常情况，不可预期 |
| 性能影响 | 几乎没有 | 抛出时有一定开销 |

### 3.7 throw的常见用法

**用法1：简单抛出字符串**
```cpp
throw "错误信息";
```

**用法2：抛出标准异常**
```cpp
throw runtime_error("运行时错误");
throw invalid_argument("参数无效");
throw out_of_range("越界访问");
throw length_error("长度错误");
```

**用法3：抛出自定义类型**
```cpp
class MyException {
public:
    string message;
    int code;
    MyException(const string& msg, int c) : message(msg), code(c) {}
};

throw MyException("自定义错误", 1001);
```

### 3.8 常见错误

**错误1：内存泄漏（在抛出异常前没有清理）**
```cpp
void bad_function() {
    int* data = new int[1000];  // 分配内存
    // 如果这里抛出异常，data指向的内存永远不会释放！
    if (some_error) {
        throw runtime_error("错误");
    }
    delete[] data;  // 不会执行到
}
```

**错误2：抛出指针（而不是对象）**
```cpp
void bad_function() {
    int* p = new int(10);
    throw p;  // 错误！应该抛对象，不应该抛指针
    // 如果catch块没有正确delete，会导致内存泄漏
}
```

**正确做法：**
```cpp
void good_function() {
    try {
        // 分配资源
        int* data = new int[1000];
        // 使用资源
        if (error_condition) {
            delete[] data;  // 先清理
            throw runtime_error("错误");  // 再抛出
        }
        // 正常清理
        delete[] data;
    }
    catch (...) {
        // 或者使用智能指针
    }
}
```

### 3.9 练习题

**练习3.1：抛出基本类型**
写一个函数`max_of_three(int a, int b, int c)`，返回三个数中的最大值。如果三个数都是负数，抛出异常提示"所有数都是负数"。

**练习3.2：异常传播**
写三个函数A→B→C，在C中抛出异常，观察异常如何传播到A，并在A中捕获。

**练习3.3：重新抛出**
写一个函数处理用户输入，在try块中捕获并处理部分异常，对无法处理的异常进行重新抛出。

**练习3.4：选择异常类型**
为以下场景选择合适的异常类型：
1. 数组下标越界
2. 文件不存在
3. 内存分配失败
4. 参数格式错误
5. 余额不足

---

## 4. 多个catch块和异常类型匹配

### 4.1 多个catch块的概念

**为什么需要多个catch块？**

不同的错误需要不同的处理方式。比如：
- 文件找不到 → 提示用户选择其他文件
- 内存不足 → 尝试清理内存或退出程序
- 网络断开 → 尝试重新连接

```cpp
try {
    // 各种可能出错的操作
}
catch (文件错误) {
    // 处理文件相关错误
}
catch (内存错误) {
    // 处理内存相关错误
}
catch (网络错误) {
    // 处理网络相关错误
}
```

### 4.2 基本用法示例

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

void handle_error(int error_type) {
    try {
        switch (error_type) {
            case 1:
                throw runtime_error("运行时错误");
            case 2:
                throw invalid_argument("参数错误");
            case 3:
                throw out_of_range("越界错误");
            case 4:
                throw string("字符串错误");
            default:
                cout << "没有错误" << endl;
        }
    }
    catch (runtime_error& e) {
        cout << "捕获运行时错误：" << e.what() << endl;
        cout << "建议：检查程序逻辑" << endl;
    }
    catch (invalid_argument& e) {
        cout << "捕获参数错误：" << e.what() << endl;
        cout << "建议：检查输入参数" << endl;
    }
    catch (out_of_range& e) {
        cout << "捕获越界错误：" << e.what() << endl;
        cout << "建议：检查数组或容器大小" << endl;
    }
    catch (string& e) {
        cout << "捕获字符串异常：" << e << endl;
        cout << "建议：检查字符串处理" << endl;
    }
}

int main() {
    cout << "=== 多个catch块 ===" << endl;
    
    for (int i = 0; i <= 4; i++) {
        cout << "\n--- 测试类型" << i << " ---" << endl;
        handle_error(i);
    }
    
    return 0;
}
```

**运行结果：**
```
=== 多个catch块 ===

--- 测试类型0 ---
没有错误

--- 测试类型1 ---
捕获运行时错误：运行时错误
建议：检查程序逻辑

--- 测试类型2 ---
捕获参数错误：参数错误
建议：检查输入参数

--- 测试类型3 ---
捕获越界错误：越界错误
建议：检查数组或容器大小

--- 测试类型4 ---
捕获字符串异常：字符串错误
建议：检查字符串处理
```

### 4.3 异常类型匹配规则

**匹配规则（从上到下匹配）：**

1. **精确匹配**：类型完全相同
2. **公有继承匹配**：抛出的是catch声明类型的派生类
3. **类型转换匹配**：
   - 非const → const
   - 派生类 → 基类
   - 数组 → 指针（某些情况下）

**匹配示例：**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 异常类层次结构：
// exception (基类)
//   ├── runtime_error (派生类)
//   │     ├── overflow_error
//   │     └── underflow_error
//   └── logic_error (派生类)
//         ├── invalid_argument
//         └── out_of_range

void test_match(int type) {
    try {
        switch (type) {
            case 1:
                throw runtime_error("运行时错误");
            case 2:
                throw invalid_argument("无效参数");
            case 3:
                throw out_of_range("越界");
            case 4:
                throw 404;  // int类型
            default:
                throw exception();  // 基类
        }
    }
    // 匹配顺序很重要！
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (out_of_range& e) {
        cout << "捕获out_of_range：" << e.what() << endl;
    }
    catch (runtime_error& e) {  // 这个会匹配case 1
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    catch (logic_error& e) {  // 不会匹配到，因为上面的invalid_argument/out_of_range是更具体的
        cout << "捕获logic_error：" << e.what() << endl;
    }
    catch (exception& e) {
        cout << "捕获exception：" << e.what() << endl;
    }
    catch (...) {
        cout << "捕获其他异常" << endl;
    }
}
```

### 4.4 异常匹配顺序的重要性

**错误示例：顺序不当**

```cpp
try {
    throw invalid_argument("测试");
}
catch (exception& e) {  // 这个会匹配所有派生类
    cout << "捕获exception" << endl;
}
catch (invalid_argument& e) {  // 永远不会执行！
    cout << "捕获invalid_argument" << endl;
}
```

**正确示例：从小范围到大范围**

```cpp
try {
    throw invalid_argument("测试");
}
// 正确顺序：先捕获具体的，再捕获一般的
catch (invalid_argument& e) {  // 先匹配这个
    cout << "捕获具体的invalid_argument" << endl;
}
catch (exception& e) {  // 最后匹配基类
    cout << "捕获通用的exception" << endl;
}
```

### 4.5 catch(...)通配符

**`catch(...)`可以捕获所有异常**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void universal_catch_demo() {
    try {
        // 可能抛出任何类型的异常
        throw runtime_error("测试");
    }
    catch (...) {
        // 捕获所有异常，但不区分类型
        cout << "捕获到某种异常，但不知道是什么类型" << endl;
    }
}

void cleanup_demo() {
    try {
        // 需要清理资源的操作
        throw runtime_error("操作失败");
    }
    catch (...) {
        cout << "发生异常，执行清理工作" << endl;
        // 清理代码...
        throw;  // 通常会重新抛出
    }
}

int main() {
    cout << "=== catch(...)通配符 ===" << endl;
    
    universal_catch_demo();
    
    cout << "\n--- 清理示例 ---" << endl;
    try {
        cleanup_demo();
    }
    catch (...) {
        cout << "main: 再次捕获" << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== catch(...)通配符 ===
捕获到某种异常，但不知道是什么类型

--- 清理示例 ---
发生异常，执行清理工作
main: 再次捕获
```

### 4.6 异常重新抛出与类型

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void process_with_logging() {
    try {
        // 模拟各种错误
        throw invalid_argument("参数错误");
        // throw runtime_error("运行时错误");
    }
    catch (invalid_argument& e) {
        cout << "记录日志：参数错误 - " << e.what() << endl;
        // 重新抛出，保持原类型
        throw;
    }
    catch (runtime_error& e) {
        cout << "记录日志：运行时错误 - " << e.what() << endl;
        throw;
    }
}

int main() {
    cout << "=== 重新抛出保持类型 ===" << endl;
    
    try {
        process_with_logging();
    }
    catch (invalid_argument& e) {
        cout << "main: 捕获到invalid_argument - " << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "main: 捕获到runtime_error - " << e.what() << endl;
    }
    
    return 0;
}
```

### 4.7 实际应用：不同错误不同处理

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>
#include <string>
using namespace std;

// 模拟文件操作，可能遇到各种错误
void process_file(const string& filename) {
    try {
        if (filename.empty()) {
            throw invalid_argument("文件名为空");
        }
        
        if (filename == "not_found.txt") {
            throw runtime_error("文件不存在");
        }
        
        if (filename == "no_permission.txt") {
            throw runtime_error("没有访问权限");
        }
        
        if (filename == "corrupted.txt") {
            throw runtime_error("文件已损坏");
        }
        
        cout << "成功处理文件：" << filename << endl;
    }
    catch (invalid_argument& e) {
        // 参数错误：让用户重新输入
        cout << "[参数错误] " << e.what() << endl;
        cout << "请重新输入有效的文件名" << endl;
    }
    catch (runtime_error& e) {
        // 运行时错误：记录并尝试恢复
        cout << "[运行时错误] " << e.what() << endl;
        if (string(e.what()).find("不存在") != string::npos) {
            cout << "建议：检查文件路径是否正确" << endl;
        }
        else if (string(e.what()).find("权限") != string::npos) {
            cout << "建议：联系管理员获取权限" << endl;
        }
        else if (string(e.what()).find("损坏") != string::npos) {
            cout << "建议：尝试恢复或使用备份文件" << endl;
        }
    }
}

int main() {
    cout << "=== 不同错误不同处理 ===" << endl;
    
    test_file("data.txt");  // 成功
    test_file("");          // 参数错误
    test_file("not_found.txt");    // 文件不存在
    test_file("no_permission.txt"); // 权限问题
    test_file("corrupted.txt");    // 文件损坏
    
    return 0;
}
```

### 4.8 常见错误

**错误1：catch顺序不当**
```cpp
// 错误：基类放在前面
try {
    throw invalid_argument("测试");
}
catch (exception& e) {  // 所有派生类都会被这个捕获
    cout << "exception" << endl;
}
catch (invalid_argument& e) {  // 永远不会执行
    cout << "invalid_argument" << endl;
}

// 正确：派生类放在前面
try {
    throw invalid_argument("测试");
}
catch (invalid_argument& e) {  // 先匹配这个
    cout << "invalid_argument" << endl;
}
catch (exception& e) {  // 作为后备
    cout << "exception" << endl;
}
```

**错误2：空catch块**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (...) {
    // 空catch块！异常被完全忽略
    // 这是非常糟糕的做法
}
```

**正确做法：**
```cpp
try {
    throw runtime_error("错误");
}
catch (...) {
    cerr << "发生未知错误" << endl;  // 至少记录日志
    // 或者重新抛出
    throw;
}
```

**错误3：按值捕获基类**
```cpp
// 可能导致对象切片问题
try {
    throw runtime_error("错误");
}
catch (exception e) {  // 按值捕获，可能丢失派生类信息
    // ...
}
```

**正确做法：**
```cpp
try {
    throw runtime_error("错误");
}
catch (exception& e) {  // 按引用捕获
    // ...
}
```

### 4.9 练习题

**练习4.1：异常匹配**
```cpp
try {
    throw out_of_range("越界");
}
catch (invalid_argument& e) {  // 会匹配吗？
    cout << "invalid_argument" << endl;
}
catch (out_of_range& e) {  // 会匹配吗？
    cout << "out_of_range" << endl;
}
catch (exception& e) {  // 会匹配吗？
    cout << "exception" << endl;
}
```
以上代码的输出是什么？

**练习4.2：异常处理顺序**
创建一个包含多个catch块的函数，分别捕获：invalid_argument、logic_error、runtime_error、exception。说明为什么要按这个顺序排列。

**练习4.3：实现错误处理系统**
写一个计算器程序，处理以下错误：
1. 除数为0
2. 平方根的负数参数
3. 对数的小于等于0参数
4. 超出范围的选项

---

## 5. 标准异常类

### 5.1 标准异常类层次结构

```
std::exception (所有异常的基类)
    │
    ├── std::bad_alloc          // 内存分配失败
    ├── std::bad_cast           // 动态类型转换失败
    ├── std::bad_typeid         // typeid(nullptr)
    │
    ├── std::logic_error        // 程序逻辑错误（可预知）
    │       ├── std::invalid_argument    // 无效参数
    │       ├── std::domain_error         // 数学定义域错误
    │       ├── std::length_error         // 长度超出限制
    │       └── std::out_of_range          // 越界访问
    │
    └── std::runtime_error      // 运行时错误（不可预知）
            ├── std::range_error         // 数值范围错误
            ├── std::overflow_error      // 算术上溢
            ├── std::underflow_error     // 算术下溢
            └── std::regex_error         // 正则表达式错误
```

### 5.2 exception基类

```cpp
#include <iostream>
#include <exception>
using namespace std;

int main() {
    cout << "=== exception基类 ===" << endl;
    
    try {
        throw exception();
    }
    catch (exception& e) {
        cout << "捕获exception" << endl;
        cout << "异常信息：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== exception基类 ===
捕获exception
异常信息：std::exception
```

### 5.3 logic_error及其派生类

**logic_error：程序员的错误，可通过检查代码避免**

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

// 1. invalid_argument - 无效参数
void validate_age(int age) {
    if (age < 0 || age > 150) {
        throw invalid_argument("年龄必须在0-150之间");
    }
    cout << "年龄有效：" << age << endl;
}

// 2. domain_error - 数学定义域错误
double safe_sqrt(double x) {
    if (x < 0) {
        throw domain_error("平方根参数不能为负数");
    }
    return sqrt(x);
}

// 3. length_error - 长度超出限制
void add_to_vector(vector<int>& v) {
    if (v.size() >= 100) {
        throw length_error("向量已满，无法添加更多元素");
    }
    v.push_back(100);
}

// 4. out_of_range - 越界访问
int get_element(const vector<int>& v, size_t index) {
    if (index >= v.size()) {
        throw out_of_range("索引" + to_string(index) + "超出范围[0-" + to_string(v.size()-1) + "]");
    }
    return v[index];
}

int main() {
    cout << "=== logic_error及其派生类 ===" << endl;
    
    // 测试invalid_argument
    cout << "\n--- invalid_argument ---" << endl;
    try {
        validate_age(-5);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    
    // 测试domain_error
    cout << "\n--- domain_error ---" << endl;
    try {
        double result = safe_sqrt(-16);
    }
    catch (domain_error& e) {
        cout << "捕获domain_error：" << e.what() << endl;
    }
    
    // 测试length_error
    cout << "\n--- length_error ---" << endl;
    try {
        vector<int> v(100, 0);
        add_to_vector(v);
    }
    catch (length_error& e) {
        cout << "捕获length_error：" << e.what() << endl;
    }
    
    // 测试out_of_range
    cout << "\n--- out_of_range ---" << endl;
    try {
        vector<int> v = {1, 2, 3};
        int x = get_element(v, 10);
    }
    catch (out_of_range& e) {
        cout << "捕获out_of_range：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== logic_error及其派生类 ===

--- invalid_argument ---
捕获invalid_argument：年龄必须在0-150之间

--- domain_error ---
捕获domain_error：平方根参数不能为负数

--- length_error ---
捕获length_error：向量已满，无法添加更多元素

--- out_of_range ---
捕获out_of_range：索引10超出范围[0-2]
```

### 5.4 runtime_error及其派生类

**runtime_error：运行时发生的错误，不可预知**

```cpp
#include <iostream>
#include <stdexcept>
#include <fstream>
#include <string>
using namespace std;

// 1. range_error - 数值范围错误
double calculate(double base, int exponent) {
    if (exponent > 1000) {
        throw range_error("指数太大，结果超出范围");
    }
    double result = 1;
    for (int i = 0; i < exponent; i++) {
        result *= base;
    }
    return result;
}

// 2. overflow_error - 算术上溢
int safe_multiply(int a, int b) {
    if (a > 1000000 || b > 1000000) {
        throw overflow_error("乘法可能导致溢出");
    }
    return a * b;
}

// 3. underflow_error - 算术下溢
double very_small_division() {
    throw underflow_error("结果太小，接近下溢");
}

// 4. 文件相关运行时错误
void read_file_content(const string& filename) {
    ifstream file(filename);
    if (!file.is_open()) {
        throw runtime_error("无法打开文件：" + filename);
    }
    string line;
    while (getline(file, line)) {
        cout << line << endl;
    }
}

int main() {
    cout << "=== runtime_error及其派生类 ===" << endl;
    
    // 测试range_error
    cout << "\n--- range_error ---" << endl;
    try {
        calculate(2, 2000);
    }
    catch (range_error& e) {
        cout << "捕获range_error：" << e.what() << endl;
    }
    
    // 测试overflow_error
    cout << "\n--- overflow_error ---" << endl;
    try {
        int result = safe_multiply(9999999, 9999999);
    }
    catch (overflow_error& e) {
        cout << "捕获overflow_error：" << e.what() << endl;
    }
    
    // 测试underflow_error
    cout << "\n--- underflow_error ---" << endl;
    try {
        very_small_division();
    }
    catch (underflow_error& e) {
        cout << "捕获underflow_error：" << e.what() << endl;
    }
    
    // 测试文件错误
    cout << "\n--- 文件运行时错误 ---" << endl;
    try {
        read_file_content("nonexistent_file.txt");
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== runtime_error及其派生类 ===

--- range_error ---
捕获range_error：指数太大，结果超出范围

--- overflow_error ---
捕获runtime_error：乘法可能导致溢出

--- underflow_error ---
捕获underflow_error：结果太小，接近下溢

--- 文件运行时错误 ---
捕获runtime_error：无法打开文件：nonexistent_file.txt
```

### 5.5 bad_alloc异常

**内存分配失败时抛出**

```cpp
#include <iostream>
#include <new>
using namespace std;

int main() {
    cout << "=== bad_alloc异常 ===" << endl;
    
    try {
        // 尝试分配一个巨大的内存块
        // 可能会失败，抛出bad_alloc异常
        cout << "尝试分配超大内存..." << endl;
        size_t huge_size = SIZE_MAX / sizeof(int);
        int* arr = new int[huge_size];
        cout << "分配成功（不太可能）" << endl;
        delete[] arr;
    }
    catch (bad_alloc& e) {
        cout << "捕获bad_alloc：" << e.what() << endl;
        cout << "内存分配失败！建议：" << endl;
        cout << "1. 减少请求的内存大小" << endl;
        cout << "2. 释放不再使用的内存" << endl;
        cout << "3. 检查内存泄漏" << endl;
    }
    
    cout << "\n程序继续执行" << endl;
    return 0;
}
```

### 5.6 标准异常使用场景总结

```cpp
#include <iostream>
#include <stdexcept>
#include <vector>
#include <string>
using namespace std;

// 综合示例：银行账户系统
class BankAccount {
private:
    string account_holder;
    double balance;
    static const double MIN_BALANCE = 0.0;
    static const double MAX_BALANCE = 1000000.0;
    
public:
    BankAccount(const string& name, double initial) 
        : account_holder(name), balance(0) {
        // 验证账户名
        if (name.empty()) {
            throw invalid_argument("账户名不能为空");
        }
        // 验证初始金额
        if (initial < MIN_BALANCE) {
            throw invalid_argument("初始金额不能为负数");
        }
        if (initial > MAX_BALANCE) {
            throw overflow_error("初始金额超出最大限制");
        }
        balance = initial;
    }
    
    void deposit(double amount) {
        if (amount < 0) {
            throw invalid_argument("存款金额不能为负数");
        }
        if (balance + amount > MAX_BALANCE) {
            throw overflow_error("存款后余额超出最大限制");
        }
        balance += amount;
    }
    
    void withdraw(double amount) {
        if (amount < 0) {
            throw invalid_argument("取款金额不能为负数");
        }
        if (amount > balance) {
            throw runtime_error("余额不足");
        }
        if (balance - amount < MIN_BALANCE) {
            throw runtime_error("取款后余额不能为负");
        }
        balance -= amount;
    }
    
    double get_balance() const {
        return balance;
    }
};

int main() {
    cout << "=== 银行账户异常处理示例 ===" << endl;
    
    BankAccount* account = nullptr;
    
    // 测试各种异常
    try {
        // 1. 无效账户名
        cout << "\n--- 测试1：无效账户名 ---" << endl;
        account = new BankAccount("", 1000);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    
    try {
        // 2. 有效账户
        cout << "\n--- 测试2：创建有效账户 ---" << endl;
        account = new BankAccount("张三", 1000);
        cout << "账户创建成功，余额：" << account->get_balance() << endl;
        
        // 3. 负数存款
        cout << "\n--- 测试3：负数存款 ---" << endl;
        account->deposit(-100);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    catch (overflow_error& e) {
        cout << "捕获overflow_error：" << e.what() << endl;
    }
    
    try {
        // 4. 余额不足
        cout << "\n--- 测试4：余额不足取款 ---" << endl;
        account->withdraw(5000);
    }
    catch (invalid_argument& e) {
        cout << "捕获invalid_argument：" << e.what() << endl;
    }
    catch (runtime_error& e) {
        cout << "捕获runtime_error：" << e.what() << endl;
    }
    
    try {
        // 5. 成功操作
        cout << "\n--- 测试5：正常操作 ---" << endl;
        account->deposit(500);
        account->withdraw(300);
        cout << "当前余额：" << account->get_balance() << endl;
    }
    catch (exception& e) {
        cout << "捕获其他异常：" << e.what() << endl;
    }
    
    delete account;
    cout << "\n程序结束" << endl;
    
    return 0;
}
```

### 5.7 常见错误

**错误1：捕获了异常但没有处理**
```cpp
try {
    // 可能抛出异常的代码
    throw runtime_error("错误");
}
catch (runtime_error& e) {
    // 捕获了，但什么都没做
    // 异常被忽略了！
}
```

**错误2：捕获错误的异常类型**
```cpp
try {
    throw invalid_argument("参数错误");
}
catch (out_of_range& e) {  // 类型不匹配！不会捕获到
    // 这段代码不会执行
}
```

**错误3：异常被默默转换**
```cpp
try {
    throw runtime_error("运行时错误");
}
catch (exception& e) {  // 可以匹配，基类可以捕获派生类
    // 但e.what()返回的是派生类的消息
    cout << e.what() << endl;  // 仍然输出"运行时错误"
}
```

### 5.8 练习题

**练习5.1：匹配异常类型**
为以下场景选择最合适的异常类型：
1. `vector<int> v; v[100]` 访问
2. `sqrt(-16)` 函数调用
3. `new int[999999999999]` 内存分配
4. 函数参数为nullptr
5. 整数相除结果溢出

**练习5.2：使用标准异常改写计算器**
用标准异常改写第4章的计算器程序：
- 无效选择 → invalid_argument
- 除数为0 → invalid_argument
- 越界 → out_of_range
- 结果溢出 → overflow_error

**练习5.3：异常层次结构**
画出标准异常类的层次结构，并说明logic_error和runtime_error的区别。

---

## 6. 自定义异常类

### 6.1 为什么需要自定义异常

**标准异常的局限性：**
- 无法携带业务特定的错误信息
- 无法定义业务相关的错误码
- 异常类型不够精确

**自定义异常的优势：**
- 可以携带丰富的错误信息
- 可以定义业务相关的属性
- 可以建立应用程序特定的异常层次

### 6.2 最简单的自定义异常

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 最简单的自定义异常：继承exception基类
class MyException : public exception {
public:
    // 重写what()方法，返回错误信息
    const char* what() const noexcept override {
        return "我的自定义异常";
    }
};

int main() {
    cout << "=== 简单的自定义异常 ===" << endl;
    
    try {
        throw MyException();
    }
    catch (exception& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    return 0;
}
```

### 6.3 带有构造参数的自定义异常

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 带错误消息的自定义异常
class BusinessException : public exception {
private:
    string message;  // 存储错误消息
    int error_code;  // 存储错误码
    
public:
    // 构造函数
    BusinessException(const string& msg, int code = 0) 
        : message(msg), error_code(code) {}
    
    // 重写what()方法
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    // 获取错误码
    int get_error_code() const {
        return error_code;
    }
    
    // 获取错误消息
    string get_message() const {
        return message;
    }
};

int main() {
    cout << "=== 带参数的自定义异常 ===" << endl;
    
    try {
        int user_id = -1;
        if (user_id < 0) {
            throw BusinessException("用户ID不能为负数", 1001);
        }
    }
    catch (BusinessException& e) {
        cout << "错误码：" << e.get_error_code() << endl;
        cout << "错误信息：" << e.get_message() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 带参数的自定义异常 ===
错误码：1001
错误信息：用户ID不能为负数
```

### 6.4 建立异常类层次

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <vector>
using namespace std;

// 基类：应用程序异常
class AppException : public exception {
protected:
    string message;
    string timestamp;
    
public:
    AppException(const string& msg) : message(msg) {
        // 简单的时间戳（实际应用中使用chrono）
        timestamp = "2024-01-01 12:00:00";
    }
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    string get_timestamp() const { return timestamp; }
    virtual string get_type() const { return "AppException"; }
};

// 业务异常基类
class BusinessException : public AppException {
protected:
    int error_code;
    
public:
    BusinessException(const string& msg, int code) 
        : AppException(msg), error_code(code) {}
    
    int get_error_code() const { return error_code; }
    string get_type() const override { return "BusinessException"; }
};

// 数据库异常
class DatabaseException : public AppException {
public:
    DatabaseException(const string& msg) : AppException(msg) {}
    string get_type() const override { return "DatabaseException"; }
};

// 用户相关异常
class UserException : public BusinessException {
public:
    UserException(const string& msg, int code) : BusinessException(msg, code) {}
    string get_type() const override { return "UserException"; }
};

// 订单相关异常
class OrderException : public BusinessException {
public:
    OrderException(const string& msg, int code) : BusinessException(msg, code) {}
    string get_type() const override { return "OrderException"; }
};

// 网络异常
class NetworkException : public AppException {
public:
    NetworkException(const string& msg) : AppException(msg) {}
    string get_type() const override { return "NetworkException"; }
};

// 测试函数
void test_user_operations() {
    try {
        throw UserException("用户不存在", 1001);
    }
    catch (UserException& e) {
        cout << "[" << e.get_type() << "] " << e.what() 
             << " (错误码: " << e.get_error_code() << ")" << endl;
    }
}

void test_order_operations() {
    try {
        throw OrderException("订单已过期", 2001);
    }
    catch (OrderException& e) {
        cout << "[" << e.get_type() << "] " << e.what() 
             << " (错误码: " << e.get_error_code() << ")" << endl;
    }
}

void test_database() {
    try {
        throw DatabaseException("无法连接到数据库");
    }
    catch (DatabaseException& e) {
        cout << "[" << e.get_type() << "] " << e.what() << endl;
    }
}

int main() {
    cout << "=== 自定义异常层次 ===" << endl;
    
    test_user_operations();
    test_order_operations();
    test_database();
    
    // 使用基类捕获所有派生异常
    cout << "\n--- 使用基类统一捕获 ---" << endl;
    try {
        throw OrderException("库存不足", 2002);
    }
    catch (AppException& e) {
        cout << "捕获到应用异常：" << e.get_type() << " - " << e.what() << endl;
    }
    
    return 0;
}
```

**运行结果：**
```
=== 自定义异常层次 ===
[UserException] 用户不存在 (错误码: 1001)
[OrderException] 订单已过期 (错误码: 2001)
[DatabaseException] 无法连接到数据库
[AppException] 库存不足
```

### 6.5 完整的异常类设计示例

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <vector>
#include <sstream>
using namespace std;

// ========== 异常错误码定义 ==========
namespace ErrorCode {
    const int SUCCESS = 0;
    
    // 用户相关错误码 (1000-1999)
    namespace User {
        const int NOT_FOUND = 1001;
        const int ALREADY_EXISTS = 1002;
        const int INVALID_PASSWORD = 1003;
        const int PERMISSION_DENIED = 1004;
    }
    
    // 订单相关错误码 (2000-2999)
    namespace Order {
        const int NOT_FOUND = 2001;
        const int ALREADY_PAID = 2002;
        const int OUT_OF_STOCK = 2003;
        const int PRICE_CHANGED = 2004;
    }
    
    // 系统相关错误码 (9000-9999)
    namespace System {
        const int DATABASE_ERROR = 9001;
        const int NETWORK_ERROR = 9002;
        const int UNKNOWN_ERROR = 9999;
    }
}

// ========== 自定义异常基类 ==========
class AppException : public exception {
protected:
    int code;
    string message;
    string details;
    
public:
    AppException(int c, const string& msg, const string& det = "")
        : code(c), message(msg), details(det) {}
    
    const char* what() const noexcept override {
        // 返回完整信息
        static string buffer;
        stringstream ss;
        ss << "[错误码:" << code << "] " << message;
        if (!details.empty()) {
            ss << " (" << details << ")";
        }
        buffer = ss.str();
        return buffer.c_str();
    }
    
    int get_code() const { return code; }
    string get_message() const { return message; }
    string get_details() const { return details; }
};

// ========== 用户异常 ==========
class UserException : public AppException {
public:
    UserException(int code, const string& msg, const string& details = "")
        : AppException(code, msg, details) {}
};

// ========== 订单异常 ==========
class OrderException : public AppException {
private:
    string order_id;
    
public:
    OrderException(int code, const string& msg, const string& oid = "",
                   const string& details = "")
        : AppException(code, msg, details), order_id(oid) {}
    
    string get_order_id() const { return order_id; }
};

// ========== 异常处理函数模板 ==========
void handle_exception(AppException& e) {
    cerr << "异常发生：" << e.what() << endl;
    
    // 根据错误码分类处理
    if (e.get_code() >= ErrorCode::User::NOT_FOUND && 
        e.get_code() < ErrorCode::User::NOT_FOUND + 1000) {
        cerr << "建议：检查用户相关操作" << endl;
    }
    else if (e.get_code() >= ErrorCode::Order::NOT_FOUND && 
             e.get_code() < ErrorCode::Order::NOT_FOUND + 1000) {
        cerr << "建议：检查订单相关操作" << endl;
    }
}

// ========== 测试 ==========
class User {
public:
    static User find_by_id(int id) {
        if (id <= 0) {
            throw UserException(ErrorCode::User::NOT_FOUND, 
                               "用户不存在", 
                               "用户ID: " + to_string(id));
        }
        // 模拟找到用户
        return User();
    }
};

class Order {
public:
    static void pay(const string& order_id, double amount) {
        if (order_id.empty()) {
            throw OrderException(ErrorCode::Order::NOT_FOUND,
                               "订单号不能为空");
        }
        if (amount <= 0) {
            throw OrderException(ErrorCode::Order::PRICE_CHANGED,
                               "支付金额无效",
                               "订单ID: " + order_id);
        }
        // 模拟支付
        cout << "订单 " << order_id << " 支付成功，金额：" << amount << endl;
    }
};

int main() {
    cout << "=== 完整的异常类设计 ===" << endl;
    
    // 测试用户异常
    cout << "\n--- 测试用户异常 ---" << endl;
    try {
        User::find_by_id(-1);
    }
    catch (UserException& e) {
        handle_exception(e);
    }
    
    // 测试订单异常
    cout << "\n--- 测试订单异常 ---" << endl;
    try {
        Order::pay("", 100);
    }
    catch (OrderException& e) {
        handle_exception(e);
    }
    
    try {
        Order::pay("ORDER123", -50);
    }
    catch (OrderException& e) {
        handle_exception(e);
    }
    
    return 0;
}
```

### 6.6 自定义异常的构造函数设计

```cpp
#include <iostream>
#include <exception>
#include <string>
using namespace std;

// 方法1：简单字符串构造函数
class SimpleException : public exception {
    string msg;
public:
    SimpleException(const char* s) : msg(s) {}
    const char* what() const noexcept override { return msg.c_str(); }
};

// 方法2：支持格式化消息
class FormattedException : public exception {
    string msg;
public:
    FormattedException(const string& fmt, int value) {
        // 简单的字符串格式化
        char buffer[256];
        snprintf(buffer, sizeof(buffer), fmt.c_str(), value);
        msg = buffer;
    }
    
    const char* what() const noexcept override { return msg.c_str(); }
};

// 方法3：链式异常（记录异常链）
class ExceptionWithContext : public exception {
    string current_message;
    const exception* nested_exception;  // 嵌套的异常
    
public:
    ExceptionWithContext(const string& msg, const exception* nested = nullptr)
        : current_message(msg), nested_exception(nested) {}
    
    const char* what() const noexcept override {
        return current_message.c_str();
    }
    
    const exception* get_nested() const { return nested_exception; }
};

int main() {
    cout << "=== 异常构造函数设计 ===" << endl;
    
    // 测试简单异常
    try {
        throw SimpleException("简单的错误消息");
    }
    catch (exception& e) {
        cout << "SimpleException: " << e.what() << endl;
    }
    
    // 测试格式化异常
    try {
        throw FormattedException("数组大小 %d 超出限制", 1000);
    }
    catch (exception& e) {
        cout << "FormattedException: " << e.what() << endl;
    }
    
    return 0;
}
```

### 6.7 异常与类结合的最佳实践

```cpp
#include <iostream>
#include <exception>
#include <string>
#include <memory>
using namespace std;

// 定义异常类型
class MathException : public exception {
public:
    enum class Type {
        DIVIDE_BY_ZERO,
        OVERFLOW,
        UNDERFLOW,
        INVALID_INPUT
    };
    
private:
    Type type;
    string message;
    
public:
    MathException(Type t, const string& msg) : type(t), message(msg) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    Type get_type() const { return type; }
};

// 使用异常安全的数学类
class SafeCalculator {
public:
    static double divide(double a, double b) {
        if (b == 0) {
            throw MathException(MathException::Type::DIVIDE_BY_ZERO,
                              "除数不能为零");
        }
        return a / b;
    }
    
    static double safe_sqrt(double x) {
        if (x < 0) {
            throw MathException(MathException::Type::INVALID_INPUT,
                              "平方根参数不能为负数");
        }
        return sqrt(x);
    }
    
    static int factorial(int n) {
        if (n < 0) {
            throw MathException(MathException::Type::INVALID_INPUT,
                              "阶乘参数不能为负数");
        }
        if (n > 20) {
            throw MathException(MathException::Type::OVERFLOW,
                              "阶乘结果超出int范围");
        }
        int result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    }
};

// 异常处理辅助函数
void handle_math_error(const MathException& e) {
    cout << "数学错误：" << e.what() << endl;
    
    switch (e.get_type()) {
        case MathException::Type::DIVIDE_BY_ZERO:
            cout << "  -> 建议：检查除数是否为零" << endl;
            break;
        case MathException::Type::OVERFLOW:
            cout << "  -> 建议：使用更大范围的类型" << endl;
            break;
        case MathException::Type::INVALID_INPUT:
            cout << "  -> 建议：检查输入参数" << endl;
            break;
        default:
            cout << "  -> 未知错误类型" << endl;
    }
}

int main() {
    cout << "=== 异常安全的计算器 ===" << endl;
    
    // 测试各种异常
    vector<pair<string, function<void()>>> tests = {
        {"除以零", [](){ SafeCalculator::divide(10, 0); }},
        {"负数平方根", [](){ SafeCalculator::safe_sqrt(-5); }},
        {"负数阶乘", [](){ SafeCalculator::factorial(-1); }},
        {"大数阶乘", [](){ SafeCalculator::factorial(25); }},
        {"正常计算", [](){ 
            cout << SafeCalculator::divide(10, 2) << endl;
            cout << SafeCalculator::safe_sqrt(16) << endl;
            cout << SafeCalculator::factorial(5) << endl;
        }}
    };
    
    for (size_t i = 0; i < tests.size(); i++) {
        cout << "\n--- 测试" << i+1 << ": " << tests[i].first << " ---" << endl;
        try {
            tests[i].second();
        }
        catch (MathException& e) {
            handle_math_error(e);
        }
        catch (exception& e) {
            cout << "其他异常：" << e.what() << endl;
        }
    }
    
    return 0;
}
```

### 6.8 常见错误

**错误1：继承时忘记重写what()**
```cpp
// 错误：返回的是基类的消息
class BadException : public exception {
public:
    // 忘记重写what()
};

try {
    throw BadException();
}
catch (exception& e) {
    cout << e.what();  // 输出 "std::exception"，不是自定义消息
}
```

**正确做法：**
```cpp
class GoodException : public exception {
    string msg;
public:
    GoodException(const string& m) : msg(m) {}
    
    const char* what() const noexcept override {
        return msg.c_str();
    }
};
```

**错误2：对象切片问题**
```cpp
// 错误：按值传递会导致对象切片
try {
    throw DerivedException("错误");
}
catch (BaseException e) {  // 切片！只拷贝了Base部分
    // ...
}
```

**正确做法：**
```cpp
// 正确：按引用传递
try {
    throw DerivedException("错误");
}
catch (BaseException& e) {  // 保持完整对象
    // ...
}
```

**错误3：异常类中使用动态内存导致内存泄漏**
```cpp
// 不好：可能泄漏
class BadException : public exception {
    char* msg;  // 动态分配
public:
    BadException(const char* s) {
        msg = new char[strlen(s) + 1];
        strcpy(msg, s);
    }
    // 忘记析构函数
};

// 好：使用string
class GoodException : public exception {
    string msg;  // 自动管理内存
public:
    GoodException(const char* s) : msg(s) {}
    const char* what() const noexcept override {
        return msg.c_str();
    }
};
```

### 6.9 练习题

**练习6.1：创建基本异常类**
创建一个`NetworkException`类，继承`std::exception`，包含：
- 错误消息
- 错误码（404、500、503等）
- 重写what()方法

**练习6.2：设计异常层次**
为一个学生信息管理系统设计异常层次：
- 基础异常类AppException
- 学生异常类StudentException
- 成绩异常类GradeException
- 课程异常类CourseException

**练习6.3：使用自定义异常**
使用练习6.2设计的异常类，实现一个简单的成绩计算程序，处理以下异常：
- 学生不存在
- 成绩超出范围(0-100)
- 课程已满

---

## 7. 异常规范（noexcept）

### 7.1 什么是noexcept

**noexcept**是C++11引入的关键字，用于声明**不会抛出异常**的函数。

**语法：**
```cpp
void func() noexcept;           // 明确声明不抛出异常
void func();                    // 可能抛出异常
void func() noexcept(false);    // 可能抛出异常（与上面等价）
```

### 7.2 基本用法

```cpp
#include <iostream>
#include <exception>
using namespace std;

// 声明不会抛出异常的函数
int add(int a, int b) noexcept {
    return a + b;
}

// 可能抛出异常的函数
int divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为零");
    }
    return a / b;
}

// 测试noexcept
void test() {
    cout << "add()是noexcept? " << noexcept(add(1, 2)) << endl;
    cout << "divide()是noexcept? " << noexcept(divide(1, 1)) << endl;
    cout << "throw()是noexcept? " << noexcept(throw 1) << endl;
}

int main() {
    cout << "=== noexcept基本用法 ===" << endl;
    test();
    return 0;
}
```

**运行结果：**
```
=== noexcept基本用法 ===
add()是noexcept? 1
divide()是noexcept? 0
throw()是noexcept? 0
```

### 7.3 noexcept的作用

**作用1：优化机会**
- 编译器知道函数不抛异常，可以进行更多优化
- 不需要生成栈展开的代码

**作用2：明确意图**
- 告诉其他程序员这个函数不会抛异常
- 帮助静态分析工具检测错误

**作用3：容器优化**
- vector等容器在重新分配内存时，会优先选择noexcept的析构函数

### 7.4 异常规范的历史

```cpp
// 旧式异常规范（已废弃）
void old_style() throw();           // 声明不抛出任何异常
void old_style2() throw(runtime_error);  // 只抛出特定异常（危险！）

// 新式（C++11起推荐）
void new_style() noexcept;         // 声明不抛出异常
void new_style2();                  // 可能抛出异常
```

**为什么旧式规范被废弃？**
```cpp
// 危险！如果函数实际抛出了其他类型，会调用unexpected()
void risky() throw(invalid_argument) {  // 只允许invalid_argument
    throw runtime_error("运行时错误"); // 但实际抛出了这个！
    // 导致程序调用unexpected()，默认会terminate()
}
```

### 7.5 noexcept与移动语义

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class TestMove {
    string data;
public:
    TestMove() = default;
    TestMove(const string& s) : data(s) {}
    
    // 移动构造函数应该是noexcept
    // 否则vector在重新分配时会选择拷贝而不是移动
    TestMove(TestMove&& other) noexcept : data(move(other.data)) {
        cout << "移动构造" << endl;
    }
    
    TestMove& operator=(TestMove&& other) noexcept {
        if (this != &other) {
            data = move(other.data);
            cout << "移动赋值" << endl;
        }
        return *this;
    }
};

int main() {
    cout << "=== noexcept与移动语义 ===" << endl;
    
    vector<TestMove> v;
    v.reserve(2);  // 预分配空间
    
    cout << "\n插入第一个元素..." << endl;
    v.push_back(TestMove("first"));
    
    cout << "\n插入第二个元素（触发重新分配）..." << endl;
    v.push_back(TestMove("second"));
    
    // 移动语义应该被调用，因为移动构造函数是noexcept
    // 如果没有noexcept声明，vector可能选择拷贝
    
    return 0;
}
```

### 7.6 noexcept运算符

**noexcept(expression)** 可以在编译时检查表达式是否可能抛出异常。

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

struct A {
    int data = 0;
};

struct B {
    B() noexcept(false) {}  // 可能抛出异常
};

int safe_func() noexcept {
    return 42;
}

int unsafe_func() {
    throw 1;
    return 0;
}

int main() {
    cout << "=== noexcept运算符 ===" << endl;
    
    // 检查类型
    cout << "int是noexcept? " << noexcept(int()) << endl;
    cout << "A是noexcept? " << noexcept(A()) << endl;
    cout << "B是noexcept? " << noexcept(B()) << endl;
    
    // 检查函数
    cout << "safe_func是noexcept? " << noexcept(safe_func()) << endl;
    cout << "unsafe_func是noexcept? " << noexcept(unsafe_func()) << endl;
    
    // 在条件中使用
    if constexpr (noexcept(safe_func())) {
        cout << "safe_func保证不抛异常" << endl;
    }
    else {
        cout << "safe_func可能抛异常" << endl;
    }
    
    return 0;
}
```

### 7.7 析构函数与noexcept

**重要规则：C++11起，析构函数默认是noexcept的！**

```cpp
#include <iostream>
#include <exception>
using namespace std;

class GoodDestructor {
public:
    ~GoodDestructor() noexcept {
        cout << "GoodDestructor析构" << endl;
    }
};

class BadDestructor {
public:
    ~BadDestructor() noexcept(false) {  // 允许抛出异常
        cout << "BadDestructor析构" << endl;
        // 这里可以抛出异常
    }
};

int main() {
    cout << "=== 析构函数与noexcept ===" << endl;
    
    cout << "GoodDestructor的析构函数是noexcept? " 
         << noexcept(decltype(declval<GoodDestructor>())) << endl;
    cout << "BadDestructor的析构函数是noexcept? " 
         << noexcept(decltype(declval<BadDestructor>())) << endl;
    
    return 0;
}
```

### 7.8 noexcept与异常传播

```cpp
#include <iostream>
#include <exception>
using namespace std;

void inner() noexcept {
    // 这个函数声明不抛异常
    // 但如果真的抛出异常，会调用terminate()
    cout << "inner开始" << endl;
    throw runtime_error("在noexcept函数中抛出！");
    cout << "inner结束（不会执行）" << endl;
}

void outer() {
    try {
        inner();
    }
    catch (...) {
        cout << "outer捕获到异常（不会执行到）" << endl;
    }
}

int main() {
    cout << "=== noexcept与异常传播 ===" << endl;
    
    try {
        outer();
    }
    catch (...) {
        cout << "main捕获到异常" << endl;
    }
    
    return 0;
}
```

**输出：**
```
=== noexcept与异常传播 ===
inner开始
terminate called after throwing an instance of 'std::runtime_error'
  what(): 在noexcept函数中抛出！
已放弃(吐核)
```

### 7.9 实际应用建议

**应该使用noexcept的场景：**

```cpp
// 1. 简单的getter函数
class Point {
    int x, y;
public:
    int get_x() const noexcept { return x; }
    int get_y() const noexcept { return y; }
};

// 2. 移动构造函数和移动赋值运算符
class Buffer {
    char* data;
    size_t size;
public:
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }
    
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
        }
        return *this;
    }
};

// 3. 基本的工具函数
int max_int(int a, int b) noexcept { return a > b ? a : b; }
double abs_double(double x) noexcept { return x >= 0 ? x : -x; }
```

**不应该使用noexcept的场景：**

```cpp
// 1. 内部可能调用会抛异常的函数
void process_data() noexcept {  // 危险！
    string s = get_user_input();  // get_user_input可能抛异常
    parse_data(s);                 // parse_data可能抛异常
}

// 2. 分配内存的函数
int* allocate_array(size_t n) noexcept {  // 不合适！
    return new int[n];  // new可能抛bad_alloc
}

// 3. 涉及I/O的函数
void write_log(const string&) noexcept {  // 不合适！
    ofstream file("log.txt");  // 可能抛异常
    file << "log entry";        // 可能抛异常
}
```

### 7.10 常见错误

**错误1：在noexcept函数中抛出异常**
```cpp
void dangerous() noexcept {
    throw runtime_error("危险！");
    // 程序会直接terminate()
}
```

**错误2：过度使用noexcept**
```cpp
// 不是所有函数都应该noexcept
void process_file(const string& filename) noexcept {  // 不合适！
    // 如果文件不存在，这里会出问题
}
```

**错误3：忘记考虑间接调用**
```cpp
void看起来简单() noexcept {  // 看起来简单
    auto ptr = make_unique<int>();  // 但unique_ptr可能抛bad_alloc！
}
```

### 7.11 练习题

**练习7.1：noexcept判断**
判断以下函数是否应该是noexcept：
1. 返回vector大小的函数
2. 计算两个整数最大值的函数
3. 打开文件的函数
4. 复制字符串的函数
5. 释放内存的函数

**练习7.2：实现noexcept函数**
实现一个`string_view`类的基本功能，成员函数尽可能标记为noexcept。

**练习7.3：noexcept与移动语义**
创建一个类，实现移动构造函数和移动赋值运算符，正确使用noexcept，并测试vector中使用该类时的行为。

---

## 8. 异常与析构函数

### 8.1 析构函数与异常的基本规则

**核心规则：析构函数不应该抛出异常！**

```cpp
class Dangerous {
public:
    ~Dangerous() {
        // 危险！析构函数中抛出异常
        throw runtime_error("析构函数中的异常！");
    }
};

int main() {
    try {
        Dangerous d;  // 对象构造
    }  // 这里析构函数被调用
    catch (...) {
        // 可能导致未定义行为
    }
}
```

**为什么析构函数不能抛异常？**

1. **析构函数在对象生命周期结束时自动调用**
2. **堆栈展开过程中可能已经在处理另一个异常**
3. **同时处理两个异常会导致程序terminate()**

### 8.2 析构函数抛异常的危险

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class Resource {
public:
    ~Resource() {
        cout << "Resource析构函数被调用" << endl;
        // 如果这里抛出异常，会有问题
    }
};

class Problematic {
    Resource r;
public:
    ~Problematic() {
        cout << "Problematic析构函数被调用" << endl;
        throw runtime_error("析构函数抛出的异常！");
    }
};

int main() {
    cout << "=== 析构函数抛异常的危险 ===" << endl;
    
    try {
        Problematic p;
        cout << "对象p被创建" << endl;
    }  // 这里会：
        // 1. 调用p的析构函数
        // 2. p的析构函数抛异常
        // 3. 异常被抛出，但此时没有try块保护
        // 4. 程序terminate()
    
    catch (...) {
        cout << "捕获到异常（不会执行到）" << endl;
    }
    
    cout << "程序结束（不会执行到）" << endl;
    return 0;
}
```

### 8.3 正确使用析构函数

**规则：析构函数应该是noexcept的（默认）**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class GoodClass {
    int* data;
    size_t size;
    
public:
    GoodClass(size_t n) : size(n) {
        data = new int[n];
        cout << "分配内存" << endl;
    }
    
    // 析构函数：不应该抛异常
    ~GoodClass() noexcept {
        cout << "释放内存" << endl;
        // 不会抛出异常的操作
        delete[] data;
    }
    
    // 安全的清理方法（供用户调用）
    void close() {
        if (data) {
            delete[] data;
            data = nullptr;
            size = 0;
        }
    }
};

int main() {
    cout << "=== 正确的析构函数使用 ===" << endl;
    
    GoodClass* obj = new GoodClass(100);
    
    try {
        // 使用对象...
        throw runtime_error("使用过程中出错");
    }
    catch (...) {
        cout << "捕获异常，清理资源" << endl;
        obj->close();  // 使用安全的清理方法
        delete obj;
    }
    
    return 0;
}
```

### 8.4 RAII模式（资源获取即初始化）

**核心思想：将资源管理与对象生命周期绑定**

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

// RAII文件包装类
class FileHandle {
    ifstream file;
    string filename;
    bool is_open;
    
public:
    FileHandle(const string& name) : filename(name), is_open(false) {
        file.open(name);
        if (!file.is_open()) {
            throw runtime_error("无法打开文件：" + filename);
        }
        is_open = true;
        cout << "文件已打开：" << filename << endl;
    }
    
    // RAII：析构函数自动关闭文件
    ~FileHandle() noexcept {
        if (is_open) {
            cout << "文件自动关闭：" << filename << endl;
            file.close();
        }
    }
    
    void read_all() {
        string line;
        while (getline(file, line)) {
            cout << line << endl;
        }
    }
    
    // 禁止拷贝
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
};

int main() {
    cout << "=== RAII模式 ===" << endl;
    
    try {
        FileHandle fh("test.txt");  // 打开文件
        fh.read_all();
        // 即使这里抛异常，文件也会被正确关闭
    }
    catch (runtime_error& e) {
        cout << "错误：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

### 8.5 构造函数失败与异常

**构造函数中抛出异常是合法的，但要注意清理**

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

class PartialConstruct {
    int* data1;
    int* data2;
    
public:
    PartialConstruct(size_t size) : data1(nullptr), data2(nullptr) {
        cout << "分配data1..." << endl;
        data1 = new int[size];
        
        cout << "分配data2..." << endl;
        data2 = new int[size];
        
        // 如果这里失败，会导致data1泄漏！
        // 因为data2分配失败，但data1已经分配了
        if (size > 1000000) {
            throw runtime_error("大小超出限制");
        }
    }
    
    ~PartialConstruct() {
        delete[] data1;  // 如果data1没分配呢？
        delete[] data2;
    }
};

// 正确的做法：初始化列表+清理
class SafeConstruct {
    int* data1;
    int* data2;
    
public:
    SafeConstruct(size_t size) : data1(nullptr), data2(nullptr) {
        try {
            cout << "分配data1..." << endl;
            data1 = new int[size];
            
            cout << "分配data2..." << endl;
            data2 = new int[size];
            
            if (size > 1000000) {
                throw runtime_error("大小超出限制");
        }
        }
        catch (...) {
            // 清理已分配的资源
            delete[] data1;
            delete[] data2;
            data1 = nullptr;
            data2 = nullptr;
            throw;  // 重新抛出异常
        }
    }
    
    ~SafeConstruct() {
        delete[] data1;
        delete[] data2;
    }
};

int main() {
    cout << "=== 构造函数与异常 ===" << endl;
    
    cout << "\n--- 测试不安全的构造 ---" << endl;
    try {
        PartialConstruct pc(10);
    }
    catch (runtime_error& e) {
        cout << "捕获：" << e.what() << endl;
    }
    
    cout << "\n--- 测试安全的构造 ---" << endl;
    try {
        SafeConstruct sc(10);
    }
    catch (runtime_error& e) {
        cout << "捕获：" << e.what() << endl;
    }
    
    return 0;
}
```

### 8.6 资源清理的异常安全

**三种异常安全级别：**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 基本保证：异常后对象仍然有效
class BasicGuarantee {
    int* data;
public:
    void do_something() {
        if (rand() % 2 == 0) {
            throw runtime_error("操作失败");
        }
        // 即使失败，data仍然有效
    }
    
    BasicGuarantee() : data(new int[100]) {}
    ~BasicGuarantee() { delete[] data; }
};

// 强保证：操作要么成功，要么完全回滚
class StrongGuarantee {
    int* old_data;
    int* new_data;
    bool committed;
    
public:
    void operation() {
        // 保存旧状态
        old_data = new int[100];
        
        // 尝试新操作
        try {
            new_data = new int[100];
            // 修改new_data...
            committed = true;
        }
        catch (...) {
            // 完全回滚
            delete[] new_data;
            delete[] old_data;
            throw;
        }
        
        // 提交
        delete[] old_data;
        old_data = nullptr;
    }
    
    ~StrongGuarantee() {
        delete[] old_data;
        delete[] new_data;
    }
};

// 不抛异常保证：函数不会抛异常
class NoThrow {
public:
    int get_value() const noexcept {
        return 42;
    }
};

int main() {
    cout << "=== 异常安全级别 ===" << endl;
    cout << "1. 基本保证：对象有效，但状态可能变化" << endl;
    cout << "2. 强保证：要么成功，要么完全回滚" << endl;
    cout << "3. 不抛异常保证：函数绝不抛异常" << endl;
    return 0;
}
```

### 8.7 异常安全与智能指针

```cpp
#include <iostream>
#include <memory>
#include <stdexcept>
using namespace std;

// 使用智能指针自动管理内存
class ResourceManager {
    // 智能指针自动清理，不需要析构函数操心
    unique_ptr<int[]> data1;
    unique_ptr<int[]> data2;
    unique_ptr<double[]> data3;
    
public:
    ResourceManager(size_t size) {
        data1 = make_unique<int[]>(size);
        data2 = make_unique<int[]>(size);
        
        // 如果这里抛异常
        if (size > 1000000) {
            throw runtime_error("大小超出限制");
        }
        
        data3 = make_unique<double[]>(size);
    }
    
    // 智能指针自动清理，不需要手动delete
    // 即使构造函数中抛异常，已分配的内存也会被清理
};

int main() {
    cout << "=== 智能指针与异常安全 ===" << endl;
    
    try {
        ResourceManager rm(100);
    }
    catch (runtime_error& e) {
        cout << "捕获异常：" << e.what() << endl;
        // 不用担心内存泄漏，智能指针已经清理
    }
    
    return 0;
}
```

### 8.8 常见错误

**错误1：析构函数抛异常**
```cpp
class Bad {
public:
    ~Bad() {
        // 危险！不要这样做
        if (error_occurred) {
            throw runtime_error("析构函数错误");
        }
    }
};
```

**错误2：忘记处理构造函数中的异常**
```cpp
class Risky {
    int* data;
public:
    Risky() {
        data = new int[100];
        // 如果后面抛异常，data会泄漏
        throw runtime_error("初始化失败");
    }
    ~Risky() { delete[] data; }  // data可能从未分配！
};
```

**错误3：使用裸指针而不是智能指针**
```cpp
class OldStyle {
    int* data;
public:
    OldStyle() : data(new int[100]) {}
    ~OldStyle() { delete[] data; }  // 需要手动处理异常
};

class NewStyle {
    // 异常安全：自动管理
    unique_ptr<int[]> data = make_unique<int[]>(100);
    // 不需要析构函数！
};
```

### 8.9 最佳实践总结

```cpp
#include <iostream>
#include <memory>
#include <fstream>
using namespace std;

// 最佳实践1：析构函数不要抛异常
class BestPractice1 {
    int* data;
public:
    ~BestPractice1() noexcept {  // 明确声明
        delete[] data;
    }
};

// 最佳实践2：使用智能指针
class BestPractice2 {
    // RAII：自动管理资源
    unique_ptr<int[]> data = make_unique<int[]>(100);
    unique_ptr<fstream> file;
public:
    BestPractice2() {
        file = make_unique<fstream>("data.txt");
    }
    // 不需要析构函数！
};

// 最佳实践3：构造函数使用try-catch
class BestPractice3 {
    unique_ptr<int[]> data1;
    unique_ptr<int[]> data2;
    
public:
    BestPractice3(size_t size) try 
        : data1(make_unique<int[]>(size)),
          data2(make_unique<int[]>(size)) {
        // 构造函数的函数体
    }
    catch (...) {
        // 异常会被传播
        cout << "构造失败" << endl;
    }
};

int main() {
    cout << "=== 异常安全最佳实践 ===" << endl;
    return 0;
}
```

### 8.10 练习题

**练习8.1：析构函数异常**
说明为什么析构函数不应该抛出异常？如果必须在析构函数中执行可能失败的操作，应该怎么做？

**练习8.2：实现RAII类**
实现一个`Mutex`类的RAII包装：
- 构造函数获取锁
- 析构函数释放锁
- 禁止拷贝，允许移动

**练习8.3：构造函数异常安全**
实现一个`Connection`类，构造函数连接数据库，如果失败要正确清理已分配的资源。

**练习8.4：异常安全分析**
分析以下代码的异常安全性，并提出改进建议：
```cpp
class Buffer {
    int* data;
    size_t size;
public:
    void resize(size_t new_size) {
        int* new_data = new int[new_size];
        copy(data, data + size, new_data);
        delete[] data;
        data = new_data;
        size = new_size;
    }
};
```

---

## 9. 实际应用场景

### 9.1 文件操作异常处理

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>
#include <string>
#include <vector>
using namespace std;

// 异常类定义
class FileException : public exception {
    string filename;
    string operation;
public:
    FileException(const string& file, const string& op, const string& msg)
        : filename(file), operation(op) {
        // 组合错误消息
    }
    
    const char* what() const noexcept override {
        return ("文件操作失败：" + operation + " - " + filename).c_str();
    }
};

// 文件读取器
class FileReader {
private:
    string filename;
    ifstream file;
    vector<string> lines;
    
public:
    FileReader(const string& name) : filename(name) {
        file.open(name);
        if (!file.is_open()) {
            throw FileException(filename, "打开", "文件不存在或无法访问");
        }
    }
    
    void read_all() {
        string line;
        while (getline(file, line)) {
            lines.push_back(line);
        }
        
        if (file.bad()) {
            throw FileException(filename, "读取", "读取过程中发生错误");
        }
    }
    
    const vector<string>& get_lines() const { return lines; }
    size_t line_count() const { return lines.size(); }
    
    ~FileReader() {
        if (file.is_open()) {
            file.close();
        }
    }
};

// 文件写入器
class FileWriter {
private:
    string filename;
    ofstream file;
    
public:
    FileWriter(const string& name) : filename(name) {
        file.open(name);
        if (!file.is_open()) {
            throw FileException(filename, "创建", "无法创建文件");
        }
    }
    
    void write_line(const string& line) {
        file << line << '\n';
        if (file.fail()) {
            throw FileException(filename, "写入", "写入失败");
        }
    }
    
    void write_lines(const vector<string>& lines) {
        for (const auto& line : lines) {
            write_line(line);
        }
    }
    
    ~FileWriter() {
        if (file.is_open()) {
            file.close();
        }
    }
};

int main() {
    cout << "=== 文件操作异常处理 ===" << endl;
    
    // 测试读取不存在的文件
    cout << "\n--- 测试读取不存在的文件 ---" << endl;
    try {
        FileReader reader("nonexistent.txt");
        reader.read_all();
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    catch (exception& e) {
        cout << "其他异常：" << e.what() << endl;
    }
    
    // 测试创建和写入文件
    cout << "\n--- 测试写入文件 ---" << endl;
    try {
        FileWriter writer("output.txt");
        writer.write_line("第一行数据");
        writer.write_line("第二行数据");
        writer.write_line("第三行数据");
        cout << "写入成功" << endl;
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    // 测试读取刚写入的文件
    cout << "\n--- 测试读取刚写入的文件 ---" << endl;
    try {
        FileReader reader("output.txt");
        reader.read_all();
        cout << "读取到 " << reader.line_count() << " 行数据：" << endl;
        for (const auto& line : reader.get_lines()) {
            cout << "  " << line << endl;
        }
    }
    catch (FileException& e) {
        cout << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

### 9.2 网络请求异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <map>
using namespace std;

// 网络异常定义
class NetworkException : public exception {
public:
    enum class ErrorCode {
        CONNECTION_FAILED,
        TIMEOUT,
        NOT_FOUND,
        SERVER_ERROR,
        UNKNOWN
    };
    
private:
    ErrorCode code;
    string message;
    string url;
    
public:
    NetworkException(ErrorCode c, const string& msg, const string& u = "")
        : code(c), message(msg), url(u) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
    string get_url() const { return url; }
};

// 模拟HTTP响应
struct HttpResponse {
    int status_code;
    string body;
    bool success;
};

// 模拟网络请求
class HttpClient {
private:
    string base_url;
    int timeout_ms;
    map<string, string> headers;
    
public:
    HttpClient(const string& base) : base_url(base), timeout_ms(30000) {}
    
    void set_header(const string& key, const string& value) {
        headers[key] = value;
    }
    
    HttpResponse get(const string& path) {
        // 模拟网络请求
        cout << "发送GET请求：" << base_url << path << endl;
        
        // 模拟各种错误情况
        if (path == "/notfound") {
            throw NetworkException(
                NetworkException::ErrorCode::NOT_FOUND,
                "资源不存在",
                base_url + path
            );
        }
        
        if (path == "/servererror") {
            throw NetworkException(
                NetworkException::ErrorCode::SERVER_ERROR,
                "服务器内部错误",
                base_url + path
            );
        }
        
        if (path == "/timeout") {
            throw NetworkException(
                NetworkException::ErrorCode::TIMEOUT,
                "请求超时",
                base_url + path
            );
        }
        
        // 正常响应
        HttpResponse resp;
        resp.status_code = 200;
        resp.body = "{\"message\": \"success\"}";
        resp.success = true;
        return resp;
    }
};

// 错误处理函数
void handle_network_error(const NetworkException& e) {
    cerr << "网络错误发生！" << endl;
    cerr << "URL: " << e.get_url() << endl;
    cerr << "错误类型: ";
    
    switch (e.get_code()) {
        case NetworkException::ErrorCode::CONNECTION_FAILED:
            cerr << "连接失败";
            break;
        case NetworkException::ErrorCode::TIMEOUT:
            cerr << "请求超时";
            break;
        case NetworkException::ErrorCode::NOT_FOUND:
            cerr << "资源不存在";
            break;
        case NetworkException::ErrorCode::SERVER_ERROR:
            cerr << "服务器错误";
            break;
        default:
            cerr << "未知错误";
    }
    cerr << endl;
    
    cerr << "建议: ";
    if (e.get_code() == NetworkException::ErrorCode::TIMEOUT) {
        cerr << "增加超时时间或检查网络连接" << endl;
    }
    else if (e.get_code() == NetworkException::ErrorCode::SERVER_ERROR) {
        cerr << "服务器端问题，稍后重试" << endl;
    }
    else {
        cerr << "检查请求参数或网络状态" << endl;
    }
}

int main() {
    cout << "=== 网络请求异常处理 ===" << endl;
    
    HttpClient client("https://api.example.com");
    
    // 测试各种错误
    vector<string> test_paths = {
        "/success",
        "/notfound", 
        "/servererror",
        "/timeout"
    };
    
    for (const auto& path : test_paths) {
        cout << "\n--- 请求: " << path << " ---" << endl;
        try {
            HttpResponse resp = client.get(path);
            cout << "响应状态码: " << resp.status_code << endl;
            cout << "响应内容: " << resp.body << endl;
        }
        catch (NetworkException& e) {
            handle_network_error(e);
        }
        catch (exception& e) {
            cerr << "其他异常: " << e.what() << endl;
        }
    }
    
    return 0;
}
```

### 9.3 数据库操作异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// 数据库异常
class DatabaseException : public exception {
public:
    enum class ErrorCode {
        CONNECTION_FAILED,
        QUERY_ERROR,
        CONSTRAINT_VIOLATION,
        DUPLICATE_KEY,
        TRANSACTION_FAILED,
        TIMEOUT
    };
    
private:
    ErrorCode code;
    string message;
    string query;
    
public:
    DatabaseException(ErrorCode c, const string& msg, const string& q = "")
        : code(c), message(msg), query(q) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
    string get_query() const { return query; }
};

// 模拟数据库连接
class DatabaseConnection {
private:
    string connection_string;
    bool is_connected;
    bool in_transaction;
    
public:
    DatabaseConnection(const string& conn_str) 
        : connection_string(conn_str), is_connected(false), in_transaction(false) {
        // 模拟连接数据库
        cout << "连接到数据库: " << connection_string << endl;
        
        if (conn_str.empty()) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "连接字符串不能为空"
            );
        }
        
        if (conn_str == "invalid") {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "无法连接到数据库服务器"
            );
        }
        
        is_connected = true;
        cout << "连接成功" << endl;
    }
    
    // 执行查询
    void execute(const string& sql) {
        if (!is_connected) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "未连接到数据库"
            );
        }
        
        cout << "执行SQL: " << sql << endl;
        
        // 模拟各种SQL错误
        if (sql.find("INSERT") != string::npos && sql.find("duplicate") != string::npos) {
            throw DatabaseException(
                DatabaseException::ErrorCode::DUPLICATE_KEY,
                "违反唯一约束：记录已存在",
                sql
            );
        }
        
        if (sql.find("SELECT") != string::npos && sql.find("invalid") != string::npos) {
            throw DatabaseException(
                DatabaseException::ErrorCode::QUERY_ERROR,
                "SQL语法错误",
                sql
            );
        }
    }
    
    // 事务管理
    void begin_transaction() {
        if (!is_connected) {
            throw DatabaseException(
                DatabaseException::ErrorCode::CONNECTION_FAILED,
                "未连接到数据库"
            );
        }
        in_transaction = true;
        cout << "事务开始" << endl;
    }
    
    void commit() {
        if (!in_transaction) {
            throw DatabaseException(
                DatabaseException::ErrorCode::TRANSACTION_FAILED,
                "没有活跃的事务"
            );
        }
        in_transaction = false;
        cout << "事务提交" << endl;
    }
    
    void rollback() {
        in_transaction = false;
        cout << "事务回滚" << endl;
    }
    
    ~DatabaseConnection() {
        if (in_transaction) {
            cout << "警告：事务未提交，执行自动回滚" << endl;
            rollback();
        }
        if (is_connected) {
            cout << "关闭数据库连接" << endl;
            is_connected = false;
        }
    }
};

// 模拟用户表操作
class UserRepository {
private:
    shared_ptr<DatabaseConnection> db;
    
public:
    UserRepository(shared_ptr<DatabaseConnection> conn) : db(conn) {}
    
    void create_user(const string& username, const string& email) {
        // 检查参数
        if (username.empty()) {
            throw invalid_argument("用户名不能为空");
        }
        if (email.empty()) {
            throw invalid_argument("邮箱不能为空");
        }
        
        // 执行插入
        string sql = "INSERT INTO users (username, email) VALUES ('" 
                   + username + "', '" + email + "')";
        db->execute(sql);
        cout << "用户创建成功: " << username << endl;
    }
    
    void create_user_transaction(const string& username, const string& email) {
        try {
            db->begin_transaction();
            
            // 创建用户
            create_user(username, email);
            
            // 创建相关记录...
            db->execute("INSERT INTO user_profiles (username) VALUES ('" + username + "')");
            
            db->commit();
        }
        catch (...) {
            db->rollback();
            throw;
        }
    }
};

int main() {
    cout << "=== 数据库操作异常处理 ===" << endl;
    
    // 测试1：连接失败
    cout << "\n--- 测试1：无效连接 ---" << endl;
    try {
        DatabaseConnection db1("invalid");
    }
    catch (DatabaseException& e) {
        cerr << "连接失败：" << e.what() << endl;
    }
    
    // 测试2：创建用户（重复键）
    cout << "\n--- 测试2：重复键错误 ---" << endl;
    try {
        auto db2 = make_shared<DatabaseConnection>("mysql://localhost/testdb");
        UserRepository repo(db2);
        repo.create_user("admin", "admin@example.com");
        repo.create_user("admin", "admin2@example.com");  // 重复！
    }
    catch (DatabaseException& e) {
        cerr << "数据库错误：" << e.what() << endl;
        if (e.get_code() == DatabaseException::ErrorCode::DUPLICATE_KEY) {
            cerr << "建议：用户名已存在，请使用其他用户名" << endl;
        }
    }
    
    // 测试3：事务回滚
    cout << "\n--- 测试3：事务回滚 ---" << endl;
    try {
        auto db3 = make_shared<DatabaseConnection>("mysql://localhost/testdb");
        UserRepository repo(db3);
        repo.create_user_transaction("test", "test@example.com");
    }
    catch (DatabaseException& e) {
        cerr << "事务失败：" << e.what() << endl;
        cerr << "所有更改已被回滚" << endl;
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

### 9.4 配置解析异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <map>
#include <sstream>
using namespace std;

// 配置异常
class ConfigException : public exception {
public:
    enum class ErrorCode {
        FILE_NOT_FOUND,
        SYNTAX_ERROR,
        TYPE_MISMATCH,
        MISSING_REQUIRED,
        INVALID_VALUE
    };
    
private:
    ErrorCode code;
    string message;
    int line_number;
    
public:
    ConfigException(ErrorCode c, const string& msg, int line = 0)
        : code(c), message(msg), line_number(line) {}
    
    const char* what() const noexcept override {
        if (line_number > 0) {
            return ("[第" + to_string(line_number) + "行] " + message).c_str();
        }
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
};

// 配置解析器
class ConfigParser {
private:
    map<string, string> config_data;
    
    void trim(string& s) {
        // 去除首尾空白
        size_t start = s.find_first_not_of(" \t\r\n");
        size_t end = s.find_last_not_of(" \t\r\n");
        if (start != string::npos) {
            s = s.substr(start, end - start + 1);
        }
    }
    
    void parse_line(const string& line, int line_num) {
        string trimmed = line;
        trim(trimmed);
        
        // 跳过空行和注释
        if (trimmed.empty() || trimmed[0] == '#' || trimmed[0] == ';') {
            return;
        }
        
        // 解析 key = value
        size_t eq_pos = trimmed.find('=');
        if (eq_pos == string::npos) {
            throw ConfigException(
                ConfigException::ErrorCode::SYNTAX_ERROR,
                "缺少等号: " + line,
                line_num
            );
        }
        
        string key = trimmed.substr(0, eq_pos);
        string value = trimmed.substr(eq_pos + 1);
        
        trim(key);
        trim(value);
        
        if (key.empty()) {
            throw ConfigException(
                ConfigException::ErrorCode::SYNTAX_ERROR,
                "配置键不能为空: " + line,
                line_num
            );
        }
        
        config_data[key] = value;
    }
    
public:
    void parse(const string& content) {
        istringstream stream(content);
        string line;
        int line_num = 0;
        
        while (getline(stream, line)) {
            line_num++;
            parse_line(line, line_num);
        }
    }
    
    string get_string(const string& key) {
        if (config_data.find(key) == config_data.end()) {
            throw ConfigException(
                ConfigException::ErrorCode::MISSING_REQUIRED,
                "缺少必需的配置项: " + key
            );
        }
        return config_data[key];
    }
    
    int get_int(const string& key) {
        string value = get_string(key);
        try {
            return stoi(value);
        }
        catch (...) {
            throw ConfigException(
                ConfigException::ErrorCode::TYPE_MISMATCH,
                "配置项 " + key + " 应该是整数，实际是: " + value
            );
        }
    }
    
    double get_double(const string& key) {
        string value = get_string(key);
        try {
            return stod(value);
        }
        catch (...) {
            throw ConfigException(
                ConfigException::ErrorCode::TYPE_MISMATCH,
                "配置项 " + key + " 应该是数字，实际是: " + value
            );
        }
    }
    
    bool get_bool(const string& key) {
        string value = get_string(key);
        if (value == "true" || value == "1" || value == "yes" || value == "on") {
            return true;
        }
        if (value == "false" || value == "0" || value == "no" || value == "off") {
            return false;
        }
        throw ConfigException(
            ConfigException::ErrorCode::TYPE_MISMATCH,
            "配置项 " + key + " 应该是布尔值，实际是: " + value
        );
    }
};

int main() {
    cout << "=== 配置解析异常处理 ===" << endl;
    
    // 正确的配置文件内容
    string valid_config = R"(
# 服务器配置
host = localhost
port = 8080
debug = true
timeout = 30.5
max_connections = 100
)";

    cout << "\n--- 测试1：解析正确配置 ---" << endl;
    try {
        ConfigParser parser;
        parser.parse(valid_config);
        cout << "主机: " << parser.get_string("host") << endl;
        cout << "端口: " << parser.get_int("port") << endl;
        cout << "调试模式: " << (parser.get_bool("debug") ? "开" : "关") << endl;
        cout << "超时时间: " << parser.get_double("timeout") << "秒" << endl;
        cout << "最大连接数: " << parser.get_int("max_connections") << endl;
    }
    catch (ConfigException& e) {
        cerr << "配置错误：" << e.what() << endl;
    }
    
    // 错误的配置文件内容
    string invalid_config = R"(
# 错误配置
host = localhost
port = not_a_number
enabled =
max = 
)";

    cout << "\n--- 测试2：解析错误配置 ---" << endl;
    try {
        ConfigParser parser;
        parser.parse(invalid_config);
        // 尝试获取错误类型的值
        int port = parser.get_int("port");
    }
    catch (ConfigException& e) {
        cerr << "配置错误：" << e.what() << endl;
        
        if (e.get_code() == ConfigException::ErrorCode::TYPE_MISMATCH) {
            cerr << "提示：请检查配置项的类型是否正确" << endl;
        }
    }
    
    cout << "\n程序结束" << endl;
    return 0;
}
```

### 9.5 游戏开发中的异常处理

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// 游戏异常定义
class GameException : public exception {
public:
    enum class ErrorCode {
        INVALID_MOVE,
        OUT_OF_BOUNDS,
        RESOURCE_NOT_FOUND,
        SAVE_FAILED,
        LOAD_FAILED,
        CHEATING_DETECTED
    };
    
private:
    ErrorCode code;
    string message;
    
public:
    GameException(ErrorCode c, const string& msg) : code(c), message(msg) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
    
    ErrorCode get_code() const { return code; }
};

// 位置类
struct Position {
    int x, y;
    
    Position(int x = 0, int y = 0) : x(x), y(y) {}
    
    bool is_valid(int board_width, int board_height) const {
        return x >= 0 && x < board_width && y >= 0 && y < board_height;
    }
};

// 棋子类
enum class Player { NONE, BLACK, WHITE };

// 游戏板
class GameBoard {
private:
    static const int WIDTH = 8;
    static const int HEIGHT = 8;
    Player board[WIDTH][HEIGHT];
    
public:
    GameBoard() {
        for (int i = 0; i < WIDTH; i++) {
            for (int j = 0; j < HEIGHT; j++) {
                board[i][j] = Player::NONE;
            }
        }
    }
    
    void place_stone(int x, int y, Player player) {
        // 检查坐标是否有效
        if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT) {
            throw GameException(
                GameException::ErrorCode::OUT_OF_BOUNDS,
                "坐标 (" + to_string(x) + ", " + to_string(y) + ") 超出棋盘范围"
            );
        }
        
        // 检查位置是否已被占用
        if (board[x][y] != Player::NONE) {
            throw GameException(
                GameException::ErrorCode::INVALID_MOVE,
                "位置 (" + to_string(x) + ", " + to_string(y) + ") 已被占用"
            );
        }
        
        board[x][y] = player;
    }
    
    Player get_stone(int x, int y) const {
        if (x < 0 || x >= WIDTH || y < 0 || y >= HEIGHT) {
            throw GameException(
                GameException::ErrorCode::OUT_OF_BOUNDS,
                "坐标超出范围"
            );
        }
        return board[x][y];
    }
    
    void print() const {
        cout << "  0 1 2 3 4 5 6 7" << endl;
        for (int y = 0; y < HEIGHT; y++) {
            cout << y << " ";
            for (int x = 0; x < WIDTH; x++) {
                char c = '.';
                if (board[x][y] == Player::BLACK) c = 'X';
                else if (board[x][y] == Player::WHITE) c = 'O';
                cout << c << " ";
            }
            cout << endl;
        }
    }
};

// 游戏类
class Game {
private:
    GameBoard board;
    Player current_player;
    int move_count;
    
public:
    Game() : current_player(Player::BLACK), move_count(0) {}
    
    void make_move(int x, int y) {
        try {
            board.place_stone(x, y, current_player);
            move_count++;
            cout << "玩家 " << (current_player == Player::BLACK ? "黑" : "白") 
                 << " 在 (" << x << ", " << y << ") 放置棋子" << endl;
            
            // 切换玩家
            current_player = (current_player == Player::BLACK) ? 
                            Player::WHITE : Player::BLACK;
        }
        catch (GameException& e) {
            cerr << "游戏错误：" << e.what() << endl;
            
            if (e.get_code() == GameException::ErrorCode::INVALID_MOVE) {
                cerr << "提示：请选择一个空的位置" << endl;
            }
            else if (e.get_code() == GameException::ErrorCode::OUT_OF_BOUNDS) {
                cerr << "提示：请在 0-7 范围内选择坐标" << endl;
            }
            
            // 重新抛出，让调用者知道操作失败
            throw;
        }
    }
    
    void show_board() const {
        board.print();
    }
};

int main() {
    cout << "=== 游戏异常处理 ===" << endl;
    
    Game game;
    
    cout << "\n--- 测试1：正常落子 ---" << endl;
    try {
        game.make_move(3, 3);  // 黑棋
        game.make_move(4, 4);  // 白棋
        game.show_board();
    }
    catch (GameException& e) {
        cerr << e.what() << endl;
    }
    
    cout << "\n--- 测试2：重复落子 ---" << endl;
    try {
        game.make_move(3, 3);  // 尝试在已有棋子的位置落子
    }
    catch (GameException& e) {
        cerr << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n--- 测试3：越界落子 ---" << endl;
    try {
        game.make_move(10, 10);  // 超出棋盘范围
    }
    catch (GameException& e) {
        cerr << "捕获异常：" << e.what() << endl;
    }
    
    cout << "\n--- 最终棋盘状态 ---" << endl;
    game.show_board();
    
    return 0;
}
```

### 9.6 异常处理的最佳实践

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// ========== 最佳实践1：明确异常规范 ==========
// 应该使用noexcept的函数要明确标记
class BestPractice1 {
public:
    // 简单的查询函数不应该抛异常
    int get_value() const noexcept { return 42; }
    bool is_valid() const noexcept { return true; }
    
    // 可能失败的函数不应该标记noexcept
    void process_data() /* 可以抛异常 */ {
        throw runtime_error("处理失败");
    }
};

// ========== 最佳实践2：使用智能指针管理资源 ==========
class BestPractice2 {
    // 使用unique_ptr自动管理内存
    unique_ptr<int[]> data;
    
public:
    BestPractice2(size_t size) : data(make_unique<int[]>(size)) {}
    // 不需要析构函数！unique_ptr会自动释放内存
};

// ========== 最佳实践3：使用RAII管理资源 ==========
class BestPractice3 {
    FILE* file;
    
public:
    BestPractice3(const string& filename) : file(nullptr) {
        file = fopen(filename.c_str(), "r");
        if (!file) {
            throw runtime_error("无法打开文件");
        }
    }
    
    // 析构函数自动关闭文件
    ~BestPractice3() noexcept {
        if (file) {
            fclose(file);
        }
    }
    
    // 禁用拷贝
    BestPractice3(const BestPractice3&) = delete;
    BestPractice3& operator=(const BestPractice3&) = delete;
};

// ========== 最佳实践4：提供备选方案 ==========
class BestPractice4 {
public:
    // 方案1：可能失败
    string load_premium_data() {
        throw runtime_error("高级数据加载失败");
    }
    
    // 方案2：提供备选
    string load_data_with_fallback() {
        try {
            return load_premium_data();
        }
        catch (...) {
            cout << "使用基础数据作为备选" << endl;
            return "基础数据";
        }
    }
};

// ========== 最佳实践5：只捕获可处理的异常 ==========
class BestPractice5 {
public:
    void handle_with_care() {
        try {
            // 可能抛出各种异常
            throw runtime_error("错误");
        }
        catch (const invalid_argument& e) {
            // 只处理我们知道的异常
            cerr << "无效参数：" << e.what() << endl;
        }
        catch (const out_of_range& e) {
            // 处理越界错误
            cerr << "越界：" << e.what() << endl;
        }
        // 其他异常让它传播上去
    }
};

// ========== 最佳实践6：异常安全等级 ==========
class BestPractice6 {
    string data;
    
public:
    // 基本保证：操作后对象仍然有效
    void basic_guarantee() noexcept {
        // 即使失败，对象仍然有效
    }
    
    // 强保证：操作要么完全成功，要么完全回滚
    void strong_guarantee() {
        string old_data = data;  // 保存旧状态
        try {
            // 尝试新操作
            data = "new_value";
            // 如果这里抛异常，data已经是新值了
        }
        catch (...) {
            data = old_data;  // 回滚
            throw;
        }
    }
};

int main() {
    cout << "=== 异常处理最佳实践 ===" << endl;
    return 0;
}
```

### 9.7 练习题

**练习9.1：文件解析器**
实现一个CSV文件解析器，能够处理以下异常：
- 文件不存在
- 文件格式错误
- 数据类型不匹配
- 缺少必需字段

**练习9.2：网络请求包装器**
实现一个HTTP客户端包装类，处理连接超时、服务器错误、解析错误等异常。

**练习9.3：数据库连接池**
实现一个简化的数据库连接池，包含以下异常处理：
- 连接获取失败
- 连接池已满
- 连接超时

**练习9.4：游戏引擎异常系统**
为一个简化的游戏引擎设计异常系统，包括：
- 资源加载失败
- 着色器编译错误
- 纹理格式不支持
- 内存不足

---

## 10. 本章小结

### 10.1 核心概念回顾

| 概念 | 说明 |
|------|------|
| 异常 | 程序运行时的错误情况 |
| throw | 抛出异常的关键字 |
| try | 包含可能抛出异常的代码块 |
| catch | 捕获并处理异常 |
| noexcept | 声明函数不抛出异常 |

### 10.2 异常处理语法

```cpp
try {
    // 可能抛出异常的代码
    throw exception_type("错误信息");
}
catch (exception_type1& e) {
    // 处理特定类型的异常
}
catch (exception_type2& e) {
    // 处理另一种异常
}
catch (...) {
    // 捕获所有剩余异常
}
```

### 10.3 标准异常类层次

```
exception
├── logic_error
│   ├── invalid_argument
│   ├── domain_error
│   ├── length_error
│   └── out_of_range
├── runtime_error
│   ├── range_error
│   ├── overflow_error
│   └── underflow_error
└── bad_alloc
```

### 10.4 关键规则

1. **析构函数不应抛出异常**
2. **使用智能指针管理资源**
3. **catch块从具体到一般排序**
4. **使用noexcept标记不抛异常的函数**
5. **不要用异常处理正常流程**

### 10.5 异常安全等级

| 等级 | 说明 |
|------|------|
| 不抛异常保证 | 函数保证不抛异常 |
| 强保证 | 操作要么成功，要么完全回滚 |
| 基本保证 | 操作后对象仍然有效 |

### 10.6 本章练习答案

**练习1.1：什么是异常**
异常是程序在运行过程中遇到的意外情况或错误状态。使用异常处理可以将正常逻辑和错误处理分离，使代码更清晰，并且强制调用者处理错误。

**练习2.2：执行流程**
输出顺序为：1 → 2 → 4 → 5
（throw后立即跳转到catch，不会输出3）

**练习3.1：抛出异常**
```cpp
int max_of_three(int a, int b, int c) {
    if (a < 0 && b < 0 && c < 0) {
        throw runtime_error("所有数都是负数");
    }
    return max(a, max(b, c));
}
```

**练习4.1：匹配结果**
- invalid_argument的catch：不匹配
- out_of_range的catch：**匹配**
- exception的catch：不匹配（已被out_of_range捕获）

**练习5.1：异常类型选择**
1. out_of_range（数组越界）
2. domain_error（负数平方根）
3. bad_alloc（内存分配）
4. invalid_argument（参数为nullptr）
5. overflow_error（整数溢出）

**练习6.1：NetworkException**
```cpp
class NetworkException : public exception {
    int error_code;
    string message;
public:
    NetworkException(int code, const string& msg) 
        : error_code(code), message(msg) {}
    const char* what() const noexcept override {
        return message.c_str();
    }
};
```

**练习7.1：noexcept判断**
1. 返回vector大小：是noexcept
2. 计算整数最大值：是noexcept
3. 打开文件：不是noexcept
4. 复制字符串：不是noexcept
5. 释放内存：是noexcept

**练习8.1：析构函数异常**
析构函数抛异常会导致双重异常（已有异常时再抛异常），程序会terminate()。如果必须在析构函数中执行可能失败的操作，应该：
1. 使用noexcept(false)显式声明
2. 使用try-catch捕获所有异常
3. 记录错误但不抛出

---

## 下章预告

下一章我们将学习**C++模板**，这是C++泛型编程的核心特性。通过模板，我们可以编写与类型无关的代码，提高代码复用性。

主要内容：
- 函数模板
- 类模板
- 模板参数
- 模板特化
- 模板与STL

---

*本章完*
