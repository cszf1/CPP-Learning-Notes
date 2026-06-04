# 第2章 基本控制结构

> 本章目标：让程序会选择、会重复。没有控制结构，程序就像只会直走的扫地机器人。

## 目录

1. 顺序结构
2. if 选择结构
3. switch 多分支
4. while 与 do while
5. for 循环
6. break 与 continue
7. 循环嵌套与程序设计思路
8. 本章小结

## 1. 顺序结构

### 2.1 程序的三种基本结构

> 想象做菜的流程：
> - **顺序结构**：按步骤一步一步来（洗菜→切菜→炒菜→装盘）
> - **选择结构**：根据情况选择（如果没盐就出去买）
> - **循环结构**：重复做某事（搅拌100次）




### 1.1 程序的三种基本结构

> 想象做菜的流程：
> - **顺序结构**：按步骤一步一步来（洗菜→切菜→炒菜→装盘）
> - **选择结构**：根据情况选择（如果没盐就出去买）
> - **循环结构**：重复做某事（搅拌100次）



## 2. if 选择结构

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



## 3. switch 多分支

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



## 4. while 与 do while

### 4.1 while 循环

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



### 4.2 do-while 循环

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



## 5. for 循环

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



## 6. break 与 continue

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



## 7. 循环嵌套与程序设计思路


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




### 7.1 循环的典型应用

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



### 7.2 循环选择指南

| 循环类型 | 特点 | 适用场景 |
|---------|------|---------|
| for | 循环次数明确 | 遍历、计数 |
| while | 先判断后执行 | 次数不确定 |
| do-while | 先执行后判断 | 至少执行一次 |

> **记忆口诀**：
> - 次数确定用 **for**
> - 次数不确定用 **while**
> - 至少一次用 **do-while**



### 7.3 常见错误

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



### 7.4 练习

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




## 8. 本章小结

- 顺序、选择、循环是结构化程序设计的三件套。
- `switch` 里大多数情况要写 `break`。
- `while` 适合条件循环，`for` 适合计数循环。
- 循环嵌套要注意变量范围和退出条件。
- 复杂题先画流程，别让脑子直接裸跑。

[返回主目录](../README.md)

[返回主目录](../README.md)


[返回主目录](../README.md)
