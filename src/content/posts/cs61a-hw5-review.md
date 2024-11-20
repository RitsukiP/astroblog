---
title: CS61A Homework 5 Review
published: 2024-11-20
description: CS61A Homework 5 答案以及自我解析
tags: [CS61A]
category: 笔记
---

本文基于 [CS61A](https://cs61a.org) 2024 Fall 的 [Homework 5](https://cs61a.org/hw/hw05/) 而记。

## Q1 Infinite Hailstone

Write a generator function that yields the elements of the hailstone sequence starting at number `n`. After reaching the end of the hailstone sequence, the generator should yield the value 1 indefinitely.

Here is a quick reminder of how the hailstone sequence is defined:

1. Pick a positive integer `n` as the start.
2. If `n` is even, divide it by 2.
3. If `n` is odd, multiply it by 3 and add 1.
4. Continue this process until `n` is 1.

程序结果示例：

```
>>> hail_gen = hailstone(10)
>>> [next(hail_gen) for _ in range(10)]
[10, 5, 16, 8, 4, 2, 1, 1, 1, 1]
>>> next(hail_gen)
1
```

Hailstone 对于输入参数 `n` 的处理是如遇偶数就除以 2 ，遇到奇数就乘以 3 再加 1 ，遇到 1 则持续输出 1 ；生成器控制输出成一个 list 。

Solution :

```python
def hailstone(n):
    yield n
    if n == 1:
        yield from hailstone(1)
    elif n % 2 == 0:	# n is even
        yield from hailstone(n // 2)
    else:				# n is odd
        yield from hailstone(3 * n + 1)
```

`hailstone(n)` 是一个递归的生成器。首先，无论什么情况下每一次调用本函数，都应当「输出」一次传入的参数 `n` ，所以生成器要做的第一步就是 `yield n` 。

本题中基线条件即当传入参数 `n` 等于 1 时，之后的调用只会控制输出 1 ，我们可以得到以下操作：

```python
for x in hailstone(1):
    yield x
```

回顾 `for` 语句的格式：

```python
for <name> in <expression>:
    <suite>
```

> To execute a `for` statement, Python evaluates the header `<expression>`, which must yield an iterrable value. Then, the `__iter__` method is invoked on that value. Until a `StopIteration` exception is raised, Python repeatedly invokes the `__next__` method on that iterator and binds the result to the `<name>` in the `for` statement. Then, it executes the `<suite>`.

首先 Python 检查 `<expression>` 是否为可迭代的东西，显然 `hailstone(1)` 作为 Generator 是可迭代的，但由于 Iterator / Generator 的 Lazy Computation 属性，我们还不知道 `hailstone(1)` 是什么，Python 就会先评估（执行）生成器控制程序。分析得到 `yield 1` ，即输出 1 ，所以每次评估 `hailstone(1)` 都会 `yield 1` 。以上部分又可以简化为 `yield from hailstone(1)` 。

剩下的情况为传入参数 `n` 为奇数或偶数。同理，下面使用 `yield from` 语句时 Python 会分别评估 `hailstone(n // 2)` 和 `hailstone(3 * n + 1)` 的内容，即执行 `yield (n // 2)` 和 `yield (3 * n + 1)` 。



## Q2 Merge

Definition: An *infinite iterator* is a iterator that never stops providing values when `next` is called. For example, `ones()` evaluates to an infinite iterator:

```
def ones():
    while True:
        yield 1
```

Write a generator function `merge(a, b)` that takes two infinite iterators, `a` and `b`, as inputs. Both iterators yield elements in strictly increasing order with no duplicates. The generator should produce all unique elements from both input iterators in increasing order, ensuring no duplicates.

如题，`merge(a, b)` 将 `a` 和 `b` 两个特殊的可迭代序列中的所有元素从小到大不重复地输出。

Solution :

```python
def merge(a, b):
    a_val, b_val = next(a), next(b)
    while True:
        if a_val == b_val:
            "*** YOUR CODE HERE ***"
            yield a_val
            a_val, b_val = next(a), next(b)
        elif a_val < b_val:
            "*** YOUR CODE HERE ***"
            yield a_val
            a_val = next(a)
        else:
            "*** YOUR CODE HERE ***"
            yield b_val
            b_val = next(b)
```

分析题目已经给出的代码部分，局部变量 `a_val` 和 `b_val` 即分别从两个序列中抓取的数值，`while True` 下面的部分即收到迭代指示时永续执行。

本题代码设计的关键之处在于每次都要判断两个抓取值的大小来控制输出，在 `yield` 控制输出后抓取下一个值。当二者值相等时，输出任意一个（这里就输出 `a_val`）后二者同时抓取下一个值。



## Q3 Stair Ways

Imagine that you want to go up a staircase that has `n` steps, where `n` is a positive integer. You can take either **one** or **two steps** each time you move.

Write a generator function `stair_ways` that yields all the different ways you can climb the staircase.

Each "way" of climbing a staircase can be represented by a list of 1s and 2s, where each number indicates whether you take one step or two steps at a time.

For example, for a staircase with 3 steps, there are three ways to climb it:

- You can take one step each time: `[1, 1, 1]`.
- You can take two steps then one step: `[2, 1]`.
- You can take one step then two steps: `[1, 2].`.

Therefore, `stair_ways(3)` should yield `[1, 1, 1]`, `[2, 1]`, and `[1, 2]`. These can be yielded in any order.

输入参数 `n` 为总阶梯数，一次可以上 1 阶或 2 阶，要求输出所有的爬楼可能安排。每一个安排都是一个 list，本题的结果应当是 `stair_ways(n)` 生成器输出一系列 lists 。

Solution :

```python
def stair_ways(n):
    # base cases
    if n == 0:
        yield []
    elif n == 1:
        yield [1]
    else:			# recursive case
        for way in stair_ways(n - 1):
            yield [1] + way
        for way in stair_ways(n - 2):
            yield [2] + way
```

先看基线条件，当总阶梯数为 0 时直接输出空列表；当总阶梯数为 1 时有且仅有一种爬楼方法，即爬一次，一次 1 阶。

再来看递归情况（Recursive case），回顾列表的性质：List concatenation

```
>>> [1, 2, 3] + [4, 5, 6]
[1, 2, 3, 4, 5, 6]
```

使用 `+` 让两个列表合二为一，这是

### Example Walkthrough

For `stair_ways(3)`:

1. We start with `n = 3`. First, take a `1`, leaving `n = 2`.
   - Now for `n = 2`, we take a `1`, leaving `n = 1`, and yield `[1, 1]`.
   - Then, for `n = 2`, we take a `2`, leaving `n = 0`, and yield `[2]`.
   - Thus, for `n = 3`, we yield `[1, 1, 1]` and `[1, 2]`.
2. Then, back to the original call for `n = 3`, we now try taking a `2`:
   - For `n = 1`, we take a `1`, yielding `[2, 1]`.

对于 `stiar_ways(3)`，他将 `yield [1] + way`。其中这里的 `way` 就是 `stair_ways(2).__next()`。

然后我们来讨论 `stair_ways(2).__next()`。第一步，它 `yield [1] + way`，其中这里的 `way` 是 `stair_ways(1)` 即输出 `[1]`，第一步先输出 `[1] + [1]` 即 `[1, 1]`；第二步，它 `yield [2] + way`，这里的 `way` 为 `stair_ways(0).__next__()` 即为 `[]`。加起来就是输出 `[2]`。

再往上代入，即在 `[1]` 后连接列表，就知道程序将输出 `[1, 1, 1], [1, 2]` 。



## Q4 Yield Paths

Write a generator function `yield_paths` that takes a tree `t` and a target `value`. It yields each path from the root of `t` to any node with the label `value`.

Each path should be returned as a list of labels from the root to the matching node. The paths can be yielded in any order.

程序结果示例：

```python
>>> t1 = tree(1, [tree(2, [tree(3), tree(4, [tree(6)]), tree(5)]), tree(5)])
>>> print_tree(t1)
1
  2
    3
    4
      6
    5
  5
>>> next(yield_paths(t1, 6))
[1, 2, 4, 6]
>>> path_to_5 = yield_paths(t1, 5)
>>> sorted(list(path_to_5))
[[1, 2, 5], [1, 5]]

>>> t2 = tree(0, [tree(2, [t1])])
>>> print_tree(t2)
0
  2
    1
      2
        3
        4
          6
        5
      5
>>> path_to_2 = yield_paths(t2, 2)
>>> sorted(list(path_to_2))
[[0, 2], [0, 2, 1, 2]]
```

以上我们可以分析得出，`yield_path` 返回的是一系列 lists，其中每一个 list 是从顶部节点到 `value` 的路径。

Solution:

```python
def yield_paths(t, value):
    if label(t) == value:
        "*** YOUR CODE HERE ***"
        yield [value]
	for b in branches(t):
        "*** YOUR CODE HERE ***"
        for path in yield_paths(b, value):
            yield [label(t)] + path
```

官方答案解释：

> If our current label is equal to `value`, we've found a path from the root to a node containing `value` containing only our current label, so we should yield that. From there, we'll see if there are any paths starting from one of our branches that ends at a node containing `value`. If we find these "partial paths" we can simply add our current label to the beginning of a path to obtain a path starting from the root.
>
> In order to do this, we'll create a generator for each of the branches which yields these "partial paths". By calling `yield_paths` on each of the branches, we'll create exactly this generator! Then, since a generator is also an iterator, we can iterate over the paths in this generator and yield the result of concatenating it with our current label.

题目中有一个预判定条件 `if label(t) == value:`，整个函数体本身是一个 recursive case。

先讨论预判定条件我们很容易得出此时应当 `yield [value(t)]` 或者 `yield [label(t)]`，这种情况出现在在某一个分叉中找到目标节点。



再来看 recursive case（即主函数体），所有代码都是工作于在每个传入树的分支中，本题的目的是要在每个分支中寻找目标节点并记录路径（path），于是我们应该在每个分支中调用 `yield_paths` 来执行这一任务，所以得出 `for path in yield_paths(b, value)`，因为 `yield_paths` 是个可迭代的 generator，所以调用后所 yield 出来的内容会成为 `path` 的值，在查找的过程中就需要记录所经过的节点之 label ，所以应当 `yield [label(t)] + path`。



