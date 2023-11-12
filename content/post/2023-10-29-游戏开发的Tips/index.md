---
title: 游戏开发的Tips
author: Mingxian Yang
date: 2023-10-29 19:19:00 +0800
description: 对基础知识的掌握比较重要，有时候工作或是切实开发几年，脱离了基础知识，等到参加一些注重基础的面试时，啥也不会了。这里会总结一下游戏工程的基石；开发也中很多小小不言的问题，也会聚沙成塔。
categories: [学习笔记]
tags: [游戏设计]
draft: true
---

## 程序上的技巧

1. List的末尾元素 list[^-1]

    在C#中，list[^-1] 是一种从列表（List）的末尾开始索引的新语法。这种语法是C# 8.0引入的一项特性，称为“范围索引”（range indexing）。

    通过使用 [^-1]，可以方便地访问列表中的最后一个元素。它等效于使用索引 list[list.Count - 1] 来获取列表的最后一个元素。

    以下是一个示例，展示了如何使用 [^-1] 来获取列表的最后一个元素：
    ```csharp
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
        int lastNumber = numbers[^-1];
        Console.WriteLine(lastNumber);  // 输出: 5
    ``` 

    范围索引还支持其他语法，例如使用 [^2] 来获取倒数第二个元素，或使用 [^3..^1] 来获取倒数第三个到倒数第一个元素的范围。

    需要注意的是，范围索引仅适用于支持索引操作的类型，如数组和实现了索引器的集合类型（如 List<T>）。如果尝试在不支持索引操作的类型上使用范围索引，将导致编译错误。

## 游戏中的“欺骗”



 [**作者博客：YMX's Site**](https://yangmingxian.com)  
 [**作者B站视频：CyberStreamer**](https://space.bilibili.com/22212765)




