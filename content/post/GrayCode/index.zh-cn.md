---
title: 格雷码（Gray code）
description: 格雷码（循环二进制单位距离碼）是任意两个相邻数的代码只有一位二进制数不同的编码，它与奇偶校验码同属可靠性编码。
date: 2023-02-23
slug: graycode
image: 1.jpg
categories:
- 算法
- 学习笔记
tags:
- 算法
---

## 简介 & 原理
&emsp;格雷码（Gray code）是由贝尔实验室的Frank Gray在1940年提出，用于在PCM（脉冲编码调变）方法传送讯号时防止出错。  
&emsp;格雷码是一个数列集合，相邻两数间**只有一个位元**改变，为无权数码，且格雷码的顺序不是唯一的。  
&emsp;原理：
>&emsp;传统的二进位系统，例如数字3的表示法为011，要切换为邻近的数字4，也就是100时，装置中的三个位元都得要转换，因此于未完全转换的过程时装置会经历短暂的，010,001,101,110,111等其中数种状态，也就是代表着2、1、5、6、7，因此此种数字编码方法于邻近数字转换时有比较大的误差可能范围。
格雷码的发明即是用来将误差之可能性缩减至最小，编码的方式定义为每个邻近数字都只相差一个位元，因此也称为最小差异码，可以使装置做数字步进时只更动最少的位元数以提高稳定性。

## 代码实现
&emsp;用`G`代表格雷码：  
&emsp;例如3位数的格雷码序列为：`000`,`001`,`011`,`010`,`110`,`111`,`101`,`100`。`111`可以用`G(4) = 6`来表示。  

&emsp;***公式：***`G(n) = n ^ (n >> 1)`
```
int g (int n) {
    return n ^ (n >> 1);
}
```
### 例题
[[medium]1238. Circular Permutation in Binary Representation](https://leetcode.com/problems/circular-permutation-in-binary-representation/description/ "1238. Circular Permutation in Binary Representation")
> 如有遗漏或错误，欢迎补充纠正