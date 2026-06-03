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

顺序结构就是语句从上到下执行。

```cpp
int a, b;
cin >> a >> b;
cout << a + b << endl;
```

它最简单，也最诚实：写在前面的先执行。

## 2. if 选择结构

```cpp
if (score >= 60) {
    cout << "及格";
} else {
    cout << "再来一次";
}
```

多条件：

```cpp
if (score >= 90) cout << "A";
else if (score >= 80) cout << "B";
else if (score >= 60) cout << "C";
else cout << "D";
```

条件表达式结果是 `true` 或 `false`。不要把 `if (x = 1)` 写成判断，它其实是赋值。

## 3. switch 多分支

```cpp
switch (choice) {
case 1:
    cout << "新增";
    break;
case 2:
    cout << "删除";
    break;
default:
    cout << "未知操作";
}
```

`break` 很重要，不写就会继续往下执行。它像电梯到站开门，不按停就一路下去了。

## 4. while 与 do while

`while` 先判断再执行：

```cpp
while (n > 0) {
    cout << n % 10;
    n /= 10;
}
```

`do while` 先执行一次再判断：

```cpp
do {
    cin >> password;
} while (password != 123456);
```

## 5. for 循环

适合次数明确的重复任务。

```cpp
int sum = 0;
for (int i = 1; i <= 100; ++i) {
    sum += i;
}
```

三部分：初始化、循环条件、更新。别把更新忘了，否则循环可能变成“永动机”，电脑不累，你会累。

## 6. break 与 continue

- `break`：结束整个循环。
- `continue`：跳过本轮，进入下一轮。

```cpp
for (int i = 1; i <= 10; ++i) {
    if (i == 5) continue;
    if (i == 8) break;
    cout << i << ' ';
}
```

## 7. 循环嵌套与程序设计思路

循环嵌套常用于矩阵、图形输出、枚举组合。

```cpp
for (int i = 1; i <= 9; ++i) {
    for (int j = 1; j <= i; ++j) {
        cout << j << "*" << i << "=" << i * j << '\t';
    }
    cout << endl;
}
```

写复杂程序时先分解：输入什么、处理什么、输出什么。别一上来就写代码，代码会把你的混乱原样放大。

## 8. 本章小结

- 顺序、选择、循环是结构化程序设计的三件套。
- `switch` 里大多数情况要写 `break`。
- `while` 适合条件循环，`for` 适合计数循环。
- 循环嵌套要注意变量范围和退出条件。
- 复杂题先画流程，别让脑子直接裸跑。

[返回主目录](../README.md)