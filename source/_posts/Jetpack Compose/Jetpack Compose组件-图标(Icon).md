---
title: Jetpack Compose组件-图标(Icon)
date: 2023-01-12 11:15:30
tags: Icon
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-图标(Icon)

## 图标(Icon)

```kotlin
@Composable
@NonRestartableComposable
fun Icon(
    // 位图对象，可以显示 JPG，PNG 等格式的图标
    bitmap: ImageBitmap,
    // 无障碍功能, 图标描述信息
    contentDescription: String?,    
    // 修饰符
    modifier: Modifier = Modifier, 
    // 设置图标颜色
    tint: Color = LocalContentColor.current.copy(alpha = LocalContentAlpha.current)
): Unit

@Composable
fun Icon(
    // 画笔，可以使用画笔在 Canvas 上直接绘制图标，可以通过 res/ 下的图片资源来设置图标
    painter: Painter,
    // 无障碍功能, 图标描述信息
    contentDescription: String?,  
    // 修饰符
    modifier: Modifier = Modifier, 
    // 设置图标颜色
    tint: Color = LocalContentColor.current.copy(alpha = LocalContentAlpha.current)
): Unit

@Composable
@NonRestartableComposable
fun Icon(
    // 矢量图对象，可以显示 SVG 格式的图标
    imageVector: ImageVector,
    // 无障碍功能, 图标描述信息
    contentDescription: String?,  
    // 修饰符
    modifier: Modifier = Modifier,  
    // 设置图标颜色
    tint: Color = LocalContentColor.current.copy(alpha = LocalContentAlpha.current)
): Unit
```

```kotlin
Icon(imageVector = ImageVector.vectorResource(
    id = R.drawable.ic_svg, contentDescription = "矢量图资源")
  
Icon(bitmap = ImageBitmap.imageResource(
    id = R.drawable.ic_png), contentDescription = "图片资源")
    
Icon(painter = painterResource(
    id = R.drawable.ic_both), contentDescription = "任意类型资源")
     
Icon(imageVector = Icons.Default.Deck, contentDescription = null, tint = Color.Red)
```

