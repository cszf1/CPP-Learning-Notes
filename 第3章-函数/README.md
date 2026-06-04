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

### 1.1 函数的概念

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



### 1.2 函数的定义和调用

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



### 1.3 函数原型（声明）

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




## 2. 参数传递

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



## 3. 函数返回值与函数指针

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



## 4. 默认参数

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



## 5. 函数重载

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



## 6. 内联函数

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



## 7. 作用域与生存期

> **好函数的标准**：
> 1. **单一职责**：一个函数只做一件事
> 2. **命名清晰**：函数名要能说明它的功能
> 3. **参数适量**：参数太多考虑用结构体
> 4. **不要太长**：超过一屏考虑拆分
> 5. **避免副作用**：尽量不修改全局变量



## 8. 递归

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



## 9. 本章小结

### 9.1 常见错误

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



### 9.2 练习

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



### 9.3 核心要点回顾

- 函数是代码复用的基本单位：声明告诉编译器"有这个函数"，定义告诉它"怎么干"。
- 参数传递：值传递传副本，引用传递传别名。需要修改实参时用引用。
- 函数重载：同名不同参，编译器根据参数类型选择。
- 默认参数从右向左，调用时从左向右匹配。
- 递归要有终止条件，否则栈溢出。
- 内联函数适合简单短小的函数，编译时展开，避免函数调用开销。


[返回主目录](../README.md)
