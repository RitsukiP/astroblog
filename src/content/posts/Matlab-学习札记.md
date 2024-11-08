---
title: Matlab 学习札记
published: 2022-01-20
description: 本博文主要记录一些在学习 Matlab 时的一些指令和公式，方便自己回看和复习各种指令以及相关知识。
category: 笔记
tags: [MATLAB]
---

# 使用指南
1. 本文 Code block 中的注释采用 C++ 单行注释样式，即`//`
2. 例子中的 Code block 为在 MATLAB R2021a 环境下的 Command Window 显示内容
3. 本文在矩阵部分内容遵从中国大陆的命名方式，即 Row 为行，Column 为列
4. 由于内容较为庞大，建议在桌面端浏览本页面，请善用目录
5. 部分内容可能过时或出现纰漏，且不展开完整的指令库，如遇问题请求助 Documentaion

*本文将不断更新，迭代信息请参考更新日期*


# 笔记正文
## 一、 基本操作
### MATLAB 系统环境
有四大主窗口

- MATLAB Rxxxxa //主窗口
- Current folder
- Command Window
- Workspace

#### 命令提示符 `>>`

当 Command Window 出现此符号时表示 MATLAB 已就绪，可以输入命令

**此时点击 PgUp/PgDn 来上翻/下翻命令记录**

#### 续行符 `...`

在一串命令后加上此符号时表示将下一行的命令续在上一行上，为同一条命令。

示例：
```
>> s = 1/2 + 1/3 + 1/4 + ...
1/5 + 1/6 + 1/17
```
#### 清除 Command Window `clc`

执行该命令将会清除 Command Window 上的内容

#### Current folder

可以使用命令来操控当前文件夹

使用 `cd` (Change Directory) 指令更改文件夹

`>> cd D:` 设置当前文件夹为 D:\

使用 `mkdir` (Make directory) 指令创建文件夹

`>> mkdir work`
> 此时在 D:\ 下创建了文件夹 \work
> 
> 可使用 `cd D:\work` 指令进入该文件夹

#### Workspace
工作区（Workspace）是 MATLAB 用于存储各种变量和结果的内存空间，
可用于变量的显示和操作

#### MATLAB 的搜索路径

当搜索一个字符串时，MATLAB 按照以下类型顺序进行检索：

1. 变量
2. 内部函数
3. 程序文件
    - 当前文件夹下的文件
    - 文件搜索路径文件夹中的程序文件

**请注意！不要使用和 MATLAB 函数名相同的名字用作变量**

#### 清除命令 clear
`>> clear`

使用 `clear` 加上函数名来清除特定的变量:

`>> clear cos`  
执行后将清除工作区的全部变量

#### 命令行分号使用
在命令后加上分号 `;` 结果将不会立即显示

#### 设定路径命令 path
`>> path(path,'D:\work')`   
将 D:\work 文件夹纳入搜索路径


或者在 Home（选项卡）- ENVIRONMENT（Section）- Set Path
来设定路径。

### 变量
MATLAB 使用变量无需 declaration，直接赋值即可

e.g.    
`A = 10`

使用 `iskeyword` 来查看什么不能作为变量名：
`>> iskeyword`

### 作为一个工程计算器
基本的四则运算和幂运算: `+ - * / ^`

`ans` 为默认的输出输出结果

> **规则**
> - 从左到右进行运算
> - 运算优先级
>   1. 括号`（）`
>   2. 乘方 `^`
>   3. 乘除 `* /`
>   4. 加减 `+ -`

### 嵌入式函数（复合函数）
e.g.
> `f(g(x)) = f(alpha), alpha = g(x);`

### MATLAB 数值数据
**数值数据的分类**

- 整型
- 浮点型
- 复数型

**整型数据转换**

`uint8` 将数据转化为无符号 8 位整数。

`int8` 将数据转化为有符号 8 位整数。

示例：
```
>> x=int8(129)

x =

  int8

   127    //因为 int8 最大值为 127，此处自动转化为 127

>> x=uint8(129)

x =

  uint8

   129
```
**浮点型**

浮点型分为单精度（占用 4 Byte）和双精度（占用 8 Byte）

> 在 MATLAB 中，浮点型默认为**双精度型**数据

可使用 `single` 函数转化为单精度型；

使用 `double` 函数转化为双精度型；

```
>> class(4)     //使用 class 函数查看其数据类型

ans =

    'double'    //说明默认为双精度类型

>> class(single(4))

ans =

    'single'
```
**复数型**

复数型数据包含实部和虚部，二者默认为双精度型；

虚数单位用 i 或者 j 表示：
```
>> 6 + 5i

ans =

   6.0000 + 5.0000i

>> 6 + 5j

ans =

   6.0000 + 5.0000i
```
使用 `real` 函数求复数实部；
使用 `imag` 函数求复数虚部

数值数据输出格式
format 命令：`format 格式符`

e.g.
```
>> format long    //输出 long 格式，保留小数点后 15 位
>> 50/3

ans =

  16.666666666666668

>> format    //默认输出 short 格式，保留小数点后 4 位
>> 50/3

ans =

   16.6667
```
注意！format 指令只影响输出格式，不影响计算和储存

**快速查询变量**
使用 who 命令来查看所有变量
`>> who`

使用 whos 命令来查看变量更多的信息

`>> whos`

示例：
```
>> A=10

A =

    10

>> whos
  Name      Size            Bytes  Class     Attributes

  A         1x1                 8  double              
```

**数学函数**
函数调用格式为：
> `function(value)`
> 
> //function 为函数名，value 为函数自变量的值（矩阵变量）

e.g.
```
>> A=[4,2;3,6]

A =

     4     2
     3     6

>> B=exp(A)

B =

   54.5982    7.3891
   20.0855  403.4288
```

**使用了自然指数函数 `exp`**

#### 常见数学函数的应用
1. 三角函数
> 默认以弧度制为单位，使用角度制需要在函数名后加 `d`

e.g.
```
>> sin(pi/2)

ans =

     1

>> sind(90)

ans =

     1
```
2. ABS 函数
```
>> abs(-4)

ans =

     4

>> abs(3+4i)

ans =

     5

>> abs('a')

ans =

    97
```

### MATLAB Calling Priority
1. Variable
2. Built-in function
3. Subfunction
4. Private function
    - MEX-file
    - P-file
    - M-file
## 二、向量和矩阵
### Row Vector
`>> a = [1 2 3 4]`

### Column Vector
`>> b = [1;2;3;4]`

```
>> a*b      //点积运算

ans =

      30       

>> b*a      //叉积运算

ans =

       1              2              3              4       
       2              4              6              8       
       3              6              9             12       
       4              8             12             16       
```
### Matrix
在一个 Row 里面用空格空开，换行时用分号分开

### Array Indexing 数组索引
1. 使用 array(n) 来检索数组名为 array 的第 n 项
示例：
```
>> a=[1,2,3,4]

a =

     1     2     3     4

>> a(3)

ans =

     3
```

2. 矩阵内检索
- 方法一：使用 A(x,y) 来检索矩阵名为 A 的 第 x 行，第 y 列

- 方法二：使用 A(n) 来检索矩阵名为 A 的 Matrix，以 先由上至下，后由左至右 的顺序编号。

$$
A = \begin{bmatrix}
a  & b & c\\\\
d  & e & f\\\\
g  & h & i
\end{bmatrix}
$$

从 1 号到 9 号 其顺序为：
$$a, d, g, b, e, h, c, f, i$$

检索方式如下：

**Type 1**

`>> A(3,2)` //第三行第二列，即 $h$

`>> A([1 3],[1 3])` //逗号前为 Row，逗号后为 Column；调取第 1、3 条 Row 和第 1、3 条 Column 的交集形成矩阵：
$$
\begin{bmatrix}
a  & c\\\\
g  & i
\end{bmatrix}
$$

**Type2:**

`>> A(8)` //按照 “先由上至下，后由左至右” 的顺序检索第 8 个，即为$f$

`>> A([1,3,5])` //按照上述顺序检索第 1, 3, 5 个，即为 $a, g, e$

`>> A([1 3;1 3])` //按照上述顺序，形成矩阵：
$$
\begin{bmatrix}
a  & g\\\\
a  & g
\end{bmatrix}
$$

### 替换矩阵元素 Replacing Entries
直接将某个值赋给矩阵的元素：

`A(x,y)= K`

将 K 赋给 A 矩阵中第 x 行，第 y 列的元素







