---
title: Jetpack Compose组件-导航栏(Navigation)
date: 2023-01-15 09:48:11
tags: Navigation
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-导航栏(Navigation)

## 底部导航栏(BottomNavigation )

BottomNavigation 为水平方向的导航栏。

```kotlin
@Composable
fun BottomNavigation(
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 高度(阴影)
    elevation: Dp = BottomNavigationDefaults.Elevation,
    content: @Composable RowScope.() -> Unit
): Unit

@Composable
fun RowScope.BottomNavigationItem(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 导航按钮图标
    icon: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 导航按钮使能
    enabled: Boolean = true,
    // 导航按钮文字
    label: (@Composable () -> Unit)? = null,
    // 总是显示导航按钮文字
    alwaysShowLabel: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 选中颜色
    selectedContentColor: Color = LocalContentColor.current,
    // 未选中颜色
    unselectedContentColor: Color = selectedContentColor.copy(alpha = ContentAlpha.medium)
): Unit
```

```kotlin
var selectedItem by remember { mutableStateOf(0) }
val items = listOf("Songs", "Artists", "Playlists")

BottomNavigation {
    items.forEachIndexed { index, item ->
        BottomNavigationItem(
            icon = { Icon(Icons.Filled.Favorite, contentDescription = null) },
            label = { Text(item) },
            selected = selectedItem == index,
            onClick = { selectedItem = index }
        )
    }
}
```



## 侧边导航栏(NavigationRail)

NavigationRail为垂直方向的导航栏。

```kotlin
@Composable
fun NavigationRail(
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.surface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 高度(阴影)
    elevation: Dp = NavigationRailDefaults.Elevation,
    // 自定义头部
    header: (@Composable ColumnScope.() -> Unit)? = null,
    content: @Composable ColumnScope.() -> Unit
): Unit

@Composable
fun NavigationRailItem(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 导航按钮图标
    icon: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 导航按钮使能
    enabled: Boolean = true,
    // 导航按钮文字
    label: (@Composable () -> Unit)? = null,
    // 总是显示导航按钮文字
    alwaysShowLabel: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 选中颜色
    selectedContentColor: Color = MaterialTheme.colors.primary,
    // 未选中颜色
    unselectedContentColor: Color = LocalContentColor.current.copy(alpha = ContentAlpha.medium)
): Unit
```

```kotlin
var selectedItem by remember { mutableStateOf(0) }
val items = listOf("Home", "Search", "Settings")
val icons = listOf(Icons.Filled.Home, Icons.Filled.Search, Icons.Filled.Settings)
NavigationRail {
    items.forEachIndexed { index, item ->
        NavigationRailItem(
            icon = { Icon(icons[index], contentDescription = item) },
            label = { Text(item) },
            selected = selectedItem == index,
            onClick = { selectedItem = index }
        )
    }
}
```

