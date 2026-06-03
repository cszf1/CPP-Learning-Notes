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

流是数据传输的通道。输入流把数据读进程序，输出流把数据写出去。

| 流 | 作用 |
| --- | --- |
| `cin` | 标准输入 |
| `cout` | 标准输出 |
| `cerr` | 错误输出，不缓冲 |
| `clog` | 日志输出，缓冲 |

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

```cpp
#include <fstream>

ifstream in("input.txt");
ofstream out("output.txt");
fstream io("data.txt", ios::in | ios::out);
```

打开模式：

| 模式 | 含义 |
| --- | --- |
| `ios::in` | 读 |
| `ios::out` | 写 |
| `ios::app` | 追加 |
| `ios::binary` | 二进制 |
| `ios::trunc` | 清空原内容 |

## 5. 文本文件读写

按单词读：

```cpp
string word;
while (in >> word) {
    cout << word << endl;
}
```

按行读：

```cpp
string line;
while (getline(in, line)) {
    cout << line << endl;
}
```

## 6. 二进制文件读写

```cpp
int x = 123;
ofstream out("num.bin", ios::binary);
out.write(reinterpret_cast<char*>(&x), sizeof(x));

int y;
ifstream in("num.bin", ios::binary);
in.read(reinterpret_cast<char*>(&y), sizeof(y));
```

二进制读写顺序必须一致。写时是“姓名、学号、成绩”，读时也必须这个顺序，不然数据会像拼错的积木。

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

```cpp
in.seekg(0, ios::beg);
auto pos = in.tellg();
out.seekp(0, ios::end);
```

- `seekg/tellg`：控制输入位置。
- `seekp/tellp`：控制输出位置。
- `ios::beg/cur/end`：文件开头、当前位置、文件末尾。

## 9. 文件与对象

简单对象可以按字段保存：

```cpp
struct Student {
    string name;
    int score;
};

out << s.name << ' ' << s.score << endl;
```

不要随便把含有 `string`、指针、虚函数的对象整体二进制写入文件。那保存的是对象内部实现，不是稳定数据格式。

## 10. 本章小结

- 流是数据通道。
- 格式控制让输出更整齐。
- 文本文件可读，二进制文件紧凑。
- 判断读取成功用 `while (in >> x)`。
- 文件指针可用于随机访问。
- 保存对象时优先设计清楚的数据格式。

[返回主目录](../README.md)