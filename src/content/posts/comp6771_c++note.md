---
title: C++ 学习笔记
published: 2026-08-23
description: 适用于 University of New South Wales COMP6771 Advanced C++ Programming 课程的学习笔记，按照一般教学顺序编写
image: ''
tags: [C++]
category: 笔记
draft: false
---

## Week 1
### 1. Basic types
直接从 C 中借得的类型：int, void, char, double

C++ 中的类型限制与系统相关，使用标准库 `<limits>` 可以得到该编译系统下的类型限制
### 2. `auto` 自动定性
使用 `auto` 声明变量时自动将右手侧值类型决定变量声明
```cpp
auto i = 0;      // i is int
auto j = 0.5;    // j is double
auto k = false;  // k is bool
```

### 3. const 声明常量
使用 `const` 关键词声明一个不可被改变的数值（常量），编程实践中，除非你知道它会需要被改变，否则**一律声明为常量**。

`const` 可置于变量类型的左右两侧，本课程惯例为中应当把 `const` 置于类型的**右侧**
```cpp
auto const meaning_of_life = 1999;   // good
const auto meaning_of_life = 1999;   // bad
```

### 4. CMake
使用 CMake 来自动化编译体验，在 `cpp-cmake` repo 中，`CMakeList.txt` 是 CMake 的配置文件，
```cpp
add_library(name, url)    // 加入此文件为库
link_libraries(name)      // 链接库

add_executable(name, url) // 加入此文件为可执行文件


add_test(name, name, ...) // 添加 ctest 测试文件（文件名需要为 executable）
```

使用 CMake 编译项目：
Step 1: 生成 Makefile 编译文件
```
cmake -B build
```

Step 2: 编译项目
```
cd build
make 
```
### 5. Reference 引用
此为 C++ 的语法特性，与 C 略有不同！

引用可以理解为变量的**别名**——它不是一个新变量，而是绑定到已有变量上的另一个名字，两者共享同一块内存。

使用 `&` 符号引用变量
```cpp
int x = 10;
int &r = x;   // r is a reference to x

r = 20;       // update r value as 20, but actually change x value
std::cout << x;  // -> 20
```

引用传参：referencing 在函数参数的使用
```cpp
// C style 数值传参：此时仅交换了副本，无效交换
void swap_by_value(int a, int b) {
	int tmp = a; a = b; b = tmp;
}

// C style 指针传参
void swap_by_ptr(int *a, int *b) {
	int tmp = *a;  // store the ptr a value pointed to as tmp
	*a = *b;
	*b = tmp;
}

// C++ style 引用传参
void swap_by_ref(int &a, int &b) {
	int tmp = a; a = b;b = tmp;
}
```

### 6. string 字符串
C++ 标准库有类型 `std::string` ， `#include <string>` 以使用字符串类型
三种常见的声明方法：
```cpp
#include <string>

std::string str;                             // declare empty string
std::string str = "Hello, World!";           // Initialize with a C-string literal
std::string str("Hello, World!");            // Constructor syntax
std::string str{'H', 'e', 'l', 'l', 'o'};    // Initialize with characters
```

使用 `std::format` 可以创建 format string，类似 Python f-string，使用 `{}` 作为占位符并使用类似 C 语言的排列方式：
```cpp
#include <format>
#include <string>

int age = 36;
auto name = std::string("John");

std::string s = std::format("{}: {}", name, age);
```

## Week 2
STL: Standard Template Library 标准模版库，是一种用于通过算法管理泛型和抽象数据集合的架构与设计哲学。其中，所有 STL 组件都是基于模板 (Templates) 的（参考泛型编程）。
### 1. STL Container 容器
在 STL 设计中，容器只负责存储数据，而对处理它们的算法一无所知。

#### 1.1 Sequential Containers 序列容器
序列容器将有限的对象集合组织成严格的线性排列：
- `std::vector` 动态调整大小的数组
	- `v.at(i)` 取得元素；时间复杂度 $O(1)$ ；
	- `v.push_back(i)` 尾部添加元素； $O(1)+$
	- `v.insert(x)` 中间插入或 `v.erase()` 删除需要移动元素；$O(n)$
- `std::array` 固定大小的数组，声明时指定大小，
- `std::deque` **d**ouble-**e**nd **que**ue 双端队列
- `std::foward_list` 单向链表
- `std::list` 双向链表，`.insert()` / `.erase()` 均为 $O(1)$

#### 1.2 Ordered Associative Containers 有序相关容器
以键值对（Key-value pair）形式存在，
- `std::set` 唯一键集合
- `std::multiset` 可重复键集合
- `std::map` 唯一键值对映射
	- 安全插入： `m.emplace(key, value)`，以代替 `m.insert(pair)`
	- 安全查找： `m.find(key)` 存在则返回指向 `value` 的 iterator，否则返回 `m.end()`
	- **不应**使用下标访问： `m["key"]` ，若其不存在会自动插入
- `std::multimap` 一对多映射

#### 1.3 Unordered Associative Containers 无序相关容器
- `std::unordered_set`
- `std::unordered_map` 

### 2. STL Iterator 迭代器
迭代器是对指针（pointer）的概念抽象，作为 STL containers 和 algorithms 的通用接口
- `a.begin()` 指向容器第一个元素的迭代器
- `a.end()` 抽象地指向容器**最后一个元素之后的下一个位置**（哨兵位）；其可作为有效的边界，但不可对其解引用。
- `*p` 解引用（dereferencing），取得迭代器所指向对象的值
- `p++` 迭代·迭代器，i.e. 将迭代器推进到下一个元素
- 若容器（container）本身只读（const）则会返回一个只读迭代器 `const_iterator`

#### Reverse 反向迭代
对一个 vector 反向迭代最方便的方法就是使用 `rbegin()` 和 `rend()`：
- `rbegin()` 指向 vector 最后一个元素
- `rend()` 指向 vector 首部前一个元素
反向遍历：
```cpp
for (auto iter = vec.rbegin(); iter != vec.rend(); ++iter) {
	std::cout << *iter << "\n";
}
```

#### Const 常量迭代
使用 `cbegin()` 与 `cend()` 创建常量迭代器 `const_iterator`，无论容器是否只读；
用法与普通迭代器一样，常量迭代器无法修改指向对象，

同样可以创建反向常量迭代器 `crbegin()` 和 `crend()`
### 3. STL Algorithm 算法

STL 算法是一些对容器进行操作的预定义迭代器，可以方便地调用以实现有用的操作。

#### 3.1 Sum 求和
三种古法求和方法：
```cpp
#include <iostream>
#include <vector>

int main() {
	std::vector<int> nums {1, 2, 3, 4, 5};
	
	// C-style old-fashioned way: DO NOT USE
	int sum = 0;
	for (int i = 0; i < static_cast<int>(nums.size()); i++) {
		sum += i;
	}
	
	// C++ Use iterator
	auto sum = 0;
	for (auto it = nums.begin(); it != nums.end(); it++) {
		sum += *it;    // accumulate the value deref from iterator
	}
	
	// C++ Use for-range
	int sum = 0;
	for (const auto& i : nums) {
		sum += i;
	}
	
	std::cout << sum << "\n";
	return 0;
}
```

使用 STL 求和算法 `std::accumulate()`：
```cpp
#include <iostream>
#include <vector>

int main() {
	std::vector<int> nums {1, 2, 3, 4, 5};
	
	int sum = std::accumulate(nums.begin(), nums.end(), 0);
	
	std::cout << sum << "\n";
}
```

#### 3.2 sort 排序
STL 排序函数传入迭代器的 begin, end 和比较函数（可选），默认为升序排列
```cpp
std::vector<int> vec = {1, 9, 2, 8, 3, 7, 4, 6}；
std::sort(vec.begin(), vec.end())  // default: ascending order

// Customised compare function: descending order
std::sort(vec.begin(), vec.end(), [](const int &a, const int &b) {
	return a > b;
});
```


显然 STL 算法的使用与迭代器密切相关，迭代器是操作容器的接口，STL 算法是基于迭代器设计的函数抽象。
## Week 3
### 1. Lambda function 无名函数
使用 `[](){}` 创建无名函数，C++ 中不能在函数内部嵌套定义函数，必须使用 lambda function：
```cpp
[captures](parameters){
	// function body
}
```
其中 captures 捕获，即表示**访问的外部变量**；parameters 为函数参数

e.g.
```cpp
[](int &a, int &b) {return a > b};
```

捕获可以分为数值拷贝（value copy）和引用捕获（reference capture）两种方式：
```cpp
// value copy
auto f = [x](int a, int b) {
	// function body
}

// reference capture
auto g = [&x](int c, int d) {
	// function body
}
```


### 2. Scope （程序）作用域
作用域定义了某个名称（可以是变量、函数、类）的可见且可访问的范围，体现为：
- （其）作用域始于变量的定义
- 作用域通常终止于下一个 `}` 符号
- 作用域内生成的对象会在程序离开作用域后全部摧毁

定义变量时，尽可能靠近其第一次使用的地方，在 C++ 中，把所有变量定义在顶部通常是个坏主意👎，这与 C++ 编译程序的内部机制有关。

举例：局部变量 x，定义在函数 `f()` 内，在函数执行完毕后其即被摧毁：
```cpp
void f() {
	int x = 0;
}   // x destroyed here
```


### 3. Namespace 命名空间
命名空间允许让你在构建自己的函数、变量或者数据结构更加从容，

例如 STL 中的 vector 处于 `std` namespace 下，表示属于 C++ 标准库：`std::vector<class Tp>`

构建自己的命名空间使用 `namespace <name> {}`
```cpp
namespace my {
	int x = 0;
	auto println(const &T) {
		std::cout << T << "\n";
	}
}

// 访问自定义命名空间下的对象
auto L = my::x * 9;
my::println(L);
```

在某个命名空间下，你也可以创建一个取消命名（unnamed）的空间以实现类似 C 语言的函数调用

```cpp
namespace word_ladder {
namespace {
	bool valid_word(std::string const& word);
} // namespace
} //namespcae word_ladder

auto is_valid = valid_word(w);
```

使用该关键词来创建别名（alias）以缩短嵌套的命名空间
```cpp
namespace chrono = std::chrono;
```
### 4. OOP 面向对象的编程
#### 4.1 Object lifetime 对象寿命
1. An object is a piece of memory of a specific type that holds some data
	对象本质是一片有特定类型并存有数据的内存
- 所有 variables 都是对象
- wtf
2. Object lifetime starts when it comes in scope
	对象的生命周期从它进入作用域时开始。
- 构建对象为伊始
- 每个类型有至少一个构建函数（constructor）并定义了其构建方式

> 这是基本类型遵循的行为，但你可能凭直觉就知道这一点。对于类（class），我们往往会更明确地思考这个问题。

#### 4.2 Construction 对象构建
Construction 描述了物理上分配内存和逻辑上创造对象的过程，在 C++ 中，最常见的做法是使用构造函数（constructor）实现对象的构建。

#### 4.3 C++ 的 OOP 概念
1. class 类
  - 定义新的类型
  - 使用 `class` 或 `struct` 创建
  - 有可能会定义一些成员（members），可以是数据或函数
  - 使用构造函数 constructor 实例化
2. member function 成员函数
  - 必须在 class 的内部声明（declare）
  - 有可能在 class 的内部定义（define）
  - 有可能被声明为常量（const），当它不改变成员变量时
3. 数据成员应当 private ，意图为表示对象的状态
4. `struct` 和 C 中的 struct 是一样的，它与 `class` 的区别是 struct 的数据成员默认公开访问，而 class 的则是默认私有。


### 5. Class 类
class 是一种类型（type），和 `<int>, <double>` 等一样，不过它是自定义的
#### 5.1 Private & Public
Public: 可从外部访问的类组件，Private 反之。
习俗：对于 private 变量在结尾处加上下划线 `_` 

```cpp
class myclass {
public:
	point(int x, int y) {
		x_ = x;
		y_ = y;
	}
	int x() {
		return x_;
	}
	int y() {
		return y_;
	}

private:
	int x_;
	int y_;
};


int main(void) {
	auto new_1 = point(1, 2);  // 生成一个名叫 new_1 的 point 实例
	std::cout << new_1.x() << "," << new_1.y();
}
```

#### 5.2 Member access control 成员访问控制
```cpp
class foo {
public:
	// 可被公开访问的成员

protected:
	// 可被成员，友员（friend）和子类（subclass）访问的成员

private:
	// 只可被成员和友员访问的成员
}
```

#### 5.3 Constructor 构建函数
- 构建函数定义了类数据成员如何初始化
- 构建函数的名称和类名一致
- 默认的初始化流程由默认构造函数处理
- 除非自定义构造函数，否则以默认构造函数初始化实例


##### Initialiser List 初始化列表
这是 C++11 引入的新语法，简化了以往需要编写初始化时冗杂的赋值函数，使用 `to_init {init_value}` 的结构快速初始化变量。

```cpp
class Point {
public:
	Point(int x, int y)
	: x_ {x}
	, y_ {y}
	{};   // 无额外工作需要，构建函数留空

private:
	int x_;
	int y_;
};
```
C++11 以前定义赋值函数的方法参考 5.1 Private & Public 所示内容。

##### Delegating Constructors 委托构造函数
构造函数本身可以在初始化列表（initializer_list）中召唤另一个构造函数
- 被召唤的构造函数必须构建所有的数据成员，所以在初始化列表中的构造函数不应声明
- 被召唤的构造函数会在发起者之前被召唤
- 默认值会被采用，而非重载 / 代理构造函数


#### 5.4 Destructor 析构函数
当你打算毁灭一个对象（object）的时候，可能会需要析构函数，特别是当你从 heap 分配了内存时，不定义析构函数会导致内存泄漏（memory leakage），常用于：
- 释放指针
- 关闭文件
- 解锁 mutexes
- 放弃数据库操作
一定要使用 `noexcept` 关键词，向编译器作出保证你的析构函数不会抛出 exception（你也应当信守承诺，不抛出 exception）

它和构造函数一样声明在 class public field 的内部：
```cpp
class MyClass {
	~MyClass();
}
```
定义：
```cpp
MyClass::~MyClass() {
	// destructor definition
}
```

#### 5.5 static member 静态成员
使用 `static` 关键词可以创建**不依赖于实例**而是写入类定义的静态函数或变量，在调用的时候使用：
```cpp
<type name>::<static function name>();

<type name>::<static variable name>;
```

#### 5.6 `this` 指针
类似于 Python 中的 `self`，C++ 使用 `this` 作为指代对象自身的指针，使用它来访问对象自身的数据成员也有继承自 C 的 `->` 运算符用于代替 `*this.member`
```cpp
class MyClass {
public:
	MyClass(int i) {
		this->i_ = i;
	}
private:
	int i_;
}
```

#### 5.7 Rule of 5 构建类的五项原则

如果你在构建一个 class 的时候不能让所有操作都设为 default，就应当考虑以下内容的定义：
1. Destructor 析构函数
2. Copy constructor 拷贝构造函数
3. Move constructor 移动构造函数
4. Copy assignment 拷贝赋值（运算符重载）
5. Move assignment 移动赋值（运算符重载）

## Week 4
### 1. Operator Overload 运算符重载
运算符重载允许你重新定义 C++ 内置运算符（如 +、-、、、<< 等）在用于自定义类型（类或结构体）时的行为。

| Type                  | Operator             | Member / Friend      |
| --------------------- | -------------------- | -------------------- |
| I/O                   | < >                  | F                    |
| Arithmetic            | + - * /              | F                    |
| Relational, Equality  | >, <, >=, <=, ==, != | F                    |
| Assignment            | =                    | M                    |
| Compund assignment    | +=, -=, *=, /=       | M                    |
| Subscript             | [ ]                  | M (const / nonconst) |
| Increment / Decrement | ++ --                | M                    |
| Arrow, Deref          | ->, *                | M (const / nonconst) |
| Call                  | ( )                  | M                    |

e.g. I/O 运算符重载：
```cpp
class Point {
public:
	Point(int x, int y)
	: x_ {x}
	, y_ {y}
	{};
	
	friend std::ostream& operator<<(std::ostream& os, Point const& p);
	friend std::istream& operator>>(std::istream& is, Point& type);

private:
	int x_;
	int y_;
}

// definitions
std::ostream& operator<<(std::ostream& os, Point const& p) {
	os << "(" << p.x_ << ',' << p.y_ << ")";
	// print "(x,y)"
}
```

对于双目运算符重载，需要将其声明为 friend 而非 member，因为成员函数的左侧操作对象必须是该 class 的对象（实例），否则无法正确触发隐式转换 (implicit conversion)。
传入一个作为右操作对象，而自身作为左操作对象（this ptr）。

### 2. Customised Iterator 自定义迭代器

#### Iterator Invalidation 迭代器失效
STL 中的迭代器已经定义好了该如何移动；但此时你在用迭代器遍历容器的时候对容器增加 / 删减元素的时候，后续的迭代器会**立即失效**。
***通常来说***若仅仅修改某个数值不会导致迭代器失效。


#### Properties 属性

自定义迭代器需要掌握迭代器的以下属性：
- Category 种类 (input, output, forward, bi-directional, random-access)
- Value Type 数值种类 `<class T>`
- Reference Type 引用种类 (`T&` or `const T&`)
- Pointer Type 指针种类（`T*` or `const T*`）【不严格要求】
- Difference Type 步长

最小化的一个定义：
```cpp
class Iterator {
public:
	using iterator_category = std::foward_iterator_tag;
	using value_type = T;
	using reference = T&;
	using pointer = T*;
	using difference_type = int;
	
	reference operator*() const;
	Iterator& operator++();
	Iterator operator++(int) {
		auto copy {*this};
		++(*this);
		return copy;
	}
	
	pointer operator->() const {
	return &(operator*());
	}
	
	friend bool operator==(const Iterator& lhs, const Iterator& rhs) {
		// function body here
	};

	friend bool operator!=(const Iterator& lhs, const Iterator& rhs) {
	return !(lhs == rhs);
	};
}
```

#### 3. Container Requirements 容器要求
容器只需要允许 `std::[cr]begin / std::[cr]end` method 即可
```cpp
class Container {
	class iterator {
		// iterator definition
	};
	// add reverse tag
	using reverse_iterator = std::reverse_iterator<iterator>;
	using const_reverse_iterator = std::reverse_iterator<const_iterator>;
	
	
	// define
	iterator begin();
	iterator end();
	
	// define reverse
	reverse_iterator rbegin();
	reverse_iterator rend();
	
	// define const one
	const_iterator begin() const {
		return cbegin();
	}
	const_iterator cbegin() const;
	
	const_iterator end() const {
		return cend();
	}
	const_iterator cend() const;
	
	// define reverse const one
	const_reverse_iterator rbegin() const {
		return crbegin();
	}
	const_reverse_iterator crbegin() const;
	
	const_reverse_iterator rend() const {
		return crend();
	}
	const_reverse_iterator crend() const;
}
```

Automatic reverse iterator 自动反转迭代器
```cpp
class Container {
	class iterator {
		// iterator definition
	};
	// add reverse tag
	using reverse_iterator = std::reverse_iterator<iterator>;
	using const_reverse_iterator = std::reverse_iterator<const_iterator>;
	
	// define reverse
	reverse_iterator rbegin();
	reverse_iterator rend();
	
	// define reverse const one
	const_reverse_iterator rbegin() const {
		return crbegin();
	}
	const_reverse_iterator crbegin() const;
	
	const_reverse_iterator rend() const {
		return crend();
	}
	const_reverse_iterator crend() const;
}
```
## Week 5
### 1. Exceptions 异常
包含 exception 头文件：`#include <stdexcept>`

任何变量都可以是 exception，conceptual structure 类似于：
```cpp
try {
	// running code block
} catch (const std::) {
	// run when catch the error
} catch (std:exception&) {
	// run when catch any types of error(exceptions)
};
```

#### 1.2 Rethrow  异常重抛出
在局部 Catch 块中记录日志或清理部分资源后，使用单独的 `throw` 语句继续向上传递原异常。
#### 1.3 Exception catching 异常捕获
整个异常处理应当遵循抛出数值（throw by value）捕获常量引用（catch by const ref）
捕获引用的好处：
- 更加高效，减少拷贝工作
- 没有切片问题（与 polymorphism 相关）

#### 1.4 No throw guarantee 不掷异常保证
表示保证操作会成功，即使是在特殊情况下（掷出的错误被正确地处理了），使用 `noexcept` 关键词以作出不掷错保证，C++ 中以下行为是 noexcept 的：
- 关闭文件
- 释放内存
- 构建函数所做的事情（通常是移动构造函数 move constructor）
- 在 stack 中创建一个无关紧要的对象

#### 1.5 Exception Safety 异常安全
强 ES 表示程序不会造成糟糕的结果，譬如内存泄漏和操作对象的不可恢复性损坏

### 2. Resource management 资源管理
对于 C++ 的 object 来说，有三种方法可以让其在作用域（scope）之外继续存活：
- 使用拷贝 （copy） 在函数中被 return （有局限）
- 通过引用（reference）从函数中返回它（坏方法🙅）
- 作为堆资源（heap resource）从函数中 return ✅ 

当函数返回引用变量时，必须保证其引用变量的作用域在该函数之外。原因是当函数返回的时候，函数本身在 stack 上会被摧毁，但返回的引用仍然会取得原来地址的数据
✅ 这是可以的：
```cpp
auto okay(int& i) -> int& {
	return i;
}

auto okay(int& i) -> int const& {
	return i;  // return a const integer reference
}
```
❌ 这样是不可以的：
```cpp
auto not_okay(int i) -> int & {
	return i;
}

auto not_okay() -> int& {
	auto i = 0;
	return i;
}
```

### 3. 左右值
lvalue: 一个对象的引用之表达式；不是 lvalue 的就是 rvalue

左值的引用：`T&` ，常量引用：`T const&`  

右值的引用：`T&&` 

### 4. Move object 移动对象
- 始终将你的移动构造函数 / 运算符重载声明为 noexcept 否则会拖慢代码速度
- 除非另有规定，已移动的对象处于有效但 unspecified 的状态。
- 移动是一种最佳化的拷贝 Moving is an optimisation of copying
	- 差别在于被移动的对象是**可变**的。
- 被移动的对象必须置于有效的状态
- 编译器生成的移动构造函数 / 移动赋值是 memberwise 的

移动方法：`std::move()` 和 `std::exchange()`，对于具有 RAII 特性的复杂对象使用 `std::move()` 而对于管理原生对象如普通指针和常规变量。

#### 4.1 std::exchange() 
行为：将目标值替换为新值，并返回旧数值

#### 4.2 std::move()
行为：将 lvalue 变为 rvalue（静态类型转换，等效于 `static_cast`）
被移动对象会被自己定义的移动构造函数清理

### 5. RAII
**Resource Acquisition Is Initialisation 资源攫取即为初始化**
- Resource 指代 heap object 
- RAII 适用于封装在对象内部的资源：
	- 由构造函数创建的实例
	- 由析构函数销毁 / 释放的资源
- 所有的 resource 都应被以下对象所拥有：
	- 另一个资源（智能指针、数据成员）
	- 位于 stack 的有名资源（lvalue）
	- 无名临时变量（rvalue）

### 6. Object lifetime 对象寿命
- 对于有名资源：
	- 位于函数内的变量之寿命受制于函数的作用域（scope）
	- 数据成员的寿命受制于该实例（class instance）
	- e.g. vector 中的元素与 vector 一致
- 对于无名资源：
	- heap object 的寿命始于创造（new 或使用智能指针）
	- 寿命终于释放

## Week 7
### 1. Smart Pointer 智能指针
头文件引入： `#include <memory>`

从 C++11 开始引入，智能指针是一种将 heap object 封装到命名的 stack 对象中的方法，以便更安全地管理对象的生命周期。
stack 中的对象是自动管理的，此举即为将手动管理的工作自动化，通常对于某个对象可以有两套搭配方式：

| Owner      | Observer    |
| ---------- | ----------- |
| uniq_ptr   | raw pointer |
| shared_ptr | weak_ptr    |

P.S. 在 C++ 中空指针应设置为 `nullptr` 而非 C 中的 `NULL`
#### 1.1 Unique pointer
最常见的智能指针，功能上完全代替原生指针，使用 `std::unique_ptr<T>`，获得近似原生指针的开销。
uniq_ptr 持有对象的所有权，一旦其被销毁 / 释放，所拥有的对象也随之销毁

常用 method:
1. `.get()` 创建一个 observer （返回 raw ptr）用于观察对象
2.  `.reset()` 重置当前智能指针的状态，即释放所属资源（delete）并置空（nullptr）
3. `.release()` 交出所有权，返回 raw pointer，智能指针置空
#### 1.2 Shared Pointer
共享指针，使用 `std::shared_ptr<T>` ，由多个对象拥有控制权，并且自带计数器记录拥有者的个数，当计数器归零是所指向的对象也会被销毁

1.  `.use_count()` 取得所有权计数
2. `.reset()` 将强引用计数减 1。如果引用计数归零，则销毁所指对象并释放控制块；当前智能指针置空
3. `.reset(new_ptr)` 将当前引用计数减 1（若归零则释放旧资源），随后让 `new_ptr` 接管控制权
4. 直接使用拷贝赋值创建新的共享指针

```cpp
auto sp1 = std::make_shared<int>(11);
auto sp2 = sp1;    // use_count = 2 二者共有所有权

sp1.reset();       // sp1 置空，use_count = 1
sp2.reset();       // 对象 11 被销毁
```

**循环引用**：两个 `shared_ptr` 互相指向对方会导致悬挂指针，此时双方 `use_count` 均为 2，当双方离开 stack 作用域后，引用计数减为 1，从而无法触发析构函数导致内存泄漏。

#### 1.3 Weak Pointer
弱指针，用于**观察** `shared_ptr` 所管理的对象，但不增加引用计数。这解决了 `shared_ptr` 的循环引用问题。

特点：
- 不拥有对象的所有权
- 不能直接访问对象
- 必须先转换为 `shared_ptr` 才能使用
- 当所有 `shared_ptr` 都释放时，对象会被销毁

`.lock()` 升级关系，返回一个 `shared_ptr` 并会增加 `use_count`，若原对象计数为零（即已被销毁），则返回 `nullptr`
`.reset()` 自身置空，放弃观察权

以上指针在取得内存空间时应使用 C++14 起开始的新式声明方式而非 `new`:
```cpp
auto p1 = std::make_shared<int> (5);
auto p2 = std::make_unique<int> (4);


// old style (Before C++14)
std::unique_ptr p3 <int> = (3);
std::shared_ptr p2 <int> = (3);
```

除非某对象需要有多个所有权，不然应当优先使用 `uniq_ptr`

### 2. Dynamic Polymorphism 动态多态
Polymorphism 允许将子类的对象作为父类的对象来使用，是面向对象编程的基石。C++遵循“不为你没有使用的功能付出代价”的原则，因此多态是可选的 (polymorphism is **optional**)。

#### 2.1 Inheritance 继承
三种继承关系：
1. public 公有继承：通常应使用（除非有合理的理由）
2. protected 保护继承
3. private 私有继承

公有继承语法：`class DerivedClass: public BaseClass`

在公有继承下，BaseClass 的各个访问属性在派生类（DerivedClass）中保持不变，即 public 公开访问，protected 派生类（DerivedClass）内部可访问，protected 不可直接访问。

#### 2.2 Object slicing 对象切片
在 C++ 公有继承中，对象切片指的是：当把一个派生类对象按值（by value）赋值给或初始化一个基类对象时，派生类特有的成员变量和虚函数表指针会被直接“切掉”，只保留基类部分的拷贝。

派生类对象的内存大小通常大于基类对象：
- **派生类对象内存布局** = `[ 基类数据成员 + 虚表指针 (vptr) ]` + `[ 派生类新增数据成员 ]`
- **基类对象内存布局** = `[ 基类数据成员 + 虚表指针 (vptr) ]`

使用指针 / 引用传参而非数值

#### 2.3 virtual & override
为实现动态多态，即让派生类中继承的函数能够正确访问派生类而非基类，在 BaseClass 中需要重写的函数开头使用 virtual 关键词开头让编译器将其虚拟化，在 DerivedClass 的对应函数中使用 override 关键词结尾以示覆盖基类的定义。

对于**析构函数（destructor）** 来说，基类的定义必须 virtual，此时派生类如没有需要手动管理的内存资源，其就能自动被销毁。

#### 2.4 pure virtual function 纯虚函数
纯虚函数表示基类定义的（🧔）函数只提供接口定义，而没有默认的实现，即必须被派生类继承其接口且给出明确定义。使用 `= 0` 设置为纯虚函数。

```cpp
class Baseclass {
public:
	virtual auto make(int i) = 0;
}
```


#### 2.5 Static vs Dynamic
静态表示在编译器确定，动态表示在运行过程（runtime）中确定

static binding 静态绑定：在编译器确定调用什么函数
dynamic binding 动态绑定：根据动态类别于运行时决定调用的函数

#### 2.6 up-casting 向上转型
编译器允许将派生类对象的指针或引用隐式转换（implicit convert）为基类的指针或引用。这是实现动态多态（运行时多态）的基础。
注意：
- 一定要使用基类 ptr / ref 而非数值拷贝 / 实例构造，否则会 object slicing
- 基类析构函数必须 virtual


#### 2.7 final
使用 `final` 关键词向编译器保证该类或函数不会再被重写（override），这允许编译器执行“去虚化”优化，将动态查找转为更快的静态绑定或内联调用。

#### 2.8 Misc
派生类构造函数 / 析构函数在调用时会 unwinding，逐层向上层调用其对应的构造 / 析构函数

## Week 8
### 1. Generic programming 泛型编程
Polymorphism 提供了一套适用于不同类型的实体的接口
两种类型：
- static
	- function overloading
	- templates (泛型编程)
- dynamic
Generic programming 泛型编程则是将程序逻辑进行抽象，使其独立于某种特定的变量 / 数据结构类型
STL 就是一种泛型编程产物，

### 2. templates 模版
使用 `template<typename A, typename B>` 后接函数或类定义

```cpp
template <typename T>
auto min(T a, T b) {
	return a < b ? a : b;
}

template <typename T, std::size_t size>
auto findmin(const std::array<T, size> a) -> T {
	T min = a[0];
	for (std::size_t i = 1; i < size; ++i) {
		if (a[i] < min)
			min = a[i];
	}
	return min;
}
```
`T` 是一个模版类型参数（type parameter ），未知类型且没有数值
`size` 是一个非模版类型参数（Non-type parameter），已知类型但未知数值

### 3. Inclusion Compilation Model
对于使用模版的泛型编程项目，你应当把函数定义于头文件中，而非定义-声明分离，因为模版的定义必须在编译期就被编译器知晓，而 linking 发生在编译之后，编译器无法根据没有 linking 的头文件生成对应的代码。

总结：
- Lazy instantiation 惰性实例化：仅当成员函数在被调用的时候才会生成实例
- 对于类来说同样适用
- 模版的实现必须定义在头文件中
- 每一次的模版实例化都有其独有的静态成员（static member）和友员（friend）

### 4. Specialisation 特化
以上定义的模版都是完全的泛型编程，同样有不完全泛型的编程
- Partial specialisation
- Explicit specialisation

什么时候应当特化？
- 当你需要保存已存在的语义
	- `std::is_pointer`
- 你想编写 type traits
	- `std::is_integral`
- 对于某个特定的类型，你有一个最佳化方案
	- `std:;vector<bool>`

什么时候你不应当 specialisation？
- 函数
- 你觉得更改某个 class 的某些特性或某个特定的类型很酷

## Week 9 Meta-programming 元编程
### 1. Decltype
语义相当于：typeof 函数，用于在编译期推导表达式或实体的精确类型，但不实际执行它
- 单括号取得类型 `T`
- 双括号取得类型之左值引用 `T&`
- PRvalue 纯右值取得原始值类型 `T`
- Xvalue 将亡值取得右值引用 `T&&`

```cpp
int i;
int j& = i;

decltype(i) x;    // int - variable
decltype((j)) y;  // int& - lvalue
decltype(5) z;    // int - prvalue
```

### 2. Binding
Binding 分为静态绑定与动态绑定

| 特性         | 静态绑定 (Static Binding) | 动态绑定 (Dynamic Binding)   |
| ------------ | ------------------------- | ---------------------------- |
| **解析时机** | 编译期 (Compile-time)     | 运行时 (Run-time)            |
| **解析阶段** | 编译阶段完成符号地址映射  | 运行阶段通过内存查找确定地址 |
| **触发机制** | 普通函数、重载函数调用    | 通过 `virtual` 函数触发      |
| **性能特征** | 无额外运行时开销          | 存在 Vtable 查找的微小开销   |
| **灵活性**   | 较低，逻辑在编译时固化    | 极高，支持运行时的行为切换   |
| 适用规则：   |                           |                              |

| Parameters \ Arguments        | lvalue | const lvalue | rvalue | const rvalue |
| ----------------------------- | :----: | :----------: | :----: | :----------: |
| `template T&&`                |   ✅    |      ✅       |   ✅    |      ✅       |
| `T&`                          |   ✅    |      ❌       |   ❌    |      ❌       |
| `const T&`                    |   ✅    |      ✅       |   ✅    |      ✅       |
| `T&&`                         |   ❌    |      ❌       |   ✅    |      ❌       |
| `const T&` 是适用范围内最广的 |        |              |        |              |

### 3. constexpr
`constexpr` 允许函数或变量在编译阶段计算出结果。通过将逻辑前置，我们可以构建出极其高效的程序，因为原本属于运行时的计算开销在程序启动前就已经完成了。

以下情况可以考虑使用：
- 对于变量，其可以在编译器被计算
- 对于函数，它的输入 / 参数在编译期可知，且该函数能够运行在编译期

```cpp
#include <iostream>

constexpr long long factorial(long long n) {
	if (n == 10000000000000)
		return 0;
	return n + factorial(n + 1);
}

int main() {
	constexpr long long nf = factorial(1);
	std::cout << nf;
}
```
