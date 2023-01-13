---
title: Jetpack Compose组件-卡片(Card)
date: 2023-01-13 09:00:44
tags: Card
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-卡片(Card)

## 卡片(Card)

`Card` 是 Compose 中一个布局组件，我们用它可以来创造出一些类似于卡片界面

```kotlin
@Composable
@NonRestartableComposable
fun Card(
    // 修饰符
    modifier: Modifier = Modifier,
    // 卡片形状
    shape: Shape = MaterialTheme.shapes.medium,
    // 卡片背景颜色
    backgroundColor: Color = MaterialTheme.colors.surface,
    // 卡片内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 卡片边框
    border: BorderStroke? = null,
    // 卡片高度(阴影)
    elevation: Dp = 1.dp,
    content: @Composable () -> Unit
): Unit

@ExperimentalMaterialApi
@Composable
@NonRestartableComposable
fun Card(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 卡片点击使能
    enabled: Boolean = true,
    // 卡片形状
    shape: Shape = MaterialTheme.shapes.medium,
    // 卡片背景颜色
    backgroundColor: Color = MaterialTheme.colors.surface,
    // 卡片内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 卡片边框
    border: BorderStroke? = null,
    // 卡片高度(阴影)
    elevation: Dp = 1.dp,
    // 卡片点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    content: @Composable () -> Unit
): Unit
```

```kotlin
var count by remember { mutableStateOf(0) }
Card(onClick = { count++ }) {
    Text("Clickable card content with count: $count")
}
```


