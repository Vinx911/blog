---
title: Jetpack Compose组件-标签页(Tab)
date: 2023-01-14 14:06:30
id: 76
tags: Tab
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-标签页(Tab)

## 标签(Tab)

Tab图标位于文字上方，LeadingIconTab图标位于文字左侧。

```kotlin
@Composable
fun Tab(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // tab使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 选中内容颜色
    selectedContentColor: Color = LocalContentColor.current,
    // 未选中内容颜色
    unselectedContentColor: Color = selectedContentColor.copy(alpha = ContentAlpha.medium),
    content: @Composable ColumnScope.() -> Unit
): Unit

@Composable
fun Tab(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // tab使能
    enabled: Boolean = true,
    // 文本
    text: (@Composable () -> Unit)? = null,
    // 图标
    icon: (@Composable () -> Unit)? = null,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 选中内容颜色
    selectedContentColor: Color = LocalContentColor.current,
    // 未选中内容颜色
    unselectedContentColor: Color = selectedContentColor.copy(alpha = ContentAlpha.medium)
): Unit

@Composable
fun LeadingIconTab(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 文本
    text: @Composable () -> Unit,
    // 图标
    icon: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // tab使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 选中内容颜色
    selectedContentColor: Color = LocalContentColor.current,
    // 未选中内容颜色
    unselectedContentColor: Color = selectedContentColor.copy(alpha = ContentAlpha.medium)
): Unit
```



## 标签行(TabRow)

ScrollableTabRow支持滚动。

```kotlin
@Composable
@UiComposable
fun TabRow(
    // 当前选中的 Tab 下标
    selectedTabIndex: Int,
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 指示器
    indicator: @Composable @UiComposable (tabPositions: List<TabPosition>) -> Unit = @Composable { tabPositions ->
            TabRowDefaults.Indicator(
                Modifier.tabIndicatorOffset(tabPositions[selectedTabIndex])
            )
        },
    // 底部分割线
    divider: @Composable @UiComposable () -> Unit = @Composable {
            TabRowDefaults.Divider()
        },
    // Tab 数组
    tabs: @Composable @UiComposable () -> Unit
): Unit

@Composable
@UiComposable
fun ScrollableTabRow(
    // 当前选中的 Tab 下标
    selectedTabIndex: Int,
    // 修饰符
    modifier: Modifier = Modifier,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.primarySurface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 第一个tab距离左侧以及最后一个tab距离右侧的padding
    edgePadding: Dp = TabRowDefaults.ScrollableTabRowPadding,
    // 指示器
    indicator: @Composable @UiComposable (tabPositions: List<TabPosition>) -> Unit = @Composable { tabPositions ->
            TabRowDefaults.Indicator(
                Modifier.tabIndicatorOffset(tabPositions[selectedTabIndex])
            )
        },
    // 底部分割线
    divider: @Composable @UiComposable () -> Unit = @Composable {
            TabRowDefaults.Divider()
        },
    // Tab 数组
    tabs: @Composable @UiComposable () -> Unit
): Unit
```

```kotlin
@Composable
fun SliderTabs() {
    var selectedIndex by remember { mutableStateOf(0) }
    val bgColor = Color(0xff1E76DA)

    val list = listOf("Active", "Completed")
    TabRow(
        selectedTabIndex = selectedIndex,
        backgroundColor = Color(0xff1E76DA),
        modifier = Modifier
            .clip(RoundedCornerShape(50)),
        indicator = { tabPositions ->
            TabRowDefaults.Indicator(
                modifier = Modifier
                    .tabIndicatorOffset(tabPositions[selectedIndex])
                    .fillMaxSize()
                    .padding(1.dp)
                    .clip(RoundedCornerShape(50)),
                color = Color.White
            )
        }
    ) {
        list.forEachIndexed { index, text ->
            val selected = selectedIndex == index
            LeadingIconTab(
                modifier = Modifier.zIndex(2f),
                selected = selected,
                onClick = { selectedIndex = index },
                selectedContentColor = bgColor,
                unselectedContentColor = Color.White,
                text = { Text(text = text) },
                icon = { Icon(imageVector = Icons.Default.Favorite, contentDescription = null) },
                interactionSource = NoRippleInteractionSource()
            )
        }
    }
}
```

