---
title: Yii应用结构示意图
date: 2017-03-21 17:39:31
categories: yii
---

![应用结构示意图](/img/application-structure.png)

- 在类图使用实心的菱形表示，菱形从局部指向整体。组合(Composition) : 表示contains-a的关系，是一种强烈的包含关系。组合类负责被组合类的生命周期。是一种更强的聚合关系。部分不能脱离整体存在
- 1:1 表示一一映射关系 0..* 表示有0和或者多个

- 入口脚本是整体，负责实例化application，负责application的生命周期。一个application属于且仅属于一个入口脚本