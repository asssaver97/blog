---
title: 【读书笔记】《Effective Java》
date: 2023-01-19 14:43:43
tags: Java
categories: 读书笔记
---

<!--more-->

## 用静态工厂方法代替构造器

类可以提供一个公有的*静态工厂方法*（static factory method），它只是一个返回类的实例的静态方法。

提供静态工厂方法而不是公有的构造器，主要有以下优势：

1. **静态工厂方法有名字。**
