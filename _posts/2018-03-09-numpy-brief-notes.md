---
title:  "Numpy简单笔记"
excerpt: "很久没写代码了，而且我python本来就写得很少……今天对着numpy教程找了找手感，记录一下试过的代码。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Language
tags:
  - 库
---

题外话：我写py都用linux，装库简单，而且py2/py3也能方便地分开用，不用折腾系统变量。
之前装双系统把引导区搞坏了，不敢再折腾，现在linux装在一块移动硬盘上，速度其实还行。

```python
# numpy reference: https://docs.scipy.org/doc/numpy/reference/

import numpy as np
a = np.array([[1,2,3], [4,5,6]])
for i in range(a.shape[0]):
     for j in range(a.shape[1]):
         print(a[i][j])

a = np.zeros((2,2))
b = np.ones((1,2))
c = np.full((2,2), 7)
d = np.eye(2)
e = np.random.random((2,2))


# A slice of an array is a view into the same data, so modifying it
# will modify the original array.

a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

print(a[0, 1])   # Prints "2"
b[0, 0] = 77     # b[0, 0] is the same piece of data as a[0, 1]
print(a[0, 1])   # Prints "77"

# Two ways of accessing the data in the middle row of the array.
# Mixing integer indexing with slices yields an array of lower rank,
# while using only slices yields an array of the same rank as the
# original array:
row_r1 = a[1, :]    # Rank 1 view of the second row of a
row_r2 = a[1:2, :]  # Rank 2 view of the second row of a
print(row_r1, row_r1.shape)  # Prints "[5 6 7 8] (4,)"
print(row_r2, row_r2.shape)  # Prints "[[5 6 7 8]] (1, 4)"

# Advanced indexing always returns a copy of the data (contrast 
# with basic slicing that returns a view).
a = np.array([[1,2], [3, 4], [5, 6]])

# An example of integer array indexing.
# The returned array will have shape (3,) and
print(a[[0, 1, 2], [0, 1, 0]])  # Prints "[1 4 5]"
# chosen row index:[0, 1, 2], chosen column index:[0, 1, 0]


# The above example of integer array indexing is equivalent to this:
print(np.array([a[0, 0], a[1, 1], a[2, 0]]))  # Prints "[1 4 5]"

# When using integer array indexing, you can reuse the same
# element from the source array:
print(a[[0, 0], [1, 1]])  # Prints "[2 2]"

# Equivalent to the previous integer array indexing example
print(np.array([a[0, 1], a[0, 1]]))  # Prints "[2 2]"

# Create an array of indices
b = np.array([0, 2, 0, 1])

# Select one element from each row of a using the indices in b
print(a[np.arange(4), b])  # Prints "[ 1  6  7 11]"

a = np.array([[1,2], [3, 4], [5, 6]])

bool_idx = (a > 2)   # Find the elements of a that are bigger than 2;
                     # this returns a numpy array of Booleans of the same
                     # shape as a, where each slot of bool_idx tells
                     # whether that element of a is > 2.

print(bool_idx)      # Prints "[[False False]
                     #          [ True  True]
                     #          [ True  True]]"

>>> a = np.array([1,2])
>>> print(a.dtype)
int64
>>> a = np.array(a, dtype = np.float64)
>>> print(a.dtype)
float64

x = np.eye(2)
y = np.eye(2)
# can use + - * /
print(x * y) # elementwise product
print(np.sqrt(x)) # x
print(x.dot(y)) # inner product
print(np.sum(x))  # Compute sum of all elements; prints "10"
print(np.sum(x, axis=0))  # Compute sum of each column; prints "[4 6]"
print(np.sum(x, axis=1))  # Compute sum of each row; prints "[3 7]"
print(x.T)  # Prints "[[1 3]
            #          [2 4]]"

# We will add the vector v to each row of the matrix x,
# storing the result in the matrix y
x = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])
v = np.array([1, 0, 1])
y = np.empty_like(x)   # Create an empty matrix with the same shape as x

# Add the vector v to each row of the matrix x with an explicit loop
for i in range(4):
    y[i, :] = x[i, :] + v

# Now y is the following
# [[ 2  2  4]
#  [ 5  5  7]
#  [ 8  8 10]
#  [11 11 13]]
print(y)

# 让所有输入数组都向其中shape最长的数组看齐，shape中不足的部分都通过在前面加
# 1补齐
# 输出数组的shape是输入数组shape的各个轴上的最大值
# 如果输入数组的某个轴和输出数组的对应轴的长度相同或者其长度为1时，这个数组能
# 够用来计算，否则出错
# 当输入数组的某个轴的长度为1时，沿着此轴运算时都用此轴上的第一组值
# https://www.ctolib.com/docs/sfile/ts-numpy-tut/10.html

```