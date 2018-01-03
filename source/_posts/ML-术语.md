---
title: ML 术语
date: 2018-01-02 11:38:29
tags: ML
---

<!-- MarkdownTOC -->

- A
	- accuracy 精度
	- activation function 激活函数
- B
	- Bayesian 贝叶斯
- C
	- cross-entropy 交叉熵
- D
- E
- F
- G
- H
	- hidden layer 隐含层
- I
- J
- K
- L
	- logistic regression 逻辑回归模型
- M
- N
	- neuron 神经元
- O
	- overfitting 过拟合
- P
- Q
- R
	- ridge regression\(weight decay\)
- S
	- [softmax][无对应中文]
- T
- U
- V
- W
- X
- Y
- Z

<!-- /MarkdownTOC -->



# A

## accuracy 精度

### 对分类预测

accuracy = 分类准确个数 / 总测试数目

### 对判断型预测

accuracy = (判断为'是'并预测准确的个数 + 判断为'否'并预测准确的个数) / 总测试数目

## activation function 激活函数

对输入[数组]进行加权求和, 并对其做一定处理后输出到下一层

# B

## Bayesian 贝叶斯


# C

## cross-entropy 交叉熵


# D
# E
# F
# G
# H

## hidden layer 隐含层

除去输入层和输出层, 其间用来对数据进行进一步计算的层

# I
# J
# K
# L

## logistic regression 逻辑回归模型

使用sigmoid, 常用于二元分类判断, 但是也可用于多元分类[multi-class logistic regression / multinomial regression]

# M
# N

## neuron 神经元

神经网络中的一个节点, 使用activation function[激活函数]进行运算

# O

## overfitting 过拟合
对数据进行训练时对数据进行了过于精确的处理, 导致训练出的模型仅对训练数据有效, 不具普适性.

# P
# Q
# R

## ridge regression(weight decay)

# S

## softmax [无对应中文]

将一个数组转换成和为1的概率表示, 但是不严格遵循数字间的比例, 而是通过以下计算:
![math](https://wikimedia.org/api/rest_v1/media/math/render/svg/46c32a5089726d673c30a0abfda7b35ecf0fe3ca)
如一系列事件产生的概率比为 
``` 
[1.0, 2.0, 3.0, 4.0, 1.0, 2.0, 3.0]
```
如直接按比例转换为和为1的概率, 则为:
```
[0.0625, 0.125, 0.1875, 0.25, 0.0625, 0.125, 0.1875]
```
使用softmax计算, 则为:
```
[0.024, 0.064, 0.175, 0.475, 0.024, 0.064, 0.175]
```

# T
# U
# V
# W
# X
# Y
# Z