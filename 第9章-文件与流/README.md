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
9. [本章小结](#9-本章小结)

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

### 2.3 打开模式

打开文件时可以指定模式（mode）：

| 模式 | 含义 | 说明 |
|------|------|------|
| `ios::in` | 读取 | 打开用于读取（默认 for `ifstream`） |
| `ios::out` | 写入 | 打开用于写入（默认 for `ofstream`） |
| `ios::app` | 追加 | 追加模式，新内容添加到文件末尾 |
| `ios::trunc` | 截断 | 如果文件存在，清空文件内容（默认） |
| `ios::binary` | 二进制 | 以二进制模式打开（默认是文本模式） |

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

### 3.2 读取文本文件

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

### 3.3 使用 getline() 读取整行

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

### 3.4 逐字符读取

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

**说明**：
- `write()` 需要 char* 类型的指针
- `reinterpret_cast<char*>(&stu1)` 把结构体地址转成字符指针
- `sizeof(Student)` 指定写入多少字节

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
| 状态位 | 含义 |
|--------|------|
| `eofbit` | 到达文件末尾 |
| `failbit` | 上次操作失败（如类型不匹配） |
| `badbit` | 流被破坏 |
| `goodbit` | 所有状态正常 |

### 5.3 检查文件是否存在

```cpp
#include <fstream>
#include <iostream>
using namespace std;

bool fileExists(const string& filename) {
    ifstream file(filename);
    return file.good();  // 如果文件可以打开且状态良好
}

int main() {
    if (fileExists("test.txt")) {
        cout << "文件存在" << endl;
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

### 6.3 移动文件指针

**seekg()：移动读取指针**
```cpp
// 移动到指定位置
inFile.seekg(10);           // 移动到第10个字节
inFile.seekg(0);            // 移动到开头
inFile.seekg(0, ios::end);  // 移动到末尾
inFile.seekg(10, ios::cur); // 从当前位置向后移动10字节
```

**seekp()：移动写入指针**
```cpp
// 移动到指定位置
outFile.seekp(10);           // 移动到第10个字节
outFile.seekp(0, ios::end); // 移动到末尾（追加模式）
```

### 6.4 实战：文件内容的随机读取

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

---

## 7. 综合案例：学生信息管理系统

### 7.1 需求分析

实现一个简单的学生信息管理系统：
- 添加学生信息
- 查看所有学生
- 保存到文件
- 从文件加载

### 7.2 完整代码

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
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
    void searchById(int id) {
        for (const auto& stu : students) {
            if (stu.id == id) {
                cout << "找到学生：" << endl;
                cout << "学号: " << stu.id << endl;
                cout << "姓名: " << stu.name << endl;
                cout << "年龄: " << stu.age << endl;
                cout << "成绩: " << stu.score << endl;
                return;
            }
        }
        cout << "未找到该学号的学生" << endl;
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
            outFile.write(reinterpret_cast<const char*>(&stu.id), sizeof(int));
            
            // 写入姓名（固定长度）
            char nameBuf[50] = {0};
            stu.name.copy(nameBuf, stu.name.length());
            outFile.write(nameBuf, sizeof(nameBuf));
            
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
            
            inFile.read(reinterpret_cast<char*>(&stu.id), sizeof(int));
            
            char nameBuf[50];
            inFile.read(nameBuf, sizeof(nameBuf));
            stu.name = nameBuf;
            
            inFile.read(reinterpret_cast<char*>(&stu.age), sizeof(int));
            inFile.read(reinterpret_cast<char*>(&stu.score), sizeof(double));
            
            students.push_back(stu);
        }
        
        inFile.close();
        cout << "加载了 " << students.size() << " 条学生记录" << endl;
    }
};

// 主菜单
void showMenu() {
    cout << "\n===== 学生信息管理系统 =====" << endl;
    cout << "1. 添加学生" << endl;
    cout << "2. 显示所有学生" << endl;
    cout << "3. 按学号查找" << endl;
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

请选择: 0
退出系统，数据会自动保存
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

### 8.5 路径问题

```cpp
// Windows 路径分隔符
ofstream f1("C:\\folder\\file.txt");  // 转义
ofstream f2("C:/folder/file.txt");   // 正斜杠也可以

// Linux/Mac 路径
ofstream f3("/home/user/file.txt");  // 使用正斜杠
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

---

## 9. 本章小结

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

4. **二进制文件操作**
   - `write()` 写入，`read()` 读取
   - 需要 `ios::binary` 模式

5. **文件状态检查**
   - `is_open()`：检查是否打开
   - `eof()`：检查是否到末尾
   - `fail()`：检查操作是否失败

6. **文件定位**
   - `tellg()`/`tellp()`：获取位置
   - `seekg()`/`seekp()`：移动位置

### 学习建议

1. **先从文本文件开始**：操作简单，容易调试
2. **注意检查文件状态**：打开失败是常见问题
3. **二进制文件适合结构化数据**：如学生信息、游戏存档
4. **记得关闭文件**：确保数据写入磁盘
5. **使用RAII**：构造函数打开，析构函数关闭

### 练习题

**基础题**
1. 编写程序，将1-100写入文件 `numbers.txt`
2. 编写程序，读取 `numbers.txt` 并计算总和
3. 编写程序，复制一个文本文件

**进阶题**
4. 编写学生信息管理系统，支持添加、查看、保存、加载
5. 用二进制方式存储学生信息，比较和文本方式的文件大小
6. 实现文件加密功能：对文件每个字节进行XOR运算

---

*返回 [主目录](../README.md)*
