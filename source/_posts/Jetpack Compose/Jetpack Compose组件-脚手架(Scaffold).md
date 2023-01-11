---
title: Jetpack Compose组件-脚手架(Scaffold)
date: 2023-01-04 08:49:34
tags: Scaffold
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-脚手架(Scaffold)

## 脚手架(Scaffold)

`Scaffold` 实现了 **Material Design** 的基本视图界面结构。

```kotlin
@Composable
fun Scaffold(
    // 修饰符
    modifier: Modifier = Modifier,
    // Scaffold的状态，主要是设置drawerState和snackbarHostState的状态
    scaffoldState: ScaffoldState = rememberScaffoldState(),
    // 顶栏的布局
    topBar: @Composable () -> Unit = {},
    // 底栏的布局
    bottomBar: @Composable () -> Unit = {},
    // snackbarHost这个属性就是设置了一个Snackbar控件。
    snackbarHost: @Composable (SnackbarHostState) -> Unit = { SnackbarHost(it) },
    // 悬浮按钮布局
    floatingActionButton: @Composable () -> Unit = {},
    // 悬浮按钮位置,有FabPosition.End(默认)和FabPosition.Center可选
    floatingActionButtonPosition: FabPosition = FabPosition.End,
    // 与BottomAppBar配合使用,可以实现底部导航条的裁剪效果
    isFloatingActionButtonDocked: Boolean = false,
    // 侧边抽屉的布局
    drawerContent: (@Composable ColumnScope.() -> Unit)? = null,
    // 是否开启侧边抽屉手势(开启后可侧滑弹出抽屉)
    drawerGesturesEnabled: Boolean = true,
    // 侧边抽屉的形状
    drawerShape: Shape = MaterialTheme.shapes.large,
    // 侧边抽屉的阴影
    drawerElevation: Dp = DrawerDefaults.Elevation,
    // 侧边抽屉的背景色
    drawerBackgroundColor: Color = MaterialTheme.colors.surface,
    // 侧边抽屉内容颜色(似乎是覆盖字体颜色而已)
    drawerContentColor: Color = contentColorFor(drawerBackgroundColor),
    // 侧边抽屉遮盖最底层的颜色
    drawerScrimColor: Color = DrawerDefaults.scrimColor,
    // scaffold的背景颜色
    backgroundColor: Color = MaterialTheme.colors.background,
    // scaffold的内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // scaffold的内容
    content: @Composable (PaddingValues) -> Unit
): Unit
```



## 背景幕脚手架(BackdropScaffold)

```kotlin
@Composable
@ExperimentalMaterialApi
fun BackdropScaffold(
    // 顶部的控件，可用TopAppBar
    appBar: @Composable () -> Unit,
    // 显示在后面被遮挡的内容
    backLayerContent: @Composable () -> Unit,
    // 显示在前面的内容
    frontLayerContent: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 可以设置snackbar的状态。还可以设置BackdropScaffoldState的状态。
    scaffoldState: BackdropScaffoldState = rememberBackdropScaffoldState(Concealed),
    // 可否可用通过手指拖动的形式显示和隐藏前后界面
    gesturesEnabled: Boolean = true,
    // 前面的界面到顶部的距离，也可以理解成后面的界面默认露出显示的高度。默认是56dp
    peekHeight: Dp = BackdropScaffoldDefaults.PeekHeight,
    // 前面界面不活动时的最小高度。
    headerHeight: Dp = BackdropScaffoldDefaults.HeaderHeight,
    // 当为true的时候，不管显示还是隐藏后面的界面，appBar都一直显示的。如果值为false，则显示后面的界面的时候，appBar会隐藏
    persistentAppBar: Boolean = true,
    // 当为true的时候，前面的界面之后下拉到后面界面的内容高度位置。为false 的时候，前面的界面可以一直下拉到屏幕底部（试试效果就知道）
    stickyFrontLayer: Boolean = true,
    // 后面界面的背景颜色
    backLayerBackgroundColor: Color = MaterialTheme.colors.primary,
    // 后面界面的内容的颜色
    backLayerContentColor: Color = contentColorFor(backLayerBackgroundColor),
    // 前面界面的形状
    frontLayerShape: Shape = BackdropScaffoldDefaults.frontLayerShape,
    // 前面界面的阴影
    frontLayerElevation: Dp = BackdropScaffoldDefaults.FrontLayerElevation,
    // 前面界面的背景颜色
    frontLayerBackgroundColor: Color = MaterialTheme.colors.surface,
    // 前面界面的内容的颜色
    frontLayerContentColor: Color = contentColorFor(frontLayerBackgroundColor),
    // 前面界面下拉时，顶部预留出来的空白的颜色
    frontLayerScrimColor: Color = BackdropScaffoldDefaults.frontLayerScrimColor,
    // Snackbar
    snackbarHost: @Composable (SnackbarHostState) -> Unit = { SnackbarHost(it) }
): Unit
```



## 底部抽屉脚手架(BottomSheetScaffold)

```kotlin
@Composable
@ExperimentalMaterialApi
fun BottomSheetScaffold(
    // 底部抽屉的内容
    sheetContent: @Composable ColumnScope.() -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // BottomSheetScaffoldState的状态
    scaffoldState: BottomSheetScaffoldState = rememberBottomSheetScaffoldState(),
    // 顶部控件，可用TopAppBar
    topBar: (@Composable () -> Unit)? = null,
    // 其实是设置了一个Snackbar控件
    snackbarHost: @Composable (SnackbarHostState) -> Unit = { SnackbarHost(it) },
    // 浮动的button
    floatingActionButton: (@Composable () -> Unit)? = null,
    // 设置floatingActionButton的按钮的位置
    floatingActionButtonPosition: FabPosition = FabPosition.End,
    // 是否能通过手指滑动去打开和关闭底部的抽屉
    sheetGesturesEnabled: Boolean = true,
    // 底部抽屉的形状
    sheetShape: Shape = MaterialTheme.shapes.large,
    // 底部抽屉的阴影
    sheetElevation: Dp = BottomSheetScaffoldDefaults.SheetElevation,
    // 底部抽屉的背景颜色
    sheetBackgroundColor: Color = MaterialTheme.colors.surface,
    // 底部抽屉的内容颜色
    sheetContentColor: Color = contentColorFor(sheetBackgroundColor),
    // 是底部抽屉的初始的高度
    sheetPeekHeight: Dp = BottomSheetScaffoldDefaults.SheetPeekHeight,
    // 左边抽屉的内容
    drawerContent: (@Composable ColumnScope.() -> Unit)? = null,
    // 是否能通过手指拖动的形式去打开和关闭左边的抽屉
    drawerGesturesEnabled: Boolean = true,
    // 左边抽屉的形状
    drawerShape: Shape = MaterialTheme.shapes.large,
    // 左边抽屉的阴影
    drawerElevation: Dp = DrawerDefaults.Elevation,
    // 左边抽屉的背景颜色
    drawerBackgroundColor: Color = MaterialTheme.colors.surface,
    // 左边抽屉的内容的颜色
    drawerContentColor: Color = contentColorFor(drawerBackgroundColor),
    // 左边抽屉打开的时候，右边剩余部分的颜色值
    drawerScrimColor: Color = DrawerDefaults.scrimColor,
    // BottomSheetScaffold控件的背景颜色
    backgroundColor: Color = MaterialTheme.colors.background,
    // BottomSheetScaffold控件的内容的颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // BottomSheetScaffold控件的内容
    content: @Composable (PaddingValues) -> Unit
): Unit
```

