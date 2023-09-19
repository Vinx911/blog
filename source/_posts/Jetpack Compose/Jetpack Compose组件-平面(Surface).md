---
title: Jetpack Compose组件-平面(Surface)
date: 2023-01-13 09:53:00
id: 70
tags: Surface
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-平面(Surface)

## 平面(Surface)

将很多的组件摆放在这个平面之上，我们可以设置这个平面的边框，圆角，颜色等等。

Surface实际上是Box的重新包装。

```kotlin
@Composable
fun Surface(
    // 修饰符
    modifier: Modifier = Modifier,
    // 形状
    shape: Shape = RectangleShape,
    // 颜色
    color: Color = MaterialTheme.colors.surface,
    // 内容颜色
    contentColor: Color = contentColorFor(color),
    // 边框
    border: BorderStroke? = null,
    // 高度(阴影)
    elevation: Dp = 0.dp,
    content: @Composable () -> Unit
): Unit

@ExperimentalMaterialApi
@Composable
fun Surface(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 点击使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 其余参数参照上方第一个
): Unit

@ExperimentalMaterialApi
@Composable
fun Surface(
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    // 选中使能
    enabled: Boolean = true,
    // 选中状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 其余参数参照上方第一个
): Unit

@ExperimentalMaterialApi
@Composable
fun Surface(
    // 点击回调
    onClick: () -> Unit,
    // 点击使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 其余参数参照上方第一个
): Unit
```



