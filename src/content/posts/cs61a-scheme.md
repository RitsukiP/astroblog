---
title: CS61A Scheme Review
published: 2025-01-05
description: 本文为笔者在 CS61A 中学习 Scheme 的笔记
image: ''
tags: [CS61A]
category: 笔记
draft: true
---

## Fundamentals

Scheme programs consist of expressions, which can be:

- Primitive expressions / 原始表达式
- Combinations / 组合

Numbers are self-evaluating; symbols are bound to values. 数字是自我评估的；符号与数值相绑定。

Call expressions include an operator and `0` or more operands in parentheses.

函数（Functions）在 Scheme 中被称为 ***Procedure***



例子：

```scheme
> 2
2
> (+ 1 2 3 4)
10
> (* 1 2 3 4)
24
> (number? 3)
#t
```

### Interpreters 解释器

参考 [HW07: Scheme](https://cs61a.org/hw/hw07/)，其中用 Python 构建一个 Scheme 的解释器



### Special Forms 特殊形式

A combination that is **not a call expression** is a *special form* / 不是调用表达式的组合是*特殊形式*：

- `if` expression: `(if <predicate> <consequent> <alternative>)`

过程：1. 评估 `<predicate>`，2. 评估 `<consequent>` 或 `<alternative>` 二者之一

- `and` 和 `or`: `(and <e1> ... <en>)` `(or <e1> ... <en>)`
- Binding symbols: `(define <symbol> <expression>)`
- New procedures: `(define (<symbol> <formal parameters>) <body>)`

**更多的特殊形式：Cond & Begin**

`cond` 特殊形式和 Python 中的 `if-elif-else` 类似:

```python
if x > 10:
    print('big')
elif x > 5:
    print('medium')
else:
    print('small')
```

```scheme
(cond ((> x 10) (print 'big))
      ((> x 5) (print 'medium))
      (else (print 'small)))
; An equivalent form
(print
 (cond ((> x 10) 'big)
       ((> x 5) 'medium)
       (else 'small)))
```



`begin` 特殊形式将多个表达式合为一个表达式：

示例：

```python
if x > 10:
    print('big')
    print('guy')
else:
    print('small')
    print('fuy')
```

```scheme
(cond ((> x 10) (begin (print 'big) (print 'guy)))
      (else (begin (print 'small) (print 'fry))))
; A natural form
(if (> x 10) (begin
              (print 'big)
              (print 'guy))
    (begin
     (print 'small)
     (print 'fry)))
```



###  Lambda Expressions

Lambda 表达式用于执行匿名的 procedure：`(lambda (<formal-parameters>) <body>)`

示例——两个等价的表达式：

```scheme
(define (plus4 x) (+ x 4))
(define plus4 (lambda(x) (+ x 4)))
```

An operator can be a call expression too  / 一个运算符也可以成为一个调用表达式：

```scheme
((lambda (x y z) (+ x y (square z))) 1 2 3)
```

## Scheme Lists

