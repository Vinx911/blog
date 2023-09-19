---
title: Jetpack Compose组件-徽章(Badge)
date: 2023-01-13 09:12:31
id: 66
tags: Badge
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-徽章(Badge)

## 徽章(Badge)

Badge 是一个可以包含动态信息的组件，例如是否存在新通知或许多未处理请求。徽章可以是纯图标或包含短文本。一般不单独使用，使用BadgeBox 将Badge放置在组件的右上角。

```kotlin
@Composable
fun Badge(
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.error,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    content: (@Composable RowScope.() -> Unit)? = null
): Unit
```

```kotlin
var count by remember { mutableStateOf(0) }
Card(onClick = { count++ }) {
    Text("Clickable card content with count: $count")
}
```

## BadgeBox

```kotlin
@Composable
fun BadgedBox(
	// 徽章
    badge: @Composable BoxScope.() -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    content: @Composable BoxScope.() -> Unit
): Unit
```

```kotlin
BadgedBox(badge = {
    Badge {
        val badgeNumber = "8"
        Text(
            badgeNumber,
            modifier = Modifier.semantics {
                contentDescription = "$badgeNumber new notifications"
            }
        )
    }
}) {
    Icon(
        Icons.Filled.Favorite,
        contentDescription = "Favorite"
    )
}
```

