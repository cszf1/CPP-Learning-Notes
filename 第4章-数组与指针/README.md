# 第4章 数组与指针

> 本章目标：理解连续存储、地址和字符串。数组和指针是 C++ 入门路上的两座桥，桥不难，别闭着眼冲。

## 目录

1. 一维数组
2. 二维数组
3. 数组作为函数参数
4. 指针与地址
5. 指针运算
6. 指针作为函数参数
7. 字符数组与字符串
8. 查找与排序应用
9. 本章小结

## 1. 一维数组

### 1.1 数组的概念

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

### 1.2 一维数组

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

## 2. 二维数组

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


## 3. 数组作为函数参数

数组作参数时通常退化为指针，所以要额外传长度。

```cpp
int sum(const int a[], int n) {
    int s = 0;
    for (int i = 0; i < n; ++i) s += a[i];
    return s;
}
```

二维数组作参数时，列数必须明确：

```cpp
void print(int a[][3], int rows);
```


## 4. 指针与地址

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


## 5. 指针运算

指针加 1 不是地址数值简单加 1，而是移动到下一个同类型元素。

```cpp
int a[3] = {10, 20, 30};
int* p = a;
cout << *(p + 1); // 20
```

两个同一数组内的指针可以相减，结果是元素个数差。


## 6. 指针作为函数参数

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

### 9.1 常见错误

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

### 9.2 练习

```cpp
// 练习1：找出数组中的第二大数

// 练习2：实现字符串反转（不用库函数）

// 练习3：实现strcmp函数（比较两个字符串）

// 练习4：使用指针实现冒泡排序

// 练习5：动态分配一个3x3矩阵，输入数据后转置输出
```

---


## 7. 字符数组与字符串

C 风格字符串以 `'\0'` 结尾。

```cpp
char s1[] = "abc";          // 含结尾 '\0'
char s2[] = {'a','b','c'};  // 不是合法 C 字符串
```

常见函数：`strlen`、`strcpy`、`strcat`、`strcmp`。现代 C++ 更推荐 `std::string`：

```cpp
string s = "hello";
s += " world";
```




## 8. 查找与排序应用

顺序查找：

```cpp
int find(const int a[], int n, int x) {
    for (int i = 0; i < n; ++i)
        if (a[i] == x) return i;
    return -1;
}
```

折半查找要求数组有序：

```cpp
int binarySearch(const int a[], int n, int x) {
    int l = 0, r = n - 1;
    while (l <= r) {
        int m = l + (r - l) / 2;
        if (a[m] == x) return m;
        if (a[m] < x) l = m + 1;
        else r = m - 1;
    }
    return -1;
}
```

排序常见有插入、冒泡、选择。理解原理即可，实际项目优先 `std::sort`。


## 9. 本章小结

- 数组连续存储，下标从 0 开始。
- 数组作参数常退化为指针，长度要另传。
- 指针保存地址，`*` 访问目标。
- C 字符串必须以 `'\0'` 结尾。
- 折半查找必须先有序。

[返回主目录](../README.md)

[返回主目录](../README.md)
