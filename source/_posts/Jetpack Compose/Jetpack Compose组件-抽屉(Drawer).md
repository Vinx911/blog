---
title: Jetpack Compose组件-抽屉(Drawer)
date: 2023-01-16 14:25:01
id: 80
tags: Drawer
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-抽屉(Drawer)

## 底部抽屉(BottomDrawer)

BottomDrawer是一个从底部滑出的界面。

```kotlin
@Composable
@ExperimentalMaterialApi
fun BottomDrawer(
    // 底部抽屉内容
    drawerContent: @Composable ColumnScope.() -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 底部抽屉状态源
    drawerState: BottomDrawerState = rememberBottomDrawerState(BottomDrawerValue.Closed),
    // 启用手势
    gesturesEnabled: Boolean = true,
    // 底部抽屉形状
    drawerShape: Shape = MaterialTheme.shapes.large,
    // 底部高度(阴影)
    drawerElevation: Dp = DrawerDefaults.Elevation,
    // 底部背景颜色
    drawerBackgroundColor: Color = MaterialTheme.colors.surface,
    // 底部抽屉内容颜色
    drawerContentColor: Color = contentColorFor(drawerBackgroundColor),
    // 底部抽屉打开后，遮罩颜色
    scrimColor: Color = DrawerDefaults.scrimColor,
    content: @Composable () -> Unit
): Unit
```



## 模态抽屉(ModalDrawer)

ModalDrawer是一个从左侧滑出的界面。

```kotlin
@Composable
fun ModalDrawer(
    // 抽屉内容
    drawerContent: @Composable ColumnScope.() -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 抽屉状态源
    drawerState: DrawerState = rememberDrawerState(DrawerValue.Closed),
    // 启用手势
    gesturesEnabled: Boolean = true,
    // 抽屉形状
    drawerShape: Shape = MaterialTheme.shapes.large,
    // 高度(阴影)
    drawerElevation: Dp = DrawerDefaults.Elevation,
    // 抽屉背景颜色
    drawerBackgroundColor: Color = MaterialTheme.colors.surface,
    // 抽屉内容颜色
    drawerContentColor: Color = contentColorFor(drawerBackgroundColor),
    // 抽屉打开后，遮罩颜色
    scrimColor: Color = DrawerDefaults.scrimColor,
    content: @Composable () -> Unit
): Unit
```



## 模态底部Sheet(ModalBottomSheetLayout)

```kotlin
@Composable
@ExperimentalMaterialApi
fun ModalBottomSheetLayout(
    // sheet内容
    sheetContent: @Composable ColumnScope.() -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // sheet状态源
    sheetState: ModalBottomSheetState = rememberModalBottomSheetState(Hidden),
    // 抽屉形状
    sheetShape: Shape = MaterialTheme.shapes.large,
    // 高度(阴影)
    sheetElevation: Dp = ModalBottomSheetDefaults.Elevation,
    // sheet背景颜色
    sheetBackgroundColor: Color = MaterialTheme.colors.surface,
    // sheet内容颜色
    sheetContentColor: Color = contentColorFor(sheetBackgroundColor),
    // sheet打开后，遮罩颜色
    scrimColor: Color = ModalBottomSheetDefaults.scrimColor,
    content: @Composable () -> Unit
): Unit
```

