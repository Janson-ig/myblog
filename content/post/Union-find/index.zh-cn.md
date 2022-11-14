---
title: 并查集（Union-find）
description: 简单总结并查集概念及其用法
date: 2022-11-14
slug: Union
image: 1.png
categories:
    - 算法
    - 学习笔记
tags:
    - 算法
---
## 什么是并查集？  
> &emsp; 并查集(Disjoint-set data structure)是一种数据结构，用于解决不交集问题的合并及查询问题。它还有几个英文名叫：Union-find data structure / merge–find set(中文就是：合并-查找数据结构)。  
 
 &emsp; 为什么叫[合并-查询数据结构]？因为并查集有三个功能：  
 &emsp; 1. **添加**  
 &emsp; 2. **合并**  
 &emsp; 3. **查询**  

&emsp; 举个例子来说:
>![2-1](2-1.jpg)
![2-2](2-2.jpg)

## 

&emsp;
为例说明查找过程：
> S: **ABC**&ensp;ABCDAB&ensp;ABCDABCDABDE  
> W: **ABC**DABD  

* S[3]（='&ensp;')与W[3]（='D'）不匹配，所以跳过；从S[4]开始，与W[0]进行比较。
> S:ABC&ensp;**ABCDAB**&ensp;ABCDABCDABDE  
> W:**ABCDAB**D
* 从S[4]开始比较，当位于S[10]（='&ensp;'）时，与W对应的字符不匹配；但是此时可以注意到，"**AB**"在“ABCD**AB**"的头尾处均有出现——这意味着，尾部的“AB”可以作为下一次比较的起点。  
* 所以下次从index = 8的位置：
> S:ABC&ensp;ABCD**A**B&ensp;ABCDABCDABDE  
> W:ABCD**A**BD  

* 即**A**开始进行匹配。


## 应用题目实战:

[LeetCode 28. Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/ "LeetCode 28. Find the Index of the First Occurrence in a String")
> 题目实战日后补充说明  

同时推荐一个算法可视化工具：  
[algorithm-visualizer_KMP](https://algorithm-visualizer.org/dynamic-programming/knuth-morris-pratts-string-search "algorithm-visualizer_KMP")



> 如有遗漏或错误，欢迎补充纠正