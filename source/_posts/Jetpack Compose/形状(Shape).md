---
title: 形状(Shape)
date: 2023-01-03 09:56:55
tags: Shape
categories:  Jetpack Compose
copyright: false
---

# 形状(Shape)

```kotlin
// 切角/圆角形状基类
abstract class CornerBasedShape : Shape
// 绝对切角形状
class AbsoluteCutCornerShape : CornerBasedShape
// 绝对圆角形状
class AbsoluteRoundedCornerShape : CornerBasedShape
// 切角形状
class CutCornerShape : CornerBasedShape
// 圆角形状
class RoundedCornerShape : CornerBasedShape

// 圆形
val CircleShape: RoundedCornerShape
// 常规形状, 需要自定义形状路径
class GenericShape : Shape
```

- 四个角相同大小
  - CornerSize：以像素为单位定义角的大小
  - size: Dp：dp
  - size: Float：像素
  - percent: Int：百分比
- 四个角不同大小：分别指定四个角的dp/像素/百分比



