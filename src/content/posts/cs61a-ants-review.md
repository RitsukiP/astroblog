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
    interest = 0.2			# This is a CA
    def __init__(self, account_holder):
        self.balance = 0	# balance is the IA of self(an Instance)
        self.holder = account_holder
        
        # Additional methods would be defined here
```

其中 `def __init__()` 为实例化一个 object 之时执行的初始化 method，其中的操作都是对实例化后的 object 之属性进行的，即操作对象为 IA。

```python
>>> tom_account = Account("Tom")
>>> jim_account = Account("Jim")
>>> tom_account.interest	# Use dot exp to access CA
0.02
>>> jim_account.interest	# Access the same one, so get same result
0.02
```

以上读取的 `.interest` 为 CA，所有该类的实例数值均相同（若不被更改）。

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



## Problem 4

A `ThrowerAnt` is a powerful threat to the bees, but it has a high food cost. In this problem, you'll implement two subclasses of `ThrowerAnt` that are less costly but have constraints on the distance they can throw:

- The `LongThrower` can only `throw_at` a `Bee` that is found after following at least 5 `entrance` transitions. It cannot hit `Bee`s that are in the same `Place` as it or the first 4 `Place`s in front of it. If there are two `Bee`s, one too close to the `LongThrower` and the other within its range, the `LongThrower` should only throw at the farther `Bee`, which is within its range, instead of trying to hit the closer `Bee`.
- The `ShortThrower` can only `throw_at` a `Bee` that is found after following at most 3 `entrance` transitions. It cannot throw at any bees further than 3 `Place`s in front of it.

Neither of these specialized throwers can `throw_at` a `Bee` that is exactly 4 `Place`s away.

|     Class      | Food Cost | Initial Health |
| :------------: | :-------: | :------------: |
| `ShortThrower` |     2     |       1        |
| `LongThrower`  |     2     |       1        |

To implement these new throwing ants, your `ShortThrower` and `LongThrower` classes should inherit the `nearest_bee` method from the base `ThrowerAnt` class. The logic of choosing which bee a thrower ant will attack is the same, except the `ShortThrower` and `LongThrower` ants where their range is limited by a lower and upper bound, respectively.

To do this, modify the `nearest_bee` method to reference `lower_bound` and `upper_bound` attributes, and only return a bee if it is within range.

Make sure to give these `lower_bound` and `upper_bound` attributes appropriate values in the `ThrowerAnt` class so that the behavior of `ThrowerAnt` is unchanged. Then, implement the subclasses `LongThrower` and `ShortThrower` with appropriately constrained ranges.

You should **not** need to repeat any code between `ThrowerAnt`, `ShortThrower`, and `LongThrower`.

> *Hint:* `float('inf')` returns an infinite positive value represented as a float that can be compared with other numbers.

> *Hint:* `lower_bound` and `upper_bound` should mark an inclusive range.

> **Important:** Make sure your class attributes are called `upper_bound` and `lower_bound` The tests directly reference these attribute names, and will error if you use another name for these attributes.

本题和 Problem 3 为联动题目，基于已经完成 `ThrowerAnt` 类的情况下，构建 `ShortThrower` 和 `LongThrower` 两个继承自 `ThrowerAnt` 的类，它们的 Food Cost 和 Initial Health 不同。以及这两种 `ThrowerAnt` 的投射范围不同，其中 `ShortThrower` 最多可以攻击到前方三个 place 内的蜜蜂，而 `LongThrower` 最短可以攻击到前方第 5 个 place 的蜜蜂。

投射范围可视化：

|         0          |  1   |  2   |  3   |  4   |  5   |  6   | Infinite |
| :----------------: | :--: | :--: | :--: | :--: | :--: | :--: | :------: |
| `ShortThrower` / O |  O   |  O   |  O   |  X   |  X   |  X   |    X     |
| `LongThrower` / X  |  X   |  X   |  X   |  X   |  O   |  O   |    O     |

题目要求引入 `lower_bound` 和 `upper_bound` 分别作为下限和上限的变量名，为应用 OOP 中继承的特性，我们应该修改其父类 `ThrowerAnt` 中的 `nearest_bee` 方法而不是分别在两个类中构建自己的锁定目标方法。

### Problem 3 Recap

构建 `ThrowerAnt` 这一继承自 `Ant` 类的蚂蚁，使用 `nearest_bee(slef)` method 取得离自身最近且不是 `Hive` 的蜜蜂之 `place`。其中的 `place`并不是可以被 index 的列表，需要使用 OOP 的方法来实现位移定位。

已知一个 `place` 具有 `.exit`和 `.entrance` 的属性，而蜜蜂移动的方向是从 entrance 向 exit 移动，所以我们用 `pointer.palce = pointer.entrance` 来移动判定区域，其中 `self.place`不可被改变，因为蚂蚁自身不移动。

```python
class ThrowerAnt(Ant):
    """Original Problem 3 code by myself
    """
    name = 'Thrower'
    implemented = True
    damage = 1
    # ADD/OVERRIDE CLASS ATTRIBUTES HERE
    food_cost = 3
    health = 1

    def nearest_bee(self):
        pointer = self.place            # initialize location
        while not pointer.is_hive:      # target place is not hive
            if pointer.bees:            # if aimmed place has bees
                return random_bee(pointer.bees)
            pointer = pointer.entrance  # move pointer to next Place
        return None                     # No bee to attack
```

Process review: 做题的时候忘记用给出的 `random_bee()` 方法来 return 所在地的蜜蜂，导致初期程序失败，应当留意题目中给出的方法而不是自造车轮。

### Solution

```python
class ThrowerAnt(Ant):
    """ThrowerAnt throws a leaf each turn at the nearest Bee in its range."""

    name = 'Thrower'
    implemented = True
    damage = 1
    # ADD/OVERRIDE CLASS ATTRIBUTES HERE
    food_cost = 3
    health = 1
    lower_bound = 0
    upper_bound = float('inf')

    def nearest_bee(self):
        # BEGIN Problem 3 and 4
        position = self.place
        step_count = 0
        while not position.is_hive \
            and position.entrance is not None:         # proceed until reach hive
            if step_count > self.upper_bound:
                return None
            if len(position.bees) > 0 and \
                step_count >= self.lower_bound:
                return random_bee(position.bees)
            position = position.entrance
            step_count += 1
        return None
        # END Problem 3 and 4
```

```python
class ShortThrower(ThrowerAnt):
    """A ThrowerAnt that only throws leaves at Bees at most 3 places away."""

    name = 'Short'
    food_cost = 2
    # OVERRIDE CLASS ATTRIBUTES HERE
    upper_bound = 3
    # BEGIN Problem 4
    implemented = True   # Change to True to view in the GUI
    # END Problem 4


class LongThrower(ThrowerAnt):
    """A ThrowerAnt that only throws leaves at Bees at least 5 places away."""

    name = 'Long'
    food_cost = 2
    # OVERRIDE CLASS ATTRIBUTES HERE
    lower_bound = 5
    # BEGIN Problem 4
    implemented = True   # Change to True to view in the GUI
    # END Problem 4
```

在 `ThrowerAnt`中加入范围限制的设计，引入步长变量 `step_count` 并初始化为 0，这一变量用于跟踪瞄准距离的变化。

瞄准并位移的工作条件为该位置不是蜂巢且此位置拥有相对位置 entrance；再看这个循环的 base case，如果步长超出上限即表明找不到蜜蜂，应当 `return None`。

返回所瞄准位置的蜜蜂的条件是此地有蜜蜂：`len(position.bees) > 0`以及步长大于或等于下限：`step_count >= self.lower_bound`。综上我们可以完成 `ThrowerAnt` 的代码重构。

对于两个继承自它的子类，只需要修改其固定属性并确定上限和下限即可。

## Problem 5

