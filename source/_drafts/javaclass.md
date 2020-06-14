---
title: 了解java反射机制
categories:
  - 笔记
tags:
  - java
  - 反射机制
---
# 介绍 
JAVA反射机制是在运行状态中，对于任何一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java的反射机制。
# 与反射机制相关的类
## Class
### 获取方式
1. 已知具体类  
`Class TestClass = TestClass.class;`  
2. 通过Class.forName()传入类路径
`Class TestClass = Class.forName("com.test.TestClass"); `

## Field
类的成员变量
## Method
类的方法
## Constructor
类的构造方法
# 实例