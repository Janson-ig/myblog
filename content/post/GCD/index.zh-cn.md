---
title: GCD算法（最大公因数算法）
description: 代码
date: 2023-02-01
slug: GCD
image: 1.jpg
categories:
- 算法
- 学习笔记
tags:
- 算法
---

## 欧几里得算法（辗转相除法）
### 递归实现
``` java
private int gcd(int x, int y){
        if(y == 0) return x;

        return gcd(y, x % y);
    }
```
### 迭代实现
```java
public int gcd(int x, int y) {
        while(y != 0) {
            int tmp = x;
            x = y;
            y = tmp % y;
        }
        return a;
}
```
### 例题
[[easy]1071. Greatest Common Divisor of Strings](https://leetcode.com/problems/greatest-common-divisor-of-strings/description/ "1071. Greatest Common Divisor of Strings")


> 如有遗漏或错误，欢迎补充纠正