---
title: vscode中ctrl+左键不跳转
description: 
date: 2022-12-15
slug: tips-1
image: 1.png
categories:
    - 踩坑日志
tags:
    - Git
---
&emsp;我一直是ssh远程操作的，之前ctrl+鼠标左键一直好用，可以跳转到对应函数的位置。  
&emsp;但是最近突然不好用了，解决方法很简单：
# vscode里重装C/C++拓展
&emsp;先是尝试了在本地运行一个简单的.c文件，结果提示```找不到类型为“cppdbg”的调试适配器```。  
然后**卸载重装远程C/C++拓展**，问题解决！