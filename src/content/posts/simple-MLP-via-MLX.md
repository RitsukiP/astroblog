---
title: 使用 MLX 在 Mac 上搭建一个简单的多层感知机
published: 2026-05-19
description: 介绍一种更适合统一内存体质的机器学习框架
image: https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png
tags: [机器学习, Mac, MLX]
category: 笔记
draft: true
---

学习过机器学习的各位都知道，目前使用得最广泛的机器学习框架是最初由 FaceBook 主导并开源的 [PyTorch](https://pytorch.org) ，以及在 Python 中提供了计算基础的 NumPy，

Apple 在 WWDC25 上发布了一个全新的机器学习框架 MLX ，针对近年来搭载 Apple Sillicone 的 Macintosh 设备之统一内存设计专门优化，

如果你曾经使用过给予 NumPy 和 PyTorch 的机器学习代码，上手 MLX 就是信手拈来的事，如果你手上有一台符合资格的 Mac，那就让我们开始吧！

## 写在前面

### 1. 多层感知机（Multi-Layer Perceptron, MLP）

MLP 是最简单的深度学习类别，

### 2. MLX

## 干什么？

我们使用最经典的 MNIST 数据集构建一个机器学习手写阿拉伯数字识别，

## 神经网络设计

### 1. 参数量的选定

我们有 $N$ 个样本，那么权重数 $N_w$ 应当不多于样本数的十分之一：
$$
N_w \leq \frac{N}{10}
$$
假设输入层参数是 $N_i$，隐藏层参数 $N_h$，和输出层参数 $N_o$，有以下限制条件：
$$
N_w = (N_i + 1) N_h + (N_h + 1) N_o
$$


## 外部资料

再次列出一些可供参考的学习资源供阁下取阅：

[1] Apple MLX [官网](https://mlx-framework.org/)

[2] Artificial Intelligence: A Modern Approach 人工智能：现代方法

[3] 3Blue1Brown, Neural Networks [YouTube Playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)



