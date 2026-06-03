# 第9章 文件与流

> 本章目标：学会把数据写进文件、从文件读回来。内存像草稿纸，文件像笔记本；程序结束后，草稿纸会被收走，笔记本还能留下。

## 目录

1. 文件与流
2. fstream 文件流
3. 文本文件读写
4. 二进制文件读写
5. 文件状态与 eof 陷阱
6. 文件定位
7. 文件与对象
8. 本章小结

## 1. 文件与流

C++ 用“流”处理输入输出。`cin`、`cout` 是控制台流，`ifstream`、`ofstream`、`fstream` 是文件流。

```cpp
#include <fstream>
```

## 2. fstream 文件流

| 类型 | 用途 |
| --- | --- |
| `ifstream` | 读文件 |
| `ofstream` | 写文件 |
| `fstream` | 读写文件 |

## 3. 文本文件读写

按单词读取：

```cpp
string word;
while (in >> word) {
    cout << word << endl;
}
```

按行读取：

```cpp
string line;
while (getline(in, line)) {
    cout << line << endl;
}
```

如果前面用了 `cin >> x`，再 `getline(cin, line)`，可能会读到残留换行。可以先 `cin.ignore()`。

## 4. 二进制文件读写

```cpp
int x = 123;
ofstream out("num.bin", ios::binary);
out.write(reinterpret_cast<char*>(&x), sizeof(x));
out.close();

int y = 0;
ifstream in("num.bin", ios::binary);
in.read(reinterpret_cast<char*>(&y), sizeof(y));
in.close();
```

二进制读写要保证写入顺序和读取顺序一致。写的时候是“鸡蛋、牛奶、面包”，读的时候别按“面包、鸡蛋、牛奶”来。

## 5. 文件状态与 eof 陷阱

不要这样写：

```cpp
while (!in.eof()) {
    in >> x;
}
```

推荐这样写：

```cpp
while (in >> x) {
    // 只有读取成功才处理
}
```

`eof()` 是读取失败后才知道到了末尾，不是预言家。

## 6. 文件定位

读指针用 `seekg/tellg`，写指针用 `seekp/tellp`。

```cpp
in.seekg(0, ios::beg);
auto pos = in.tellg();
```

## 7. 文件与对象

保存对象时，文本方式适合可读数据，二进制方式适合固定格式数据。

```cpp
struct Student {
    string name;
    int score;
};

ofstream out("students.txt");
Student s{"Alice", 90};
out << s.name << ' ' << s.score << endl;
```

不要直接把含有 `string`、指针、虚函数的复杂对象整体二进制写入文件。那保存的是内部结构，不是稳定格式。

## 8. 本章小结

- `ifstream` 读，`ofstream` 写，`fstream` 读写。
- 文本文件可读性强，二进制文件更紧凑。
- 判断读取成功用 `while (in >> x)`，少用 `while (!eof())`。
- `seekg/seekp` 可做随机访问。
- 保存对象时，优先设计清楚的数据格式。

[返回主目录](../README.md)