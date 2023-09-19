---
title: Jetpack Compose组件-分割线(Divider)
date: 2023-01-13 09:20:19
id: 67
tags: Divider
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-分割线(Divider)

## 分割线(Divider)

`Divider`是将列表和布局中的内容分组的细线。

```kotlin
@Composable
fun Divider(
    // 修饰符
    modifier: Modifier = Modifier,
    // 分割线颜色
    color: Color = MaterialTheme.colors.onSurface.copy(alpha = DividerAlpha),
    // 分割线粗细
    thickness: Dp = 1.dp,
    // 分割线起始偏移
    startIndent: Dp = 0.dp
): Unit
```

```kotlin
Column {
    Text("Foo")
    Divider(startIndent = 8.dp, thickness = 1.dp, color = Color.Black)
    Text("Bar")
}
```

