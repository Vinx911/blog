---
title: Jetpack Compose组件-下拉菜单(DropdownMenu)
date: 2023-01-14 14:30:39
tags: DropdownMenu
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-下拉菜单(DropdownMenu)

## 下拉菜单(DropdownMenu)

2023-01-14 ：DropdownMenu组件目前还存在众多问题，不建议使用。

```kotlin
@Composable
fun DropdownMenu(
    // 是否展开
    expanded: Boolean,
    // 关闭请求回调，点击对话框外部或者按下返回按钮关闭对话框时调用
    onDismissRequest: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 对话框位置偏移
    offset: DpOffset = DpOffset(0.dp, 0.dp),
    // 对话框属性
    properties: PopupProperties = PopupProperties(focusable = true),
    content: @Composable ColumnScope.() -> Unit
): Unit

@Composable
fun DropdownMenuItem(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 菜单项使能
    enabled: Boolean = true,
    // 内容边距
    contentPadding: PaddingValues = MenuDefaults.DropdownMenuItemContentPadding,
    // 点击状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    content: @Composable RowScope.() -> Unit
): Unit
```



## 显示下拉菜单的框

```kotlin
@ExperimentalMaterialApi
@Composable
fun ExposedDropdownMenuBox(
    // 是否展开
    expanded: Boolean,
    // 展开状态改变
    onExpandedChange: (Boolean) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    content: @Composable ExposedDropdownMenuBoxScope.() -> Unit
): Unit
```

