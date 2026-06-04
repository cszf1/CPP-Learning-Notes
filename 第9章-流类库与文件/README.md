# 第9章 流类库与文件

> 本章目标：学会输入输出控制和文件读写。内存像草稿纸，文件像笔记本；程序结束后，草稿纸会没，笔记本还能留。

## 目录

1. 流的概念
2. 标准输入输出
3. 格式控制
4. 文件流类
5. 文本文件读写
6. 二进制文件读写
7. 文件状态与 eof 陷阱
8. 文件定位与随机访问
9. 文件与对象
10. 本章小结
## 1. 流的概念

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




## 2. 标准输入输出

```cpp
int x;
cin >> x;
cout << "x = " << x << endl;
```

`>>` 默认跳过空白字符，`getline` 读取整行。混用时注意残留换行：

```cpp
cin >> n;
cin.ignore();
getline(cin, line);
```



## 3. 格式控制

需要 `<iomanip>`：

```cpp
cout << fixed << setprecision(2) << 3.14159;
cout << setw(10) << left << "name";
```

常用控制：

- `fixed`：定点小数格式。
- `setprecision(n)`：控制精度。
- `setw(n)`：设置输出宽度。
- `left/right`：左/右对齐。



## 4. 文件流类

### 4.1 文件流类家族

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

### 4.2 打开文件

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

### 4.3 打开模式

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

### 4.4 关闭文件

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




## 5. 文本文件读写

### 5.1 写入文本文件

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

### 5.2 格式化输出到文件

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

### 5.3 读取文本文件

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

### 5.4 使用 getline() 读取整行

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

### 5.5 逐字符读取

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

### 5.6 综合实例：文本文件处理

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




## 6. 二进制文件读写

### 8.1 文本文件 vs 二进制文件

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

### 8.2 打开二进制文件

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

### 8.3 写入二进制文件：write()

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

### 8.4 读取二进制文件：read()

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

### 8.5 文本与二进制方式存储对比

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

### 8.6 二进制文件的高级操作

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




## 7. 文件状态与 eof 陷阱

不要这样：

```cpp
while (!in.eof()) {
    in >> x;
}
```

推荐：

```cpp
while (in >> x) {
    // 读取成功再处理
}
```

`eof()` 是读失败后才知道到了末尾，不是预言家。



## 8. 文件定位与随机访问

### 8.1 文件指针概念

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

### 8.2 获取当前位置

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

### 8.3 移动文件指针

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

### 8.4 实战：文件内容的随机读取

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

### 8.5 二进制文件随机访问的优势

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





### 10.1 常见错误与注意事项

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






## 9. 文件与对象

### 9.1 面向对象的文件操作框架

在面向对象程序设计中，信息总是放在对象的数据成员里，这些信息最终应该保存到文件中。

**规范的面向对象编程方法：**

1. **程序开始运行时**：由打开的文件重新创建对象（构造函数中读文件）
2. **运行过程中**：对象的数据成员得到利用和修改
3. **运行结束时**：把信息重新保存到文件中，然后关闭文件（析构函数中写文件）

> 💡 这是面向对象 C++ 程序设计的**固定框架**：构造函数中打开文件并创建对象，析构函数中保存数据并关闭文件。

### 9.2 综合案例：货物数组类

**需求：**
- 将商店的货物定义为一个货物数组类
- 数组对象动态建立，初始为2个元素，不够用时扩充一倍
- 用文本数据文件建立数组元素对象（构造函数），数据保存和文件关闭放在析构函数
- 第一次运行时建立空的数据文件，由键盘输入建立对象并写入文件
- 下一次运行由该文件构造对象，恢复前一次做过的工作

**inventory 类：**

```cpp
class inventory {
    string Description;   // 商品名称
    string No;            // 货号
    int Quantity;         // 数量
    double Cost;          // 价格
    double Retail;        // 零售价

public:
    inventory(string = "#", string = "#", int = 0, double = 0, double = 0);
    friend ostream& operator<<(ostream& dist, inventory& iv);
    friend istream& operator>>(istream& sour, inventory& iv);
    bool operator==(inventory& iv) { return No == iv.No; }
    bool operator<=(inventory& iv) { return No <= iv.No; }
};
```

**>> 运算符重载（区分键盘和文件输入）：**

```cpp
istream& operator>>(istream& sour, inventory& iv) {
    if (sour == cin) {
        // 从键盘输入时，给提示
        cout << "请输入货物名称：" << endl; sour >> iv.Description;
        cout << "请输入货号：" << endl;     sour >> iv.No;
        cout << "请输入货物数量：" << endl; sour >> iv.Quantity;
        cout << "请输入货物价格：" << endl; sour >> iv.Cost;
        cout << "请输入货物零售价格：" << endl; sour >> iv.Retail;
    } else {
        // 从文件输入时，直接读取
        sour >> iv.Description >> iv.No
             >> iv.Quantity >> iv.Cost >> iv.Retail;
    }
    return sour;
}
```

> 💡 通过 `sour == cin` 判断输入源是键盘还是文件，实现不同的输入行为。

**Array 模板类：**

```cpp
template <typename T>
class Array {
    T* elements;        // 动态数组
    int Subscript;      // 已用最大下标值
    int maxSize;        // 数组容量
    fstream datafile;   // 文件流对象

public:
    Array(int = 2);     // 默认元素数为2（便于检验）
    ~Array();
    bool IsFull() const;    // 判断是否已满
    void renews();          // 内存扩大一倍
    void show();            // 提示剩余可用元素数
    void ordinsert(T&);     // 以货号为关键字排序插入
    friend ostream& operator<<(ostream& dist, Array& ar);
};
```

**构造函数：从文件读取数据创建对象**

```cpp
template <typename T>
Array<T>::Array(int maxs) {
    maxSize = maxs;
    Subscript = -1;
    T temp;
    elements = new T[maxSize];

    datafile.open("mydatafile.txt", ios::in);
    if (!datafile == 0) {     // 文件存在
        while (!datafile.eof()) {
            datafile >> temp;
            if (datafile.eof() == 0)
                ordinsert(temp);
        }
        datafile.close();
    }
    datafile.clear(0);        // 标准库做法：清除流状态
}
```

> ⚠️ `clear(0)` 是标准库做法——若前面曾经读到文件结束或文件打开失败，流状态会被置位，必须清除后才能复用。

**析构函数：将数组元素写入文件**

```cpp
template <typename T>
Array<T>::~Array() {
    int i;
    datafile.open("mydatafile.txt", ios::out);
    for (i = 0; i <= Subscript; i++)
        datafile << elements[i];
    datafile.close();
    delete[] elements;
}
```

**重载 << 输出数组元素：**

```cpp
template <typename T>
ostream& operator<<(ostream& dist, Array<T>& ar) {
    int i;
    for (i = 0; i <= ar.Subscript; i++)
        cout << ar.elements[i];
    return dist;
}
```

**主函数：**

```cpp
int main() {
    Array<inventory> mylist;
    inventory temp;
    char ch;

    cout << "是否输入新商品？Y or N" << endl;
    cin >> ch;

    while (ch == 'Y' || ch == 'y') {
        cin.get();      // 吸收回车
        cin >> temp;
        mylist.ordinsert(temp);
        cout << "是否继续输入？Y or N" << endl;
        cin >> ch;
    }

    cout << mylist;
    return 0;           // 析构函数自动保存数据到文件
}
```

> 💡 程序退出时 `return 0` 触发析构函数，自动将数据写入文件。下次运行时构造函数自动从文件恢复数据。这就是"文件与对象"的核心设计模式。

---


## 10. 本章小结

- 流是数据通道。
- 格式控制让输出更整齐。
- 文本文件可读，二进制文件紧凑。
- 判断读取成功用 `while (in >> x)`。
- 文件指针可用于随机访问。
- 保存对象时优先设计清楚的数据格式。




#### 附录C：课件补充 - 流类库输入输出控制

> 东南大学 C++ 课件笔记 | 王文博 2026

---


1. [文本文件的读写（续）](#1-文本文件的读写续)
2. [二进制文件的读写](#2-二进制文件的读写)
3. [文件与对象](#3-文件与对象)

---

#### 1. 文本文件的读写（续）

#### 1.1 例3：复制文件

**分析：**
1. 打开源文件（输入文件流对象）
2. 打开目的文件（输出文件流对象）
3. 从源文件中依次读取一个字符
4. 把所读的字符写入目的文件中，直到把源文件中的所有字符读写完为止

**方法一：get/put 逐字符复制**

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    char filename1[256], filename2[256];
    cout << "输入源文件名：";
    cin >> filename1;
    cout << "输入目的文件名：";
    cin >> filename2;

    ifstream infile(filename1, ios::in);
    ofstream outfile(filename2);

    if (!infile)  { cerr << "open error!" << endl; exit(1); }
    if (!outfile) { cerr << "open error!" << endl; exit(1); }

    char ch;
    while (infile.get(ch))   // 依次从源文件取一个字符，直到文件结束
        outfile.put(ch);      // 将取的字符写到目的文件中

    infile.close();
    outfile.close();
    return 0;
}
```

**方法二：>> / << 运算符复制**

```cpp
// 注意：>> 默认跳过空格，需要取消该设置
infile.unsetf(ios::skipws);  // 设置为不要跳过文件中的空格
while (infile >> ch)
    outfile << ch;
```

> ⚠️ `>>` 运算符默认会跳过空白字符（空格、制表符、换行），用 `unsetf(ios::skipws)` 取消该行为。

#### 1.2 例4：文本文件按行复制 — getline

使用 `getline` 成员函数按行读取，配合字符串处理。

**文件打开失败的容错处理：**

```cpp
while (!infile) {
    cout << "源文件找不到,请重新输入路径文件名:" << endl;
    infile.clear(0);     // 清状态字
    cin >> filename1;
    infile.open(filename1, ios::in);
}

while (!outfile) {
    cout << "源文件找不到,请重新输入路径文件名:" << endl;
    outfile.clear(0);    // 清状态字
    cin >> filename2;
    outfile.open(filename2, ios::out);
}
```

> ⚠️ 文件打开失败后，流的状态字被置位，必须用 `clear(0)` 清零后才能重新使用该流对象。

**按行复制的核心逻辑：**

```cpp
char buff[256];
while (1) {
    infile.getline(buff, 256);

    if (!infile.eof()) {
        if (infile.rdstate() == 0)
            outfile << buff << '\n';   // 流正常，读到回车符但不提取，手动加换行
        else {
            outfile << buff;           // 未读到回车符，流不正常
            infile.clear(0);           // 清零状态字
        }
    } else {
        if (infile.gcount() != 0)
            outfile << buff;           // 最后一行可能没有换行符
        break;
    }
}
```

**关键点：**
- `getline` 遇到换行符停止读取，但**不提取**换行符到缓冲区
- `rdstate() == 0` 表示流正常（读到了换行符）
- 流不正常时（如一行超过指定长度），需要 `clear(0)` 清除状态
- `gcount()` 返回上次读取的实际字符数

#### 1.3 读取数值文件时的问题

```cpp
ifstream input("score.txt");
double sum = 0;
double number;
while (!input.eof()) {
    input >> number;
    cout << number << " ";
    sum += number;
}
cout << endl;
cout << sum;
input.close();
```

> ⚠️ **这段代码有问题！** 当读到文件末尾时，`eof()` 检测的是上一次操作是否遇到了 EOF，而最后一次 `>> number` 会失败，导致 `number` 的值不被更新（可能是上一次的值或垃圾值），`sum` 会多加一个错误数据。

**正确写法：**

```cpp
while (input >> number) {   // 直接判断读取是否成功
    cout << number << " ";
    sum += number;
}
```

#### 1.4 例4补充：显示文件内容函数

```cpp
void display_file(char *filename) {
    ifstream infile(filename, ios::in);
    if (!infile) { cerr << "open error!" << endl; exit(1); }

    char ch;
    while (infile.get(ch))
        cout.put(ch);

    cout << endl;
    infile.close();
}

// 调用
int main() {
    display_file("f3.dat");
    return 0;
}
```

#### 1.5 思考：删除 C++ 源文件中的注释

**分析：**
1. 把源文件看做字符流，依次读入每个字符
2. 若字符处于注释中则舍弃，否则写入新文件
3. 注释有两种：`//` 和 `/* */`

**处理逻辑：**
- 当前字符 `/`，下一个字符是 `/` → 读出本行除 `\n` 外所有字符舍弃
- 当前字符 `/`，下一个字符是 `*` → 空读，直至遇到 `*/`

```cpp
char ch1, ch2, temp[1000];
int state = 1;   // state=1 正常代码区, state=2 块注释区

while (infile.get(ch1)) {
    ch2 = infile.peek();   // 预看下一个字符但不提取

    if (state == 1) {
        if (ch1 == '/' && ch2 == '*') {
            infile.get();   // 跳过 '*'
            state = 2;      // 进入块注释区
        } else if (ch1 == '/' && ch2 == '/') {
            infile.get(temp, sizeof(temp));  // 丢弃本行剩余内容
        } else {
            outfile.put(ch1);
            cout << ch1;
        }
    } else if (state == 2) {
        if (ch1 == '*' && ch2 == '/') {
            infile.get();   // 跳过 '/'
            state = 1;      // 回到正常代码区
        }
    }
}
```

> 💡 `peek()` 函数可以预读下一个字符但不移动文件指针，用于判断注释类型非常方便。

#### 1.6 例5：文本式对象数据文件的创建和读取

**inventory 类定义：**

```cpp
class inventory {
    char Description[20];   // 商品名称
    char No[10];            // 货号
    int Quantity;            // 数量
    double Cost;             // 价格
    double Retail;           // 零售价

public:
    inventory(char* = "#", char* = "0", int = 0, double = 0, double = 0);
    friend ostream& operator<<(ostream&, inventory&);
    friend istream& operator>>(istream&, inventory&);
};
```

**重载 << 和 >> ：**

```cpp
// 重载 << — 格式化输出
ostream& operator<<(ostream& dest, inventory& iv) {
    dest << left << setw(20) << iv.Description << setw(10) << iv.No;
    dest << right << setw(10) << iv.Quantity;
    dest << setw(10) << iv.Cost << setw(10) << iv.Retail << endl;
    return dest;
}

// 重载 >> — 从流中读取数据到对象
istream& operator>>(istream& sour, inventory& iv) {
    sour >> iv.Description >> iv.No >> iv.Quantity
         >> iv.Cost >> iv.Retail;
    return sour;
}
```

**主函数：写入文件再读取**

```cpp
int main() {
    inventory car1("夏利2000", "805637928", 156, 80000, 105000), car2;
    inventory motor1("金城125", "93612575", 302, 10000, 13000), motor2;

    // 写入文件
    ofstream distfile("d:\\Ex9_9.data");
    distfile << car1 << motor1;     // << 完成对象写入文件
    distfile.close();

    cout << car1;
    cout << motor1;

    // 从文件读取
    // 分两次打开，可避免读文件时误改了源文件
    ifstream sourfile("d:\\Ex9_9.data");
    sourfile >> car2 >> motor2;     // >> 完成对象重构
    sourfile.close();

    cout << car2;
    cout << motor2;
    return 0;
}
```

> 💡 `ofstream` 是 `ostream` 的派生类，`ifstream` 是 `istream` 的派生类。所以重载的 `<<` 和 `>>` 对文件流同样适用。

---

#### 2. 二进制文件的读写

#### 2.1 什么是二进制文件

文件中的信息不是字符数据，而是字节中的**二进制形式**的信息，因此又称为**字节文件**。

- 用 `ios::binary` 指定为以二进制形式传送和存储
- 二进制文件除了可以作为输入文件或输出文件外，还可以是既能输入又能输出的文件

#### 2.2 read 和 write 成员函数

**函数原型：**

```cpp
// write: 将内存数据写入文件
ostream& write(const char* buffer, int len);

// read: 从文件读取数据到内存
istream& read(char* buffer, int len);
```

> ⚠️ 地址参数要强制转换为 `char*` 类型。

**示例：将结构体数组以二进制形式存入文件**

```cpp
struct student {
    char name[20];
    int num;
    int age;
    char sex;
};

int main() {
    student stud[3] = {
        {"Li", 1001, 18, 'f'},
        {"Fun", 1002, 19, 'm'},
        {"Wang", 1004, 17, 'f'}
    };

    ofstream outfile("stud.dat", ios::binary);
    if (!outfile) { cerr << "open error" << endl; exit(1); }

    // 逐个写入
    for (int i = 0; i < 3; i++)
        outfile.write((char*)&stud[i], sizeof(stud[i]));

    // 或一次写入全部
    // outfile.write((char*)&stud[0], sizeof(stud));

    outfile.close();
    return 0;
}
```

**从二进制文件读取：**

```cpp
int main() {
    student stud[3];
    int i;

    ifstream infile("stud.dat", ios::binary);
    if (!infile) { cerr << "open error" << endl; exit(1); }

    // 逐个读取
    for (i = 0; i < 3; i++)
        infile.read((char*)&stud[i], sizeof(stud[i]));

    // 或一次读取全部
    // infile.read((char*)&stud[0], sizeof(stud));

    infile.close();

    for (i = 0; i < 3; i++) {
        cout << "NO." << i + 1 << endl;
        cout << "name:" << stud[i].name << endl;
        cout << "num:"  << stud[i].num  << endl;
        cout << "age:"  << stud[i].age  << endl;
        cout << "sex:"  << stud[i].sex  << endl << endl;
    }
    return 0;
}
```

#### 2.3 对象的二进制文件读写

**inventory 类增加二进制读写方法：**

```cpp
class inventory {
    string Description;
    string No;
    int Quantity;
    double Cost;
    double Retail;

public:
    // ...
    void Bdatatofile(ofstream& dest);   // 写入二进制文件
    void Bdatafromfile(ifstream& sour);  // 从二进制文件读取
};
```

> ⚠️ 流类作为形式参数必须是引用。

**写入方法：**

```cpp
void inventory::Bdatatofile(ofstream& dest) {
    dest.write(Description.c_str(), 20);   // string 转为 const char*
    dest.write(No.c_str(), 10);
    dest.write((char*)&Quantity, sizeof(int));
    dest.write((char*)&Cost, sizeof(double));
    dest.write((char*)&Retail, sizeof(double));
}
```

**读取方法：**

```cpp
void inventory::Bdatafromfile(ifstream& sour) {
    char k[20];
    sour.read(k, 20);
    Description = k;
    sour.read(k, 10);
    No = k;
    sour.read((char*)&Quantity, sizeof(int));
    sour.read((char*)&Cost, sizeof(double));
    sour.read((char*)&Retail, sizeof(double));
}
```

> 💡 **读和写是完全对称的过程，次序决不能错。** `string` 类型的成员需要用 `c_str()` 转为 `const char*` 写入，读取时先读到 `char[]` 再赋给 `string`。

**使用：**

```cpp
int main() {
    inventory car1("夏利2000", "805637928", 156, 80000, 105000), car2;
    inventory motor1("金城125", "93612575", 302, 10000, 13000), motor2;

    // 写入二进制文件
    ofstream ddatafile("ex9_10.data", ios::out | ios::binary);
    car1.Bdatatofile(ddatafile);
    motor1.Bdatatofile(ddatafile);
    ddatafile.close();

    // 从二进制文件读取
    ifstream sdatafile("ex9_10.data", ios::in | ios::binary);
    car2.Bdatafromfile(sdatafile);
    if (sdatafile.eof() == 0) cout << "读文件成功" << endl;
    cout << "对象car2:" << endl;
    cout << car2;

    motor2.Bdatafromfile(sdatafile);
    if (sdatafile.eof() == 0) cout << "读文件成功" << endl;
    cout << "对象motor2:" << endl;
    cout << motor2;
    sdatafile.close();

    return 0;
}
```

#### 2.4 二进制文件拷贝

使用 `read` 和 `write` 实现二进制文件拷贝，配合缓冲区提高效率：

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    char filename1[256], filename2[256];
    char buff[4096];    // 4KB 缓冲区

    cout << "输入源文件名:"; cin >> filename1;
    cout << "输入目的文件名:"; cin >> filename2;

    fstream infile(filename1, ios::in | ios::binary);
    fstream outfile(filename2, ios::out | ios::binary);

    if (!infile)  { cerr << "open error!" << endl; exit(1); }
    if (!outfile) { cerr << "open error!" << endl; exit(1); }

    int n;
    while (!infile.eof()) {
        infile.read(buff, 4096);
        n = infile.gcount();         // 实际读入的字节数
        outfile.write(buff, n);       // 把实际读入的字节数写到文件中
    }

    infile.close();
    outfile.close();
    // infile.clear();  // 若后续还需使用 infile，需 clear
    return 0;
}
```

> 💡 `gcount()` 返回上次 `read` 实际读入的字节数，确保最后一次写入不会多写。

#### 2.5 二进制文件特点

- 可以控制字节长度，读写数据时不会出现二义性，**可靠性高**
- 不知格式是无法读取的，**保密性好**
- 文件结束后系统不会再读，但程序不会自动停下来，所以**要判断文件中是否已没有数据**
- 写完数据后若没有关闭文件就立即开始读，须把文件定位指针移到文件头
- 关闭文件后重新打开，文件定位指针自动在文件头

#### 2.6 与文件指针有关的流成员函数

磁盘文件中有一个**文件指针**，指明当前应进行读写的位置。

**文件指针控制函数：**

| 函数 | 说明 |
|------|------|
| `seekg(streampos)` | 绝对定位输入指针 |
| `seekg(streamoff, ios::seek_dir)` | 相对定位输入指针 |
| `tellg()` | 返回输入指针当前位置 |
| `seekp(streampos)` | 绝对定位输出指针 |
| `seekp(streamoff, ios::seek_dir)` | 相对定位输出指针 |
| `tellp()` | 返回输出指针当前位置 |

> `g` = get（读），`p` = put（写）

**指针移动的参照位置：**

| 参照位置 | 含义 |
|----------|------|
| `ios::beg` | 文件开头（默认值） |
| `ios::cur` | 指针当前位置 |
| `ios::end` | 文件末尾 |

**用法示例：**

```cpp
infile.seekg(100);                  // 输入指针移到第100字节位置
infile.seekg(40, ios::beg);         // 从文件头向文件尾移动40字节
infile.seekg(50, ios::cur);         // 从当前位置往文件头移动50字节
outfile.seekp(75, ios::end);        // 从文件尾往文件头移动75字节
```

#### 2.7 例：随机访问学生数据

**需求：**
1. 把5个学生数据存到磁盘文件
2. 将第1、3、5个学生数据读入并显示
3. 将第3个学生的数据修改后存回原有位置
4. 读入修改后的5个学生数据并显示

**关键设计：**
- 文件工作方式指定为 `ios::in | ios::out | ios::binary`
- 用 `seekg`/`seekp` 正确定位指针
- 修改后用 `write` 重写数据

```cpp
#include <fstream>
#include <iostream>
using namespace std;

struct student {
    int num;
    char name[20];
    float score;
};

int main() {
    student stud[5] = {
        {1001, "Li", 85},
        {1002, "Fun", 97.5},
        {1004, "Wang", 54},
        {1006, "Tan", 76.5},
        {1010, "ling", 96}
    };

    // (1) 写入文件
    fstream iofile("stud.dat", ios::in | ios::out | ios::binary);
    if (!iofile) { cerr << "open error!" << endl; exit(1); }
    iofile.write((char*)&stud[0], sizeof(stud));

    // (2) 读取第1、3、5个学生
    student stud1[5];
    for (int i = 0; i < 5; i = i + 2) {
        iofile.seekg(i * sizeof(stud[i]), ios::beg);  // 定位于第0,2,4学生数据开头
        iofile.read((char*)&stud1[i/2], sizeof(stud1[0]));
        cout << stud1[i/2].num << " " << stud1[i/2].name
             << " " << stud1[i/2].score << endl;
    }
    cout << endl;

    // (3) 修改第3个学生的数据
    stud[2].num = 1012;
    strcpy(stud[2].name, "Wu");
    stud[2].score = 60;
    iofile.seekp(2 * sizeof(stud[0]), ios::beg);  // 定位于第3个学生数据开头
    iofile.write((char*)&stud[2], sizeof(stud[2]));

    // (4) 重新从头读取所有学生
    iofile.seekg(0, ios::beg);
    for (int i = 0; i < 5; i++) {
        iofile.read((char*)&stud[i], sizeof(stud[i]));
        cout << stud[i].num << " " << stud[i].name
             << " " << stud[i].score << endl;
    }

    iofile.close();
    return 0;
}
```

#### 2.8 课堂练习题

**题目1：** 将内存中的数组 `int val[6]={2,4,8,9};` 写入二进制文件 `file.dat`，以下能够正确实现写入的语句是：

| 选项 | 代码 |
|------|------|
| A | `ofstream ofile("file.dat", ios::binary); ofile.read((char*)val, sizeof(int));` |
| B | `ifstream ifile("file.dat", ios::binary); ifile.write((char*)val, sizeof(int)*6);` |
| C | `ofstream ofile("file.dat", ios::binary); ofile.write((char*)val, 6*sizeof(int));` |
| D | `ofstream ofile("file.dat", ios::binary); ofile.write((char*)&val, 6*sizeof(int));` |

> ✅ **答案：C**。A用了read不是write；B用了ifstream不能写；D中 `&val` 是数组指针的地址，不是数组首地址，`val` 本身就是首地址。

**题目2：** 下面选项中含有只对文本文件进行读操作的是____。

| 选项 | 函数 |
|------|------|
| A | `get()`、`getline()` |
| B | `getline()`、`write()` |
| C | `get()`、`put()` |
| D | `read()`、`write()` |

> ✅ **答案：A**。`get()` 和 `getline()` 是文本读取函数；`read()`/`write()` 是二进制读写函数；`put()` 是字符输出。

**题目3：** `ofstream outf("Myf.txt");` 打开文件是通过文件流类的____实现。

| 选项 | 答案 |
|------|------|
| A | 不需要函数 |
| B | 普通成员函数 |
| C | 构造函数 |
| D | open成员函数 |

> ✅ **答案：C**。在对象创建时通过构造函数打开文件，也可以用 `open()` 成员函数单独打开。

**题目4：** 下面程序的作用是____。

```cpp
#include <iostream>
using namespace std;
int main() {
    char x;
    while ((x = cin.get()) != '\n')
        cout.put(x);
    return 0;
}
```

| 选项 | 描述 |
|------|------|
| A | 将键盘输入的一个字符显示在显示器上 |
| B | 将键盘输入的一行字符显示在显示器上 |
| C | 将键盘输入的一个数据显示在显示器上 |
| D | 将字符 x 显示在显示器上 |

> ✅ **答案：B**。循环读取字符直到遇到换行符，即读入一行字符并回显。

---

#### 9. 文件与对象

#### 9.1 面向对象的文件操作框架

在面向对象程序设计中，信息总是放在对象的数据成员里，这些信息最终应该保存到文件中。

**规范的面向对象编程方法：**

1. **程序开始运行时**：由打开的文件重新创建对象（构造函数中读文件）
2. **运行过程中**：对象的数据成员得到利用和修改
3. **运行结束时**：把信息重新保存到文件中，然后关闭文件（析构函数中写文件）

> 💡 这是面向对象 C++ 程序设计的**固定框架**：构造函数中打开文件并创建对象，析构函数中保存数据并关闭文件。

#### 9.2 综合案例：货物数组类

**需求：**
- 将商店的货物定义为一个货物数组类
- 数组对象动态建立，初始为2个元素，不够用时扩充一倍
- 用文本数据文件建立数组元素对象（构造函数），数据保存和文件关闭放在析构函数
- 第一次运行时建立空的数据文件，由键盘输入建立对象并写入文件
- 下一次运行由该文件构造对象，恢复前一次做过的工作

**inventory 类：**

```cpp
class inventory {
    string Description;   // 商品名称
    string No;            // 货号
    int Quantity;         // 数量
    double Cost;          // 价格
    double Retail;        // 零售价

public:
    inventory(string = "#", string = "#", int = 0, double = 0, double = 0);
    friend ostream& operator<<(ostream& dist, inventory& iv);
    friend istream& operator>>(istream& sour, inventory& iv);
    bool operator==(inventory& iv) { return No == iv.No; }
    bool operator<=(inventory& iv) { return No <= iv.No; }
};
```

**>> 运算符重载（区分键盘和文件输入）：**

```cpp
istream& operator>>(istream& sour, inventory& iv) {
    if (sour == cin) {
        // 从键盘输入时，给提示
        cout << "请输入货物名称：" << endl; sour >> iv.Description;
        cout << "请输入货号：" << endl;     sour >> iv.No;
        cout << "请输入货物数量：" << endl; sour >> iv.Quantity;
        cout << "请输入货物价格：" << endl; sour >> iv.Cost;
        cout << "请输入货物零售价格：" << endl; sour >> iv.Retail;
    } else {
        // 从文件输入时，直接读取
        sour >> iv.Description >> iv.No
             >> iv.Quantity >> iv.Cost >> iv.Retail;
    }
    return sour;
}
```

> 💡 通过 `sour == cin` 判断输入源是键盘还是文件，实现不同的输入行为。

**Array 模板类：**

```cpp
template <typename T>
class Array {
    T* elements;        // 动态数组
    int Subscript;      // 已用最大下标值
    int maxSize;        // 数组容量
    fstream datafile;   // 文件流对象

public:
    Array(int = 2);     // 默认元素数为2（便于检验）
    ~Array();
    bool IsFull() const;    // 判断是否已满
    void renews();          // 内存扩大一倍
    void show();            // 提示剩余可用元素数
    void ordinsert(T&);     // 以货号为关键字排序插入
    friend ostream& operator<<(ostream& dist, Array& ar);
};
```

**构造函数：从文件读取数据创建对象**

```cpp
template <typename T>
Array<T>::Array(int maxs) {
    maxSize = maxs;
    Subscript = -1;
    T temp;
    elements = new T[maxSize];

    datafile.open("mydatafile.txt", ios::in);
    if (!datafile == 0) {     // 文件存在
        while (!datafile.eof()) {
            datafile >> temp;
            if (datafile.eof() == 0)
                ordinsert(temp);
        }
        datafile.close();
    }
    datafile.clear(0);        // 标准库做法：清除流状态
}
```

> ⚠️ `clear(0)` 是标准库做法——若前面曾经读到文件结束或文件打开失败，流状态会被置位，必须清除后才能复用。

**析构函数：将数组元素写入文件**

```cpp
template <typename T>
Array<T>::~Array() {
    int i;
    datafile.open("mydatafile.txt", ios::out);
    for (i = 0; i <= Subscript; i++)
        datafile << elements[i];
    datafile.close();
    delete[] elements;
}
```

**重载 << 输出数组元素：**

```cpp
template <typename T>
ostream& operator<<(ostream& dist, Array<T>& ar) {
    int i;
    for (i = 0; i <= ar.Subscript; i++)
        cout << ar.elements[i];
    return dist;
}
```

**主函数：**

```cpp
int main() {
    Array<inventory> mylist;
    inventory temp;
    char ch;

    cout << "是否输入新商品？Y or N" << endl;
    cin >> ch;

    while (ch == 'Y' || ch == 'y') {
        cin.get();      // 吸收回车
        cin >> temp;
        mylist.ordinsert(temp);
        cout << "是否继续输入？Y or N" << endl;
        cin >> ch;
    }

    cout << mylist;
    return 0;           // 析构函数自动保存数据到文件
}
```

> 💡 程序退出时 `return 0` 触发析构函数，自动将数据写入文件。下次运行时构造函数自动从文件恢复数据。这就是"文件与对象"的核心设计模式。

---

#### 重点回顾

1. **文本文件读写**：`get/put` 逐字符、`getline` 按行、`>>/<<` 运算符；注意 `skipws` 和 `eof()` 的陷阱
2. **二进制文件读写**：`read/write` 函数，地址参数必须转 `(char*)`；读写次序必须对称
3. **文件指针**：`seekg/seekp` 定位、`tellg/tellp` 获取位置；支持 `beg/cur/end` 三种参照点
4. **文件与对象**：构造函数读文件建对象，析构函数写文件保存数据——面向对象的标准框架
5. **流状态管理**：`clear(0)` 清除状态字，`rdstate()` 检查状态，`gcount()` 获取实际读取字节数

---

*笔记基于王文博老师课件整理，2026年6月*

[返回主目录](../README.md)
