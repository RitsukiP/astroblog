---
title: 树递归复习
published: 2024-11-10
description: ''
image: ''
tags: [CS61A, Python]
category: 笔记
draft: true
---

## 基础递归

在梳理树递归（Tree recursion）之前，先回顾基础递归的结构。

参考 [Composing Programs 1.7](https://www.composingprograms.com/pages/17-recursive-functions.html) 的开头部分：

> A function is called *recursive* if the body of the function calls the function itself, either directly or indirectly. That is, the process of executing the body of a recursive function may in turn require applying that function again.

我们知道递归函数即是会调用自身的函数，从结构上我们可以把递归函数分为以下几个区块：

### Base cases

> A conditional statement that defines the behavior of the function for the inputs that are simplest to process. 

函数体应当以基线条件（Base cases）作为开头，其作为一系列条件语句（conditional statement），所谓的 simplest input process 即是处理最简单的函数执行情况，通常为一些特殊情况和终止递归的条件。



### Recursive calls

递归调用（Recursive calls）置于 base case 之后，这一部分即用于再次 / 多次调用函数自身

