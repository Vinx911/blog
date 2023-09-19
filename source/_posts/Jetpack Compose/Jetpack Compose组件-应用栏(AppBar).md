---
title: Jetpack Compose组件-应用栏(AppBar)
date: 2023-01-15 09:21:16
id: 78
tags: AppBar
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-应用栏(AppBar)

## 顶部应用栏(TopAppBar)

Chip(小标签)类似于淘宝的历史搜索标签。

```kotlin
@Composable
fun TopAppBar(
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 高度(阴影)
    elevation: Dp = AppBarDefaults.TopAppBarElevation,
    // 内容边距
    contentPadding: PaddingValues = AppBarDefaults.ContentPadding,
    content: @Composable RowScope.() -> Unit
): Unit

@Composable
fun TopAppBar(
    // 标题
    title: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 导航图标
    navigationIcon: (@Composable () -> Unit)? = null,
    // 右侧动作按钮
    actions: @Composable RowScope.() -> Unit = {},
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 高度(阴影)
    elevation: Dp = AppBarDefaults.TopAppBarElevation
): Unit
```

```kotlin
TopAppBar(
    title = { Text("Simple TopAppBar") },
    navigationIcon = {
        IconButton(onClick = { /* doSomething() */ }) {
            Icon(Icons.Filled.Menu, contentDescription = null)
        }
    },
    actions = {
        // RowScope here, so these icons will be placed horizontally
        IconButton(onClick = { /* doSomething() */ }) {
            Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
        }
        IconButton(onClick = { /* doSomething() */ }) {
            Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
        }
    }
)
```



## 底部应用栏(BottomAppBar)

```kotlin
@Composable
fun BottomAppBar(
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 底部应用栏剪切形状, 在Scaffold中使用时，可将浮动按钮嵌入应用栏
    cutoutShape: Shape? = null,
    // 高度(阴影)
    elevation: Dp = AppBarDefaults.BottomAppBarElevation,
    // 内容边距
    contentPadding: PaddingValues = AppBarDefaults.ContentPadding,
    content: @Composable RowScope.() -> Unit
): Unit
```

```kotlin
BottomAppBar(cutoutShape = CircleShape) {
    // Leading icons should typically have a high content alpha
    CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.high) {
        IconButton(onClick = { /* doSomething() */ }) {
            Icon(Icons.Filled.Menu, contentDescription = "Localized description")
        }
    }
    // The actions should be at the end of the BottomAppBar. They use the default medium
    // content alpha provided by BottomAppBar
    Spacer(Modifier.weight(1f, true))
    IconButton(onClick = { /* doSomething() */ }) {
        Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
    }
    IconButton(onClick = { /* doSomething() */ }) {
        Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
    }
}
```

