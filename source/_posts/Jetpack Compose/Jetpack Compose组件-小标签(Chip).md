---
title: Jetpack Compose组件-小标签(Chip)
date: 2023-01-13 08:43:57
id: 64
tags: Chip
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-小标签(Chip)

## 小标签(Chip)

Chip(小标签)类似于淘宝的历史搜索标签。

```kotlin
@ExperimentalMaterialApi
@Composable
fun Chip(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 小标签使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 小标签形状
    shape: Shape = MaterialTheme.shapes.small.copy(CornerSize(percent = 50)),
    // 小标签边框
    border: BorderStroke? = null,
    // 小标签的各种颜色
    colors: ChipColors = ChipDefaults.chipColors(),
    // 小标签左侧图标
    leadingIcon: (@Composable () -> Unit)? = null,
    content: @Composable RowScope.() -> Unit
): Unit
```

```kotlin
Chip(
    onClick = { /* Do something! */ },
    border = ChipDefaults.outlinedBorder,
    colors = ChipDefaults.outlinedChipColors(),
    leadingIcon = {
        Icon(
            Icons.Filled.Settings,
            contentDescription = "Localized description"
        )
    }
) {
    Text("Change settings")
}
```



## 可以选中的小标签(FilterChip)

```kotlin
@ExperimentalMaterialApi
@Composable
fun FilterChip(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 小标签使能
    enabled: Boolean = true,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 小标签形状
    shape: Shape = MaterialTheme.shapes.small.copy(CornerSize(percent = 50)),
    // 小标签边框
    border: BorderStroke? = null,
    // 小标签的各种颜色
    colors: SelectableChipColors = ChipDefaults.filterChipColors(),
    // 小标签左侧图标
    leadingIcon: (@Composable () -> Unit)? = null,
    // 小标签选中图标
    selectedIcon: (@Composable () -> Unit)? = null,
    // 小标签右侧图标
    trailingIcon: (@Composable () -> Unit)? = null,
    content: @Composable RowScope.() -> Unit
): Unit
```

```kotlin
val state = remember { mutableStateOf(false) }
FilterChip(
    selected = state.value,
    onClick = { state.value = !state.value },
    leadingIcon = {
        Icon(
            imageVector = Icons.Filled.Home,
            contentDescription = "Localized description",
            modifier = Modifier.requiredSize(ChipDefaults.LeadingIconSize)
        )
    },
    selectedIcon = {
        Icon(
            imageVector = Icons.Filled.Done,
            contentDescription = "Localized Description",
            modifier = Modifier.requiredSize(ChipDefaults.SelectedIconSize)
        )
    }
) {
    Text("Filter chip")
}
```

