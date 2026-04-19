# 第9章 文件与流

> 🔧 C++实用技能：让程序能够"记住"数据

---

## 📋 目录

1. [文件的基本概念](#1-文件的基本概念)
2. [fstream文件流类](#2-fstream文件流类)
3. [文本文件读写](#3-文本文件读写)
4. [二进制文件读写](#4-二进制文件读写)
5. [文件状态检查](#5-文件状态检查)
6. [文件定位与随机访问](#6-文件定位与随机访问)
7. [综合案例：学生信息管理系统](#7-综合案例学生信息管理系统)
8. [常见错误与注意事项](#8-常见错误与注意事项)
9. [文件操作与异常的结合](#9-文件操作与异常的结合)
10. [实战技巧与最佳实践](#10-实战技巧与最佳实践)
11. [本章小结](#11-本章小结)

---

## 1. 文件的基本概念

### 1.1 什么是文件

**概念讲解：**

文件（File）是存储在磁盘上的数据集合。就像我们的衣柜里有不同的抽屉，每个抽屉存放不同类型的衣物；计算机用文件来存放不同类型的数据。

**通俗比喻：**

想象你写日记：
- 每天写一页，写完保存到日记本里
- 下次想看的时候，打开日记本翻到那一天
- 日记本就是"文件"，每一页就是文件里的数据

在C++中，文件就是存储在硬盘上的数据，可以是：
- 文本文件：可以用记事本打开，能看懂的内容（如 `.txt`、`.csv`）
- 二进制文件：不能直接用记事本打开，内容是机器码（如 `.exe`、`.jpg`）

### 1.2 为什么需要文件操作

**程序的"记忆"问题：**

```cpp
int main() {
    int scores[5] = {90, 85, 92, 88, 95};
    // 程序运行完，这些数据就没了
    // 下次运行，scores还是这三个数
    return 0;
}
```

**问题**：程序关闭后，数据就消失了！

**文件的作用**：
- 让数据"活"得更久——程序退出后数据还在
- 不同程序之间可以共享数据
- 永久保存用户配置、游戏存档等

**生活中的类比**：
- 人的记忆：短期记忆（变量）vs 长期记忆（文件）
- 短期记忆：关机就没了
- 长期记忆：写入日记本/云端，永远保存

### 1.3 流的概念

**什么是流（Stream）？**

流就是"数据流动的管道"。想象自来水管道：
- 水从水源流到用户家里
- 中间经过管道，不需要关心水是怎么流的

C++中的流也是一样的：
- `cin` 是从键盘到程序的数据流
- `cout` 是从程序到屏幕的数据流
- 文件流是程序和文件之间的数据流

**三类标准流**：
| 流对象 | 含义 | 默认设备 |
|--------|------|----------|
| `cin` | 标准输入流 | 键盘 |
| `cout` | 标准输出流 | 屏幕 |
| `cerr` | 标准错误流 | 屏幕 |

**流的特性**：
1. **方向性**：数据只能单向流动
2. **顺序性**：数据按顺序读写
3. **缓冲**：数据先到缓冲区，满了才真正读写

**图示**：
```
键盘 → [cin缓冲区] → 程序 → [cout缓冲区] → 屏幕
                      ↓
              [文件缓冲区] → 文件
```

### 1.4 文件与内存的关系

**为什么要缓冲区？**

想象你在喝水：
- 直接一口一口喝（无缓冲）：每次都要起身倒水
- 用杯子接水（有缓冲）：倒一大杯，慢慢喝

文件缓冲区也是同样的道理：
- 减少磁盘读写次数（磁盘很慢）
- 提高程序效率

**缓冲区刷新的时机**：
1. 缓冲区满时自动刷新
2. 调用 `flush()` 手动刷新
3. 调用 `close()` 时自动刷新
4. 程序正常结束时

---

## 2. fstream文件流类

### 2.1 文件流类家族

C++的文件操作通过三个主要的类完成：

```cpp
#include <fstream>

// 三个主要的文件流类
ifstream   // 输入文件流（Input File Stream）—— 读取文件
ofstream   // 输出文件流（Output File Stream）—— 写入文件
fstream    // 文件流（File Stream）—— 既能读也能写
```

**通俗理解**：
- `ifstream` = `i`(input输入) + `fstream` = "往程序里读"
- `ofstream` = `o`(output输出) + `fstream` = "从程序往外写"
- `fstream` = 双向通道，既能读也能写

**类继承关系**：
```
ios_base
    ↓
ios
    ↓
istream
    ↓        ↓
ifstream   ostream
    ↓        ↓
    └────┬────┘
         ↓
      iostream
         ↓
       fstream
```

### 2.2 打开文件

**方式一：构造函数打开**

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    // 以写入模式打开文件
    ofstream outFile("test.txt");  // 简单写法
    
    if (!outFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    outFile << "Hello, World!" << endl;
    outFile.close();  // 关闭文件
    
    return 0;
}
```

**方式二：open()函数打开**

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    // 先创建对象，再用open打开
    ofstream outFile;
    outFile.open("test.txt");  // 打开文件
    
    if (!outFile.is_open()) {  // 检查是否成功打开
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    outFile << "Hello, World!" << endl;
    outFile.close();
    
    return 0;
}
```

**两种方式的区别**：
| 方式 | 优点 | 适用场景 |
|------|------|----------|
| 构造函数 | 简洁，一步到位 | 文件确定时使用 |
| open() | 灵活，可多次使用 | 需要判断后打开 |

### 2.3 打开模式

打开文件时可以指定模式（mode）：

| 模式 | 含义 | 说明 |
|------|------|------|
| `ios::in` | 读取 | 打开用于读取（默认 for `ifstream`） |
| `ios::out` | 写入 | 打开用于写入（默认 for `ofstream`） |
| `ios::app` | 追加 | 追加模式，新内容添加到文件末尾 |
| `ios::trunc` | 截断 | 如果文件存在，清空文件内容（默认） |
| `ios::binary` | 二进制 | 以二进制模式打开（默认是文本模式） |
| `ios::ate` | 末尾 | 打开后移到文件末尾 |

**示例：不同打开模式**

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    // 追加模式：不会覆盖原有内容
    ofstream outFile1("log.txt", ios::app);
    outFile1 << "新的日志内容" << endl;
    outFile1.close();
    
    // 读取模式
    ifstream inFile("data.txt", ios::in);
    if (!inFile) {
        cout << "文件不存在" << endl;
    }
    inFile.close();
    
    // 同时读写模式
    fstream ioFile("data.txt", ios::in | ios::out);
    ioFile.close();
    
    return 0;
}
```

**模式组合说明**：
- `ios::in | ios::out`：可读可写（不会创建新文件）
- `ios::in | ios::out | ios::trunc`：可读可写，但会清空文件
- `ios::app | ios::out`：只能追加，不能读取
- `ios::in | ios::out | ios::app`：可读可追加（最安全）

**生活中的类比**：
- 只读：`ios::in` = 只能看书，不能涂改
- 只写：`ios::out` = 拿一张白纸开始写（会清空旧内容）
- 追加：`ios::app` = 打开日记本翻到最后一页继续写
- 读写：`ios::in | ios::out` = 既能看书也能写新内容

### 2.4 关闭文件

```cpp
#include <fstream>
using namespace std;

int main() {
    ofstream outFile("test.txt");
    
    // 操作文件...
    outFile << "Hello" << endl;
    
    // 关闭文件（很重要！）
    outFile.close();  // 关闭后数据才会真正写入磁盘
    
    return 0;
}
```

**为什么要关闭文件？**
1. 确保数据写入磁盘（缓冲区刷新）
2. 释放系统资源
3. 防止数据丢失

**不关闭文件的后果**：
- 数据可能还停留在缓冲区，没有写入磁盘
- 如果程序崩溃，数据会丢失
- 系统资源（文件描述符）会泄漏

**自动关闭**：
```cpp
{
    ofstream outFile("test.txt");  // 进入作用域
    outFile << "Hello";
}  // 离开作用域时自动调用析构函数，关闭文件
```

**最佳实践**：使用 RAII（资源获取即初始化）
```cpp
void processFile() {
    // 文件在构造函数打开，析构函数自动关闭
    ofstream outFile("data.txt");
    outFile << "重要数据";
    // 函数结束时自动关闭，不需要手动close()
}
```

---

## 3. 文本文件读写

### 3.1 写入文本文件

**使用 `<<` 操作符（和cout一样）**

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 创建并打开文件
    ofstream outFile("students.txt");
    
    if (!outFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    // 写入内容（就像用cout一样）
    outFile << "姓名\t年龄\t分数" << endl;
    outFile << "张三\t20\t92" << endl;
    outFile << "李四\t19\t88" << endl;
    outFile << "王五\t21\t95" << endl;
    
    // 关闭文件
    outFile.close();
    
    cout << "写入成功！" << endl;
    
    return 0;
}
```

**运行后 students.txt 内容：**
```
姓名	年龄	分数
张三	20	92
李四	19	88
王五	21	95
```

### 3.2 格式化输出到文件

**与 cout 完全相同的用法**：

```cpp
#include <fstream>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    ofstream outFile("report.txt");
    
    // 设置宽度
    outFile << setw(10) << "姓名" 
            << setw(10) << "数学" 
            << setw(10) << "语文" 
            << setw(10) << "英语" << endl;
    
    outFile << setw(10) << "张三"
            << setw(10) << 95
            << setw(10) << 88
            << setw(10) << 92 << endl;
    
    // 设置对齐方式
    outFile << left;  // 左对齐
    outFile << setw(10) << "李四"
            << setw(10) << 92
            << setw(10) << 90
            << setw(10) << 85 << endl;
    
    // 设置浮点数精度
    outFile << fixed << setprecision(2);
    outFile << "平均值: " << 89.5678 << endl;  // 输出: 89.57
    
    outFile.close();
    
    return 0;
}
```

**常用格式化函数**：
| 函数 | 作用 |
|------|------|
| `setw(n)` | 设置字段宽度 |
| `setprecision(n)` | 设置浮点精度 |
| `fixed` | 定点数表示 |
| `left` | 左对齐 |
| `right` | 右对齐 |
| `setfill(c)` | 设置填充字符 |

### 3.3 读取文本文件

**使用 `>>` 操作符（和cin一样，按空格/回车分割）**

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 打开文件
    ifstream inFile("students.txt");
    
    if (!inFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    string name;
    int age, score;
    
    // 按空格/回车读取
    inFile >> name >> age >> score;  // 读第一行会读到"姓名"
    
    // 循环读取
    while (inFile >> name >> age >> score) {
        cout << name << " - 年龄:" << age << " - 分数:" << score << endl;
    }
    
    inFile.close();
    
    return 0;
}
```

**循环读取的原理**：
```cpp
// while (inFile >> name >> age >> score)
// 展开后相当于：
while (true) {
    inFile >> name;
    if (!(inFile >> age)) break;  // age读取失败，退出循环
    if (!(inFile >> score)) break;  // score读取失败，退出循环
    // 处理数据...
}
```

### 3.4 使用 getline() 读取整行

**问题**：如果一行中有空格，用 `>>` 就不行了

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 写入带空格的内容
    ofstream outFile("info.txt");
    outFile << "Hello World" << endl;  // 这一行有空格
    outFile << "你好 世界" << endl;
    outFile.close();
    
    // 读取整行（能正确处理空格）
    ifstream inFile("info.txt");
    string line;
    
    while (getline(inFile, line)) {  // 读取一行
        cout << line << endl;
    }
    
    inFile.close();
    
    return 0;
}
```

**运行结果：**
```
Hello World
你好 世界
```

**getline() 详解**：
```cpp
// 基本用法
string line;
getline(inFile, line);  // 读取一行到line中

// 指定分隔符
getline(inFile, line, ':');  // 遇到':'停止（不包含':'本身）

// 读取 CSV 文件
while (getline(inFile, line)) {
    stringstream ss(line);
    string name, age, score;
    getline(ss, name, ',');   // 逗号分隔
    getline(ss, age, ',');
    getline(ss, score, ',');
    // 处理数据...
}
```

### 3.5 逐字符读取

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream inFile("test.txt");
    
    if (!inFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    char ch;
    int charCount = 0;
    
    while (inFile.get(ch)) {  // 逐个字符读取
        cout << ch;
        charCount++;
    }
    
    cout << "\n共读取了 " << charCount << " 个字符" << endl;
    
    inFile.close();
    
    return 0;
}
```

**get() vs >> 的区别**：
```cpp
// 使用 >>
char ch1;
inFile >> ch1;  // 跳过空格和换行符

// 使用 get()
char ch2;
inFile.get(ch2);  // 不跳过空格和换行符

// 读取所有字符（包括空格和换行）
while (inFile.get(ch)) {
    cout << ch;
}
```

### 3.6 综合实例：文本文件处理

**实例1：统计文件行数、单词数、字符数**

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    ifstream inFile("article.txt");
    
    if (!inFile) {
        cout << "文件打开失败" << endl;
        return 1;
    }
    
    int lines = 0;      // 行数
    int words = 0;      // 单词数
    int chars = 0;       // 字符数
    string line;
    
    while (getline(inFile, line)) {
        lines++;
        chars += line.length() + 1;  // +1 for '\n'
        
        // 统计单词数
        bool inWord = false;
        for (char c : line) {
            if (c == ' ' || c == '\t') {
                inWord = false;
            } else if (!inWord) {
                inWord = true;
                words++;
            }
        }
    }
    
    cout << "行数: " << lines << endl;
    cout << "单词数: " << words << endl;
    cout << "字符数: " << chars << endl;
    
    inFile.close();
    return 0;
}
```

**实例2：文本文件内容替换**

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    string filename = "original.txt";
    string oldWord, newWord;
    
    cout << "请输入要被替换的单词: ";
    cin >> oldWord;
    cout << "请输入替换后的单词: ";
    cin >> newWord;
    
    // 读取原文件
    ifstream inFile(filename);
    if (!inFile) {
        cout << "文件打开失败" << endl;
        return 1;
    }
    
    // 读取所有内容到字符串
    string content((istreambuf_iterator<char>(inFile)),
                   istreambuf_iterator<char>());
    inFile.close();
    
    // 替换内容
    size_t pos = 0;
    while ((pos = content.find(oldWord, pos)) != string::npos) {
        content.replace(pos, oldWord.length(), newWord);
        pos += newWord.length();
    }
    
    // 写入新文件
    ofstream outFile("modified.txt");
    outFile << content;
    outFile.close();
    
    cout << "替换完成！" << endl;
    return 0;
}
```

---

## 4. 二进制文件读写

### 4.1 文本文件 vs 二进制文件

**区别**：
| 特征 | 文本文件 | 二进制文件 |
|------|----------|------------|
| 存储方式 | ASCII字符 | 原始字节 |
| 可读性 | 人能直接看懂 | 打开是乱码 |
| 跨平台 | 可能有换行符问题 | 完全一致 |
| 体积 | 通常较大 | 通常较小 |
| 精度 | 浮点数可能有精度损失 | 保持原样 |

**生活中的类比**：
- 文本文件 = 手写的日记（人能看懂）
- 二进制文件 = 录音文件（用特殊设备才能播放）

**什么时候用哪种？**
- 文本文件：配置文件、日志、网页、简单数据交换
- 二进制文件：图片、音频、视频、游戏存档、结构化数据

### 4.2 打开二进制文件

```cpp
#include <fstream>
using namespace std;

int main() {
    // 以二进制模式打开
    ofstream outFile("data.bin", ios::binary);
    ifstream inFile("data.bin", ios::binary);
    fstream ioFile("data.bin", ios::binary | ios::in | ios::out);
    
    return 0;
}
```

**重要**：二进制模式下去掉了文本模式的特殊处理：
- 不会自动转换 `\n` 为 `\r\n`
- 不会忽略 `\r` 字符
- 保持数据原样

### 4.3 写入二进制文件：write()

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

// 定义一个结构体
struct Student {
    char name[20];
    int age;
    double score;
};

int main() {
    Student stu1 = {"张三", 20, 92.5};
    Student stu2 = {"李四", 19, 88.0};
    
    // 打开二进制文件
    ofstream outFile("students.bin", ios::binary);
    
    if (!outFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    // 写入二进制数据
    outFile.write(reinterpret_cast<char*>(&stu1), sizeof(Student));
    outFile.write(reinterpret_cast<char*>(&stu2), sizeof(Student));
    
    outFile.close();
    
    cout << "二进制写入成功！" << endl;
    
    return 0;
}
```

**write() 函数说明**：
```cpp
// 原型
ostream& write(const char* s, streamsize n);

// 参数说明
// s: 要写入的数据的地址（必须是 char* 类型）
// n: 要写入的字节数

// 类型转换
reinterpret_cast<char*>(&stu1)
// 把 Student* 类型的指针转换为 char*
// 因为 write() 只接受 char* 类型
```

**为什么用 reinterpret_cast？**
```cpp
// 普通类型转换会报错
char* p = (char*)&stu1;  // 可能编译警告

// reinterpret_cast 明确表示"重新解释"
char* p = reinterpret_cast<char*>(&stu1);  // 明确告诉编译器

// 这就像：
// 图书管理员（编译器）：你要把这个书架（Student）当书架（Student*）用
// 你说：不，我要把它当成一排字符（char*）来看
```

### 4.4 读取二进制文件：read()

```cpp
#include <fstream>
#include <iostream>
using namespace std;

struct Student {
    char name[20];
    int age;
    double score;
};

int main() {
    ifstream inFile("students.bin", ios::binary);
    
    if (!inFile) {
        cout << "打开文件失败！" << endl;
        return 1;
    }
    
    Student stu;
    
    // 循环读取（读到文件末尾会失败）
    while (inFile.read(reinterpret_cast<char*>(&stu), sizeof(Student))) {
        cout << "姓名:" << stu.name 
             << " 年龄:" << stu.age 
             << " 分数:" << stu.score << endl;
    }
    
    inFile.close();
    
    return 0;
}
```

**read() 函数说明**：
```cpp
// 原型
istream& read(char* s, streamsize n);

// 返回值
// 如果成功读取n个字节，返回流对象（条件为true）
// 如果遇到文件末尾或错误，返回false

// 使用示例
while (inFile.read(buf, size)) {
    // 处理读取的数据
}

// 或者用 gcount() 获取实际读取的字节数
inFile.read(buf, size);
streamsize actual = inFile.gcount();  // 获取实际读取的字节数
```

### 4.5 文本与二进制方式存储对比

```cpp
#include <fstream>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double pi = 3.14159265358979;
    
    // ===== 文本方式保存 =====
    ofstream txtFile("pi.txt");
    txtFile << pi << endl;
    txtFile.close();
    
    // ===== 二进制方式保存 =====
    ofstream binFile("pi.bin", ios::binary);
    binFile.write(reinterpret_cast<char*>(&pi), sizeof(double));
    binFile.close();
    
    // 比较文件大小
    ifstream t1("pi.txt", ios::ate);
    ifstream t2("pi.bin", ios::ate);
    
    cout << "文本文件大小: " << t1.tellg() << " 字节" << endl;
    cout << "二进制文件大小: " << t2.tellg() << " 字节" << endl;
    
    t1.close();
    t2.close();
    
    return 0;
}
```

**运行结果（示例）：**
```
文本文件大小: 18 字节
二进制文件大小: 8 字节
```

### 4.6 二进制文件的高级操作

**处理动态大小的数据（字符串）**

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <vector>
using namespace std;

struct Person {
    int age;
    double height;
    // 字符串不能直接用 sizeof，需要特殊处理
};

int main() {
    // ===== 写入 =====
    ofstream outFile("people.bin", ios::binary);
    
    vector<Person> people = {
        {25, 175.5},
        {30, 180.0},
        {22, 165.0}
    };
    
    // 写入字符串数据
    string name = "张三";
    int nameLen = name.length();
    
    // 先写入名字长度
    outFile.write(reinterpret_cast<char*>(&nameLen), sizeof(int));
    // 再写入名字内容
    outFile.write(name.c_str(), nameLen);
    // 最后写入其他数据
    outFile.write(reinterpret_cast<char*>(&people[0]), sizeof(Person));
    
    outFile.close();
    
    // ===== 读取 =====
    ifstream inFile("people.bin", ios::binary);
    
    // 先读取名字长度
    int len;
    inFile.read(reinterpret_cast<char*>(&len), sizeof(int));
    // 根据长度读取名字
    char* buf = new char[len + 1];
    inFile.read(buf, len);
    buf[len] = '\0';  // 添加字符串结束符
    string readName = buf;
    delete[] buf;
    
    // 读取其他数据
    Person p;
    inFile.read(reinterpret_cast<char*>(&p), sizeof(Person));
    
    cout << "姓名: " << readName << endl;
    cout << "年龄: " << p.age << endl;
    cout << "身高: " << p.height << endl;
    
    inFile.close();
    return 0;
}
```

**使用结构体序列化**

```cpp
#include <fstream>
#include <iostream>
#include <vector>
using namespace std;

struct Student {
    int id;
    char name[50];
    double gpa;
    
    // 序列化方法
    void serialize(ofstream& out) {
        out.write(reinterpret_cast<char*>(&id), sizeof(int));
        out.write(name, 50);
        out.write(reinterpret_cast<char*>(&gpa), sizeof(double));
    }
    
    // 反序列化方法
    void deserialize(ifstream& in) {
        in.read(reinterpret_cast<char*>(&id), sizeof(int));
        in.read(name, 50);
        in.read(reinterpret_cast<char*>(&gpa), sizeof(double));
    }
};

int main() {
    // 写入
    ofstream outFile("class.bin", ios::binary);
    vector<Student> class1 = {
        {1001, "张三", 3.8},
        {1002, "李四", 3.5},
        {1003, "王五", 3.9}
    };
    
    int count = class1.size();
    outFile.write(reinterpret_cast<char*>(&count), sizeof(int));
    
    for (auto& stu : class1) {
        stu.serialize(outFile);
    }
    outFile.close();
    
    // 读取
    ifstream inFile("class.bin", ios::binary);
    int readCount;
    inFile.read(reinterpret_cast<char*>(&readCount), sizeof(int));
    
    cout << "共有 " << readCount << " 名学生:" << endl;
    for (int i = 0; i < readCount; i++) {
        Student stu;
        stu.deserialize(inFile);
        cout << stu.id << " - " << stu.name 
             << " - GPA: " << stu.gpa << endl;
    }
    inFile.close();
    
    return 0;
}
```

---

## 5. 文件状态检查

### 5.1 检查文件是否成功打开

**方法一：使用 if 判断**

```cpp
ifstream inFile("test.txt");
if (!inFile) {
    cout << "文件打开失败" << endl;
}
```

**方法二：使用 is_open() 函数**

```cpp
ifstream inFile("test.txt");
if (!inFile.is_open()) {
    cout << "文件打开失败" << endl;
}
```

**两种方法的区别**：
| 方法 | 优点 | 缺点 |
|------|------|------|
| `!inFile` | 简洁 | 不够明确 |
| `!inFile.is_open()` | 语义明确 | 稍长 |

**检查失败的常见原因**：
1. 文件不存在（读取模式）
2. 文件被其他程序占用
3. 文件夹不存在
4. 没有读取权限
5. 磁盘已满（写入模式）

### 5.2 检查文件操作状态

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream inFile("test.txt");
    
    // 检查各种状态
    if (inFile.eof()) {
        cout << "已到达文件末尾" << endl;
    }
    
    if (inFile.fail()) {
        cout << "文件操作失败" << endl;
    }
    
    if (inFile.bad()) {
        cout << "文件流损坏" << endl;
    }
    
    // 或者直接用 ! 检查
    string s;
    if (!(inFile >> s)) {
        cout << "读取失败" << endl;
    }
    
    inFile.close();
    
    return 0;
}
```

**状态位说明**：
| 状态位 | 含义 | 说明 |
|--------|------|------|
| `eofbit` | 到达文件末尾 | 正常情况，不算错误 |
| `failbit` | 上次操作失败 | 如类型不匹配、格式错误 |
| `badbit` | 流被破坏 | 如磁盘读取错误 |
| `goodbit` | 所有状态正常 | 可以继续操作 |

**状态检查函数**：
```cpp
// 检查函数
inFile.eof()    // 是否到达末尾
inFile.fail()   // 是否有错误（包括格式错误和严重错误）
inFile.bad()    // 是否有严重错误
inFile.good()   // 是否所有状态正常
inFile.operator!()  // 等同于 fail()

// 清除状态
inFile.clear();  // 清除所有错误状态
```

**常见使用场景**：
```cpp
// 场景1：正确读取直到文件末尾
while (inFile >> data) {
    // 处理data
}
// 注意：上面这样写会在格式错误时提前退出

// 场景2：读取后检查是否到达末尾
while (true) {
    inFile >> data;
    if (inFile.eof()) break;  // 到达末尾，正常退出
    if (inFile.fail()) break; // 读取失败，非正常退出
    // 处理data
}

// 场景3：先读后检查
inFile >> data;
if (!inFile.fail() && !inFile.eof()) {
    // 成功读取
}
```

### 5.3 检查文件是否存在

**方法一：尝试打开**

```cpp
bool fileExists(const string& filename) {
    ifstream file(filename);
    return file.good();  // 如果文件可以打开且状态良好
}
```

**方法二：检查 bad() 状态**

```cpp
bool fileExists(const string& filename) {
    ifstream file(filename);
    if (file.bad()) return false;  // 流损坏
    file.close();
    return true;
}
```

**方法三：使用文件大小判断**

```cpp
bool fileExists(const string& filename) {
    ifstream file(filename, ios::ate);  // 打开并移动到末尾
    if (file.fail()) return false;
    // 移动到开头获取文件大小
    streampos size = file.tellg();
    return size >= 0;
}
```

### 5.4 获取文件大小

```cpp
#include <fstream>
#include <iostream>
using namespace std;

streamsize getFileSize(const string& filename) {
    ifstream file(filename, ios::ate | ios::binary);
    if (!file) return -1;
    streamsize size = file.tellg();
    return size;
}

int main() {
    streamsize size = getFileSize("data.bin");
    if (size >= 0) {
        cout << "文件大小: " << size << " 字节" << endl;
        // 转换为 KB、MB
        if (size >= 1024) {
            cout << "约 " << size / 1024.0 << " KB" << endl;
        }
        if (size >= 1024 * 1024) {
            cout << "约 " << size / (1024.0 * 1024) << " MB" << endl;
        }
    } else {
        cout << "文件不存在" << endl;
    }
    return 0;
}
```

---

## 6. 文件定位与随机访问

### 6.1 文件指针概念

文件有两个"指针"：
- **get指针**：下次读取的位置
- **put指针**：下次写入的位置

**图示**：
```
文件内容: [H][e][l][l][o][ ][W][o][r][l][d]
              ↑
           get指针位置 (假设在第二个字符)

文件内容: [H][e][l][l][o][ ][W][o][r][l][d]
                                  ↑
                               put指针位置
```

### 6.2 获取当前位置

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream inFile("test.txt");
    
    // 获取当前读取位置（字节数）
    streampos pos = inFile.tellg();
    cout << "当前读取位置: " << pos << endl;
    
    inFile.close();
    
    ofstream outFile("test.txt");
    
    // 获取当前写入位置
    streampos pos2 = outFile.tellp();
    cout << "当前写入位置: " << pos2 << endl;
    
    outFile.close();
    
    return 0;
}
```

**tellg() 和 tellp() 的区别**：
| 函数 | 全称 | 作用 |
|------|------|------|
| `tellg()` | tell get | 获取读取指针位置 |
| `tellp()` | tell put | 获取写入指针位置 |

### 6.3 移动文件指针

**seekg()：移动读取指针**
```cpp
// 移动到指定位置
inFile.seekg(10);           // 移动到第10个字节
inFile.seekg(0);             // 移动到开头
inFile.seekg(0, ios::end);  // 移动到末尾
inFile.seekg(10, ios::cur); // 从当前位置向后移动10字节
inFile.seekg(-10, ios::cur); // 从当前位置向前移动10字节
```

**seekp()：移动写入指针**
```cpp
// 移动到指定位置
outFile.seekp(10);           // 移动到第10个字节
outFile.seekp(0, ios::end);  // 移动到末尾（追加模式）
```

**seekg()/seekp() 的参数**：
```cpp
// seekg(偏移量, 参考点)
// 参考点可以是：
ios::beg  // 文件开头（默认）
ios::cur  // 当前位置
ios::end  // 文件末尾

// 示例
file.seekg(0);                // 跳到开头
file.seekg(0, ios::beg);      // 同上，跳到开头
file.seekg(0, ios::end);     // 跳到末尾
file.seekg(10, ios::cur);     // 从当前位置向后10字节
file.seekg(-10, ios::end);   // 从末尾向前10字节
```

### 6.4 实战：文件内容的随机读取

**实例1：读取文件任意位置的数据**

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
    // 先创建一个测试文件
    ofstream outFile("lines.txt");
    for (int i = 1; i <= 5; i++) {
        outFile << "这是第 " << i << " 行" << endl;
    }
    outFile.close();
    
    // 打开文件读取
    ifstream inFile("lines.txt");
    string line;
    
    // 读取第3行（跳过前2行）
    inFile.seekg(0);  // 先回到开头
    for (int i = 0; i < 2; i++) {
        getline(inFile, line);  // 跳过前两行
    }
    getline(inFile, line);  // 读第3行
    cout << "第3行: " << line << endl;
    
    // 读取最后一行
    inFile.seekg(0, ios::end);  // 移动到文件末尾
    // 注意：文本模式下反向定位可能不准，这里演示原理
    
    inFile.close();
    
    return 0;
}
```

**实例2：修改文件中的特定位置**

```cpp
#include <fstream>
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 创建初始文件
    ofstream outFile("scores.bin", ios::binary);
    vector<int> scores = {90, 85, 92, 88, 95};
    for (int s : scores) {
        outFile.write(reinterpret_cast<char*>(&s), sizeof(int));
    }
    outFile.close();
    
    // 修改第3个成绩（索引2）从92改为100
    fstream ioFile("scores.bin", ios::binary | ios::in | ios::out);
    
    // 定位到第3个成绩的位置
    int index = 2;
    int newScore = 100;
    ioFile.seekp(index * sizeof(int), ios::beg);
    
    // 写入新成绩
    ioFile.write(reinterpret_cast<char*>(&newScore), sizeof(int));
    
    // 验证修改
    ioFile.seekg(0);
    cout << "修改后的成绩: ";
    int readScore;
    for (int i = 0; i < 5; i++) {
        ioFile.read(reinterpret_cast<char*>(&readScore), sizeof(int));
        cout << readScore << " ";
    }
    cout << endl;
    
    ioFile.close();
    return 0;
}
```

**实例3：倒序读取文件**

```cpp
#include <fstream>
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 创建测试文件
    ofstream outFile("numbers.bin", ios::binary);
    for (int i = 1; i <= 10; i++) {
        outFile.write(reinterpret_cast<char*>(&i), sizeof(int));
    }
    outFile.close();
    
    // 倒序读取
    ifstream inFile("numbers.bin", ios::binary);
    
    // 先获取文件大小
    inFile.seekg(0, ios::end);
    streamsize fileSize = inFile.tellg();
    int count = fileSize / sizeof(int);
    
    cout << "倒序输出: ";
    for (int i = count - 1; i >= 0; i--) {
        inFile.seekg(i * sizeof(int), ios::beg);
        int num;
        inFile.read(reinterpret_cast<char*>(&num), sizeof(int));
        cout << num << " ";
    }
    cout << endl;
    
    inFile.close();
    return 0;
}
```

### 6.5 二进制文件随机访问的优势

**为什么二进制文件更适合随机访问？**

```cpp
// 文本文件的问题
ofstream out("data.txt");
out << "张三" << endl;  // 占多少字节？不确定（中文编码）
out << "李四" << endl;

// 读取第2个人
getline(file, line);  // 需要先读第1行
getline(file, line);  // 才能读到第2行

// 二进制文件的优势
struct Person {
    char name[20];  // 固定20字节
    int age;        // 固定4字节
    double height;  // 固定8字节
};
// 每条记录固定 20 + 4 + 8 = 32 字节

// 直接读取第N条记录
fstream file("data.bin", ios::binary | ios::in | ios::out);
Person p;
file.seekg(n * sizeof(Person), ios::beg);  // 直接跳到第N条
file.read(reinterpret_cast<char*>(&p), sizeof(Person));
```

---

## 7. 综合案例：学生信息管理系统

### 7.1 需求分析

实现一个简单的学生信息管理系统：
- 添加学生信息
- 查看所有学生
- 按学号查找
- 修改学生信息
- 删除学生
- 保存到文件
- 从文件加载

### 7.2 完整代码

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
#include <algorithm>
using namespace std;

// 学生结构体
struct Student {
    int id;         // 学号
    string name;    // 姓名
    int age;        // 年龄
    double score;   // 成绩
};

// 学生管理器类
class StudentManager {
private:
    vector<Student> students;
    const string filename = "students.dat";
    
public:
    // 构造函数：自动加载数据
    StudentManager() {
        loadFromFile();
    }
    
    // 析构函数：自动保存数据
    ~StudentManager() {
        saveToFile();
    }
    
    // 添加学生
    void addStudent() {
        Student stu;
        cout << "请输入学号: ";
        cin >> stu.id;
        
        // 检查学号是否重复
        if (findById(stu.id)) {
            cout << "学号已存在！" << endl;
            return;
        }
        
        cout << "请输入姓名: ";
        cin >> stu.name;
        cout << "请输入年龄: ";
        cin >> stu.age;
        cout << "请输入成绩: ";
        cin >> stu.score;
        
        students.push_back(stu);
        cout << "添加成功！" << endl;
    }
    
    // 显示所有学生
    void showAll() {
        if (students.empty()) {
            cout << "暂无学生信息" << endl;
            return;
        }
        
        cout << setw(8) << "学号" 
             << setw(10) << "姓名" 
             << setw(6) << "年龄" 
             << setw(8) << "成绩" << endl;
        cout << string(32, '-') << endl;
        
        for (const auto& stu : students) {
            cout << setw(8) << stu.id 
                 << setw(10) << stu.name 
                 << setw(6) << stu.age 
                 << setw(8) << fixed << setprecision(1) << stu.score 
                 << endl;
        }
    }
    
    // 按学号查找
    Student* findById(int id) {
        for (auto& stu : students) {
            if (stu.id == id) {
                return &stu;
            }
        }
        return nullptr;
    }
    
    void searchById(int id) {
        Student* stu = findById(id);
        if (stu) {
            cout << "找到学生：" << endl;
            cout << "学号: " << stu->id << endl;
            cout << "姓名: " << stu->name << endl;
            cout << "年龄: " << stu->age << endl;
            cout << "成绩: " << stu->score << endl;
        } else {
            cout << "未找到该学号的学生" << endl;
        }
    }
    
    // 修改学生信息
    void modifyStudent(int id) {
        Student* stu = findById(id);
        if (!stu) {
            cout << "未找到该学号的学生" << endl;
            return;
        }
        
        cout << "当前信息:" << endl;
        cout << "姓名: " << stu->name << endl;
        cout << "年龄: " << stu->age << endl;
        cout << "成绩: " << stu->score << endl;
        
        cout << "\n请输入新信息（直接回车保留原值）:" << endl;
        string input;
        
        cout << "姓名 [" << stu->name << "]: ";
        cin >> input;
        if (!input.empty()) stu->name = input;
        
        cout << "年龄 [" << stu->age << "]: ";
        cin >> input;
        if (!input.empty()) stu->age = stoi(input);
        
        cout << "成绩 [" << stu->score << "]: ";
        cin >> input;
        if (!input.empty()) stu->score = stod(input);
        
        cout << "修改成功！" << endl;
    }
    
    // 删除学生
    void deleteStudent(int id) {
        auto it = remove_if(students.begin(), students.end(),
            [id](const Student& s) { return s.id == id; });
        
        if (it != students.end()) {
            students.erase(it, students.end());
            cout << "删除成功！" << endl;
        } else {
            cout << "未找到该学号的学生" << endl;
        }
    }
    
    // 保存到文件（二进制方式）
    void saveToFile() {
        ofstream outFile(filename, ios::binary | ios::trunc);
        
        if (!outFile) {
            cout << "保存文件失败！" << endl;
            return;
        }
        
        // 先写入学生数量
        int count = students.size();
        outFile.write(reinterpret_cast<char*>(&count), sizeof(int));
        
        // 再写入每个学生的信息
        for (const auto& stu : students) {
            // 写入ID
            outFile.write(reinterpret_cast<const char*>(&stu.id), sizeof(int));
            
            // 写入姓名（固定长度，便于读取）
            char nameBuf[50] = {0};
            stu.name.copy(nameBuf, stu.name.length());
            outFile.write(nameBuf, sizeof(nameBuf));
            
            // 写入年龄和分数
            outFile.write(reinterpret_cast<const char*>(&stu.age), sizeof(int));
            outFile.write(reinterpret_cast<const char*>(&stu.score), sizeof(double));
        }
        
        outFile.close();
        cout << "保存成功！" << endl;
    }
    
    // 从文件加载
    void loadFromFile() {
        ifstream inFile(filename, ios::binary);
        
        if (!inFile) {
            cout << "文件不存在，将创建新数据" << endl;
            return;
        }
        
        students.clear();
        
        // 先读取学生数量
        int count;
        inFile.read(reinterpret_cast<char*>(&count), sizeof(int));
        
        // 读取每个学生
        for (int i = 0; i < count; i++) {
            Student stu;
            
            // 读取ID
            inFile.read(reinterpret_cast<char*>(&stu.id), sizeof(int));
            
            // 读取姓名
            char nameBuf[50];
            inFile.read(nameBuf, sizeof(nameBuf));
            stu.name = nameBuf;
            
            // 读取年龄和分数
            inFile.read(reinterpret_cast<char*>(&stu.age), sizeof(int));
            inFile.read(reinterpret_cast<char*>(&stu.score), sizeof(double));
            
            students.push_back(stu);
        }
        
        inFile.close();
        cout << "加载了 " << students.size() << " 条学生记录" << endl;
    }
    
    // 按成绩排序
    void sortByScore() {
        sort(students.begin(), students.end(),
            [](const Student& a, const Student& b) {
                return a.score > b.score;
            });
        cout << "排序完成！" << endl;
    }
    
    // 获取学生总数
    int getCount() const {
        return students.size();
    }
};

// 主菜单
void showMenu() {
    cout << "\n===== 学生信息管理系统 =====" << endl;
    cout << "1. 添加学生" << endl;
    cout << "2. 显示所有学生" << endl;
    cout << "3. 按学号查找" << endl;
    cout << "4. 修改学生信息" << endl;
    cout << "5. 删除学生" << endl;
    cout << "6. 按成绩排序" << endl;
    cout << "0. 退出" << endl;
    cout << "请选择: ";
}

int main() {
    StudentManager manager;
    int choice;
    
    while (true) {
        showMenu();
        cin >> choice;
        
        switch (choice) {
            case 1:
                manager.addStudent();
                break;
            case 2:
                manager.showAll();
                break;
            case 3:
                cout << "请输入要查找的学号: ";
                int id;
                cin >> id;
                manager.searchById(id);
                break;
            case 4:
                cout << "请输入要修改的学号: ";
                cin >> id;
                manager.modifyStudent(id);
                break;
            case 5:
                cout << "请输入要删除的学号: ";
                cin >> id;
                manager.deleteStudent(id);
                break;
            case 6:
                manager.sortByScore();
                break;
            case 0:
                cout << "退出系统，数据会自动保存" << endl;
                return 0;
            default:
                cout << "无效选择，请重新输入" << endl;
        }
    }
    
    return 0;
}
```

### 7.3 运行示例

```
===== 学生信息管理系统 =====
1. 添加学生
2. 显示所有学生
3. 按学号查找
4. 修改学生信息
5. 删除学生
6. 按成绩排序
0. 退出
请选择: 1
请输入学号: 1001
请输入姓名: 张三
请输入年龄: 20
请输入成绩: 92.5
添加成功！

请选择: 1
请输入学号: 1002
请输入姓名: 李四
请输入年龄: 19
请输入成绩: 88.0
添加成功！

请选择: 2
      学号        姓名   年龄     成绩
--------------------------------
    1001       张三     20     92.5
    1002       李四     19     88.0

请选择: 6
排序完成！

请选择: 2
      学号        姓名   年龄     成绩
--------------------------------
    1001       张三     20     92.5
    1002       李四     19     88.0

请选择: 0
退出系统，数据会自动保存
```

### 7.4 系统扩展

**扩展1：支持批量导入**

```cpp
void importFromCSV(const string& filename) {
    ifstream inFile(filename);
    if (!inFile) {
        cout << "文件打开失败" << endl;
        return;
    }
    
    string line;
    int count = 0;
    
    // 跳过标题行
    getline(inFile, line);
    
    while (getline(inFile, line)) {
        stringstream ss(line);
        string id, name, age, score;
        
        getline(ss, id, ',');
        getline(ss, name, ',');
        getline(ss, age, ',');
        getline(ss, score, ',');
        
        Student stu;
        stu.id = stoi(id);
        stu.name = name;
        stu.age = stoi(age);
        stu.score = stod(score);
        
        students.push_back(stu);
        count++;
    }
    
    inFile.close();
    cout << "成功导入 " << count << " 条记录" << endl;
}
```

**扩展2：支持导出CSV**

```cpp
void exportToCSV(const string& filename) {
    ofstream outFile(filename);
    if (!outFile) {
        cout << "文件创建失败" << endl;
        return;
    }
    
    // 写入标题
    outFile << "学号,姓名,年龄,成绩" << endl;
    
    // 写入数据
    for (const auto& stu : students) {
        outFile << stu.id << ","
                << stu.name << ","
                << stu.age << ","
                << stu.score << endl;
    }
    
    outFile.close();
    cout << "成功导出到 " << filename << endl;
}
```

---

## 8. 常见错误与注意事项

### 8.1 忘记包含头文件

```cpp
// 错误：忘记包含头文件
// ofstream outFile("test.txt");  // 编译错误！

// 正确：包含头文件
#include <fstream>
// ...
```

**相关头文件**：
```cpp
#include <fstream>   // 文件流
#include <iomanip>   // 格式化输出
#include <sstream>   // 字符串流
```

### 8.2 文件打开失败没有检查

```cpp
// 危险：没有检查文件是否打开成功
ofstream outFile("test.txt");
outFile << "Hello";  // 如果文件打开失败，这里会崩溃

// 安全：先检查
ofstream outFile("test.txt");
if (!outFile) {
    cerr << "打开文件失败" << endl;
    return 1;
}
outFile << "Hello";
```

### 8.3 忘记关闭文件

```cpp
// 可能丢失数据
void writeData() {
    ofstream outFile("data.txt");
    outFile << "重要数据";
    // 函数结束，outFile被销毁
    // 但如果析构函数没来得及执行，数据可能没写入磁盘
}

// 正确：显式关闭
void writeData() {
    ofstream outFile("data.txt");
    if (outFile) {
        outFile << "重要数据";
        outFile.close();  // 显式关闭，确保数据写入
    }
}

// 最佳：使用 RAII（推荐）
void writeData() {
    // 文件在构造函数打开，析构函数自动关闭
    ofstream outFile("data.txt");
    outFile << "重要数据";
    // 函数结束时自动关闭
}
```

### 8.4 二进制文件使用文本模式

```cpp
// 错误：在Windows下，文本模式会改变换行符
ofstream outFile("data.bin");  // 默认文本模式
outFile.write((char*)&num, sizeof(int));  // 可能出问题

// 正确：二进制模式
ofstream outFile("data.bin", ios::binary);
outFile.write((char*)&num, sizeof(int));  // 原样写入
```

**Windows 换行符问题**：
- 文本模式：`\n` 会变成 `\r\n`
- 二进制模式：`\n` 保持原样
- Linux/Mac：一般没有这个问题

### 8.5 路径问题

```cpp
// Windows 路径分隔符
ofstream f1("C:\\folder\\file.txt");  // 转义
ofstream f2("C:/folder/file.txt");   // 正斜杠也可以

// Linux/Mac 路径
ofstream f3("/home/user/file.txt");  // 使用正斜杠

// 相对路径（相对于可执行文件）
ofstream f4("data.txt");  // 和程序在同一目录
ofstream f5("./data.txt");  // 同上

// 相对路径（相对于当前工作目录）
ofstream f6("../data.txt");  // 上级目录
```

### 8.6 文件指针位置问题

```cpp
// 容易犯的错误：读取后没重置指针
ifstream inFile("test.txt");
int a, b;
inFile >> a;       // 读取完后，指针在a之后
inFile >> b;       // 正确，会继续读
// 但如果想重新读a：
// inFile >> a;  // 这时会读b的值！

// 正确：需要重置指针
inFile.clear();    // 清除EOF标志
inFile.seekg(0);   // 重置到开头
inFile >> a;       // 重新读取
```

**注意**：清除 EOF 标志很重要！
```cpp
// 如果不清除 EOF 标志
inFile.seekg(0);   // seek 到开头
inFile >> a;       // 仍然失败！因为 EOF 标志还在

// 正确做法
inFile.clear();    // 先清除所有错误标志
inFile.seekg(0);    // 再移动指针
inFile >> a;        // 现在可以正常读取
```

### 8.7 二进制文件结构体对齐问题

```cpp
// 问题：结构体可能有填充字节
struct BadStudent {
    char name[20];  // 20 字节
    int age;        // 4 字节
    double score;   // 8 字节
};
// 实际大小可能是 40 字节（编译器对齐）

// 解决1：使用 #pragma pack
#pragma pack(push, 1)
struct GoodStudent {
    char name[20];
    int age;
    double score;
};
#pragma pack(pop)

// 解决2：始终使用 sizeof() 获取实际大小
ofstream out("data.bin", ios::binary);
GoodStudent stu;
out.write(reinterpret_cast<char*>(&stu), sizeof(GoodStudent));  // 正确
```

### 8.8 中文路径和编码问题

```cpp
// Windows 中文路径问题
// 在一些编译器中，中文路径可能有问题

// 建议：使用英文路径，或者
// 1. 将路径转换为宽字符
#include <codecvt>
#include <locale>
wstring toWString(const string& str) {
    wstring_convert<codecvt_utf8<wchar_t>> converter;
    return converter.from_bytes(str);
}

// 2. 使用宽字符版本的函数
wofstream outFile(toWString("中文路径.txt"));
```

---

## 9. 文件操作与异常的结合

### 9.1 为什么要结合异常

**文件操作容易出错的场景**：
- 文件不存在
- 文件被占用
- 磁盘空间不足
- 权限不足
- 文件损坏

**异常的优势**：
- 可以传播错误信息
- 可以统一处理各种错误
- 代码更清晰

### 9.2 使用异常处理文件操作

```cpp
#include <fstream>
#include <iostream>
#include <stdexcept>
using namespace std;

class FileException : public exception {
private:
    string message;
public:
    FileException(const string& msg) : message(msg) {}
    const char* what() const noexcept override {
        return message.c_str();
    }
};

void readFile(const string& filename) {
    ifstream inFile(filename);
    
    if (!inFile) {
        throw FileException("无法打开文件: " + filename);
    }
    
    // 读取文件...
    string content((istreambuf_iterator<char>(inFile)),
                   istreambuf_iterator<char>());
    
    inFile.close();
    cout << "文件内容: " << content << endl;
}

void writeFile(const string& filename, const string& content) {
    ofstream outFile(filename);
    
    if (!outFile) {
        throw FileException("无法创建文件: " + filename);
    }
    
    outFile << content;
    
    if (!outFile) {
        throw FileException("写入文件失败: " + filename);
    }
    
    outFile.close();
}

int main() {
    try {
        readFile("test.txt");
        writeFile("output.txt", "Hello, World!");
    }
    catch (const FileException& e) {
        cerr << "文件错误: " << e.what() << endl;
        return 1;
    }
    catch (const exception& e) {
        cerr << "一般错误: " << e.what() << endl;
        return 1;
    }
    
    return 0;
}
```

### 9.3 使用 RAII + 异常确保文件关闭

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

// 安全的文件写入类（RAII）
class SafeWriter {
private:
    ofstream file;
    string filename;
    bool opened;
    
public:
    SafeWriter(const string& fname) : filename(fname), opened(false) {
        file.open(filename, ios::binary | ios::trunc);
        if (file) {
            opened = true;
        }
    }
    
    ~SafeWriter() {
        if (opened) {
            // 确保文件正确关闭
            if (file.is_open()) {
                file.close();
            }
        }
    }
    
    void write(const void* data, size_t size) {
        if (!opened) {
            throw runtime_error("文件未打开");
        }
        file.write(static_cast<const char*>(data), size);
        if (!file) {
            throw runtime_error("写入失败");
        }
    }
    
    // 禁用拷贝
    SafeWriter(const SafeWriter&) = delete;
    SafeWriter& operator=(const SafeWriter&) = delete;
};

int main() {
    try {
        SafeWriter writer("data.bin");
        
        int numbers[] = {1, 2, 3, 4, 5};
        writer.write(numbers, sizeof(numbers));
        
        cout << "写入成功！" << endl;
    }
    catch (const exception& e) {
        cerr << "错误: " << e.what() << endl;
        return 1;
    }
    
    return 0;
}
```

### 9.4 异常安全的文件操作模板

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <vector>
#include <functional>
using namespace std;

// 模板：异常安全的文件操作
template<typename T>
void atomicWrite(const string& filename, 
                 const T& data,
                 const function<void(ofstream&, const T&)>& serializer) {
    
    string tempFile = filename + ".tmp";
    
    // 第一步：写入临时文件
    ofstream temp(tempFile, ios::binary | ios::trunc);
    if (!temp) {
        throw runtime_error("无法创建临时文件");
    }
    
    serializer(temp, data);
    temp.close();
    
    // 第二步：替换原文件（原子操作）
    // 如果原文件不存在，直接改名
    // 如果原文件存在，替换它
    ifstream check(filename);
    bool exists = check.good();
    check.close();
    
    if (exists) {
        if (remove(filename.c_str()) != 0) {
            // 删除失败，删除临时文件
            remove(tempFile.c_str());
            throw runtime_error("无法替换原文件");
        }
    }
    
    if (rename(tempFile.c_str(), filename.c_str()) != 0) {
        // 重命名失败
        throw runtime_error("无法重命名临时文件");
    }
}

int main() {
    vector<int> data = {1, 2, 3, 4, 5};
    
    try {
        atomicWrite("numbers.bin", data, 
            [](ofstream& out, const vector<int>& v) {
                for (int n : v) {
                    out.write(reinterpret_cast<const char*>(&n), sizeof(int));
                }
            });
        cout << "写入成功" << endl;
    }
    catch (const exception& e) {
        cerr << "错误: " << e.what() << endl;
    }
    
    return 0;
}
```

---

## 10. 实战技巧与最佳实践

### 10.1 性能优化技巧

**技巧1：减少磁盘写入次数**

```cpp
// 错误：频繁写入
for (int i = 0; i < 10000; i++) {
    ofstream out("data.txt", ios::app);
    out << i << endl;
    out.close();  // 每次都关闭，效率低
}

// 正确：一次性写入
ofstream out("data.txt");
for (int i = 0; i < 10000; i++) {
    out << i << endl;
}
out.close();  // 最后一次性关闭
```

**技巧2：使用缓冲区刷新控制**

```cpp
ofstream out("data.txt");
// 写入大量数据
for (int i = 0; i < 1000000; i++) {
    out << data[i] << endl;
    // 不要每条都刷新，太慢
    // out.flush();  // 删除这行
}
// 最后一次性刷新和关闭
out.flush();
out.close();
```

**技巧3：使用二进制格式减少数据量**

```cpp
// 文本格式：每个数字平均 5-10 个字符
ofstream out1("text.txt");
out1 << 12345678 << endl;  // 占用 9 字节

// 二进制格式：固定 4 或 8 字节
ofstream out2("binary.bin", ios::binary);
int num = 12345678;
out2.write(reinterpret_cast<char*>(&num), sizeof(int));  // 占用 4 字节
```

### 10.2 大文件处理

**分块读写大文件**

```cpp
#include <fstream>
#include <iostream>
#include <vector>
using namespace std;

const size_t BUFFER_SIZE = 1024 * 1024;  // 1MB 缓冲区

void copyLargeFile(const string& src, const string& dst) {
    ifstream inFile(src, ios::binary);
    if (!inFile) {
        throw runtime_error("无法打开源文件");
    }
    
    ofstream outFile(dst, ios::binary | ios::trunc);
    if (!outFile) {
        throw runtime_error("无法创建目标文件");
    }
    
    vector<char> buffer(BUFFER_SIZE);
    size_t totalRead = 0;
    size_t totalWrite = 0;
    
    while (inFile.read(buffer.data(), BUFFER_SIZE)) {
        streamsize bytesRead = inFile.gcount();
        outFile.write(buffer.data(), bytesRead);
        totalRead += bytesRead;
        totalWrite += bytesRead;
        
        // 显示进度
        cout << "\r已复制: " << totalRead / (1024 * 1024) << " MB" 
             << flush;
    }
    
    // 处理最后可能不完整的块
    streamsize bytesRead = inFile.gcount();
    if (bytesRead > 0) {
        outFile.write(buffer.data(), bytesRead);
        totalRead += bytesRead;
        totalWrite += bytesRead;
    }
    
    inFile.close();
    outFile.close();
    
    cout << endl;
    cout << "复制完成: " << totalRead << " 字节" << endl;
}
```

### 10.3 文件锁定

**防止多进程同时写入**

```cpp
#include <fstream>
#include <iostream>
#include <thread>
#include <chrono>
using namespace std;

// 简单的文件锁实现
class FileLock {
private:
    string lockFile;
    bool locked;
    
public:
    FileLock(const string& file) : lockFile(file + ".lock"), locked(false) {
        // 尝试创建锁文件
        ofstream out(lockFile, ios::trunc);
        if (!out) {
            throw runtime_error("无法创建锁文件");
        }
        out << this_thread::get_id() << endl;
        out.close();
        locked = true;
    }
    
    ~FileLock() {
        if (locked) {
            remove(lockFile.c_str());
        }
    }
    
    bool isLocked() const {
        return locked;
    }
};

// 使用示例
void safeWrite(const string& filename, const string& data) {
    FileLock lock(filename);
    if (!lock.isLocked()) {
        cout << "文件被占用，请稍后重试" << endl;
        return;
    }
    
    ofstream out(filename, ios::app);
    out << data << endl;
    out.close();
}
```

### 10.4 配置文件读写

**INI 格式配置文件**

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <map>
#include <sstream>
using namespace std;

class ConfigFile {
private:
    map<string, map<string, string>> data;
    
public:
    // 读取配置文件
    void load(const string& filename) {
        ifstream inFile(filename);
        if (!inFile) {
            throw runtime_error("无法打开配置文件");
        }
        
        string currentSection;
        string line;
        
        while (getline(inFile, line)) {
            // 跳过空行和注释
            if (line.empty() || line[0] == ';' || line[0] == '#') {
                continue;
            }
            
            // 解析节
            if (line[0] == '[') {
                size_t pos = line.find(']');
                if (pos != string::npos) {
                    currentSection = line.substr(1, pos - 1);
                }
                continue;
            }
            
            // 解析键值对
            size_t pos = line.find('=');
            if (pos != string::npos) {
                string key = line.substr(0, pos);
                string value = line.substr(pos + 1);
                
                // 去除首尾空白
                key = trim(key);
                value = trim(value);
                
                data[currentSection][key] = value;
            }
        }
        
        inFile.close();
    }
    
    // 保存配置文件
    void save(const string& filename) {
        ofstream outFile(filename);
        if (!outFile) {
            throw runtime_error("无法创建配置文件");
        }
        
        for (const auto& section : data) {
            outFile << "[" << section.first << "]" << endl;
            for (const auto& pair : section.second) {
                outFile << pair.first << "=" << pair.second << endl;
            }
            outFile << endl;
        }
        
        outFile.close();
    }
    
    // 获取值
    string get(const string& section, const string& key, 
               const string& defaultValue = "") {
        auto secIt = data.find(section);
        if (secIt != data.end()) {
            auto keyIt = secIt->second.find(key);
            if (keyIt != secIt->second.end()) {
                return keyIt->second;
            }
        }
        return defaultValue;
    }
    
    // 设置值
    void set(const string& section, const string& key, 
             const string& value) {
        data[section][key] = value;
    }
    
private:
    // 去除首尾空白
    string trim(const string& s) {
        size_t start = s.find_first_not_of(" \t\r\n");
        if (start == string::npos) return "";
        size_t end = s.find_last_not_of(" \t\r\n");
        return s.substr(start, end - start + 1);
    }
};

int main() {
    ConfigFile config;
    
    // 读取配置
    try {
        config.load("settings.ini");
    } catch (...) {
        // 文件不存在，使用默认值
    }
    
    // 读取配置
    string host = config.get("database", "host", "localhost");
    int port = stoi(config.get("database", "port", "3306"));
    
    cout << "数据库: " << host << ":" << port << endl;
    
    // 修改配置
    config.set("database", "host", "192.168.1.100");
    config.set("database", "port", "5432");
    config.set("app", "debug", "true");
    
    // 保存配置
    config.save("settings.ini");
    
    return 0;
}
```

**INI 文件格式示例**：
```ini
[database]
host=192.168.1.100
port=5432
username=admin
password=secret

[app]
debug=true
log_level=info
```

### 10.5 日志系统

**简单的日志文件类**

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <ctime>
#include <iomanip>
using namespace std;

enum class LogLevel {
    DEBUG,
    INFO,
    WARNING,
    ERROR
};

class Logger {
private:
    ofstream file;
    LogLevel minLevel;
    
public:
    Logger(const string& filename, LogLevel level = LogLevel::INFO) 
        : minLevel(level) {
        file.open(filename, ios::app);
        if (!file) {
            throw runtime_error("无法打开日志文件");
        }
    }
    
    ~Logger() {
        if (file.is_open()) {
            file.close();
        }
    }
    
    void log(LogLevel level, const string& message) {
        if (level < minLevel) return;
        
        // 获取时间戳
        time_t now = time(nullptr);
        tm* local = localtime(&now);
        
        file << "[" << put_time(local, "%Y-%m-%d %H:%M:%S") << "] ";
        file << "[" << levelToString(level) << "] ";
        file << message << endl;
        
        // 如果是错误级别，同时输出到控制台
        if (level >= LogLevel::ERROR) {
            cerr << message << endl;
        }
    }
    
    void debug(const string& msg) { log(LogLevel::DEBUG, msg); }
    void info(const string& msg) { log(LogLevel::INFO, msg); }
    void warning(const string& msg) { log(LogLevel::WARNING, msg); }
    void error(const string& msg) { log(LogLevel::ERROR, msg); }
    
private:
    string levelToString(LogLevel level) {
        switch (level) {
            case LogLevel::DEBUG: return "DEBUG";
            case LogLevel::INFO: return "INFO";
            case LogLevel::WARNING: return "WARNING";
            case LogLevel::ERROR: return "ERROR";
            default: return "UNKNOWN";
        }
    }
};

int main() {
    Logger logger("app.log", LogLevel::DEBUG);
    
    logger.debug("调试信息：开始初始化");
    logger.info("程序启动");
    logger.warning("这是一个警告");
    logger.error("发生错误！");
    
    return 0;
}
```

**日志文件内容示例**：
```
[2026-04-19 10:30:15] [DEBUG] 调试信息：开始初始化
[2026-04-19 10:30:15] [INFO] 程序启动
[2026-04-19 10:30:15] [WARNING] 这是一个警告
[2026-04-19 10:30:15] [ERROR] 发生错误！
```

---

## 11. 本章小结

### 核心知识点

1. **文件流类**
   - `ifstream`：读取文件
   - `ofstream`：写入文件
   - `fstream`：读写文件

2. **打开文件**
   - 构造函数或 `open()` 方法
   - 多种打开模式：`in`, `out`, `app`, `binary` 等

3. **文本文件操作**
   - 使用 `<<` 写入，`>>` 读取
   - `getline()` 读取整行
   - `get()` 逐字符读取

4. **二进制文件操作**
   - `write()` 写入，`read()` 读取
   - 需要 `ios::binary` 模式
   - 使用 `sizeof()` 获取结构体大小

5. **文件状态检查**
   - `is_open()`：检查是否打开
   - `eof()`：检查是否到末尾
   - `fail()`：检查操作是否失败
   - `clear()`：清除错误状态

6. **文件定位**
   - `tellg()`/`tellp()`：获取位置
   - `seekg()`/`seekp()`：移动位置
   - 支持从开头、当前位置、末尾三种参考点

### 学习建议

1. **先从文本文件开始**：操作简单，容易调试
2. **注意检查文件状态**：打开失败是常见问题
3. **二进制文件适合结构化数据**：如学生信息、游戏存档
4. **记得关闭文件**：确保数据写入磁盘
5. **使用RAII**：构造函数打开，析构函数关闭
6. **结合异常处理**：让错误处理更优雅
7. **注意路径兼容性**：使用正斜杠 `/` 更安全

### 常见错误汇总

| 错误 | 后果 | 解决方法 |
|------|------|----------|
| 不检查文件是否打开 | 程序崩溃 | 始终检查 `if (!file)` |
| 不关闭文件 | 数据丢失 | 使用 RAII 或显式 `close()` |
| 文本模式写入二进制数据 | 数据损坏 | 使用 `ios::binary` |
| 不清除 EOF 标志就 seek | 读取失败 | `seekp()` 前先 `clear()` |
| 结构体包含字符串成员 | 二进制读写出错 | 使用固定长度字符数组 |

### 练习题

**基础题**
1. 编写程序，将1-100写入文件 `numbers.txt`
2. 编写程序，读取 `numbers.txt` 并计算总和
3. 编写程序，复制一个文本文件

**进阶题**
4. 编写学生信息管理系统，支持添加、查看、保存、加载
5. 用二进制方式存储学生信息，比较和文本方式的文件大小
6. 实现文件加密功能：对文件每个字节进行XOR运算

**挑战题**
7. 实现一个简单的日志系统，支持不同级别的日志
8. 实现一个配置文件的读写类（支持 INI 格式）
9. 实现大文件的分块复制功能，并显示进度条

### 下章预告

下一章我们将学习**命名空间与C++新特性**，掌握现代C++的重要特性。

主要内容：
- 命名空间（namespace）的使用
- 智能指针（unique_ptr, shared_ptr, weak_ptr）
- lambda 表达式
- auto 和 decltype
- 范围 for 循环
- C++11/14/17/20 其他新特性

---

*返回 [主目录](../README.md)*
