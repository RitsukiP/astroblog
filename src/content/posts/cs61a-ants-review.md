---
title: CS61A Ants Review
published: 2024-11-19
description: ''
image: ''
tags: [CS61A]
category: 笔记
draft: true
---

## General Review

### Dot Expression

Both instances and classes have attributes that can be looked up by dot expression.

Formulaic: `<expression>.<name>`

点表达式的程序处理分为以下四步：

1. 处理点号左侧的 `<expression>` 
2. `<name>` 与该对象的实例属性进行匹配；如果存在该名称的属性，则返回其值
3. 若无，则在 Class 中查找 `<name>`，从而产生一个类属性值（class attribute value）
4. 除非它是一个函数，否则将返回该值，在这种情况下将返回 bound methods。

### Attributes: Class vs Instance

**Class Attribute** is the attribute of a class. A `class` statement creates a new class and binds that class to `<name>` in the first frame of the current environment.

```python
class <name>:
    <suite>
```

Assignment & def statements in `<suite>` create attributes  of the class (not name in frames).

以下将 Class Attributes 简称为 CA，Instance Attributes 简称为 IA。

如上文所讲，CA 是整个类（class）的属性，即这一属性是对于所有实例（Instance）都是一样的，每个实例都可以用点表达式（Dot Expression）来访问它：

```python
class Account:
    interest = 0.2		# This is a CA
    def __init__(self, account_holder):
        self.balance = 0	# balance is the IA of self(an Instance)
        self.holder = account_holder
        
        # Additional methods would be defined here
```

```python
>>> tom_account = Account("Tom")
>>> jim_account = Account("Jim")
>>> tom_account.interest	# Use dot exp to access CA
0.02
>>> jim_account.interest	# Access the same one, so get same result
0.02
```

### OOP 中的术语

- 所有的对象（Object）都有作为「名称-数值对」的属性（Attribute）
- 类（class）是对象（Object）的一种，所以类是对象，其即有属性
- IA 即实例之属性

关于函数：

- 函数也是对象
- Bound methods 也是对象：其为一个将第一个参数 `self` 绑定到实例上的函数
- 点表达式处理其类属性为函数的 bound methods：`<instance>.<method_1name>`

### Methods vs Functions

函数正如之前所学内容所定义的一样；而 Bound methods 将函数与将调用该方法的对象耦合在一起。

`Object` + `Function` = `Bound method`

