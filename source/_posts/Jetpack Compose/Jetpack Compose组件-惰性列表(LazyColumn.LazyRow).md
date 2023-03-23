---
title: Jetpack Compose组件-惰性列表(LazyColumn.LazyRow)
date: 2023-01-17 10:27:31
tags: [LazyColumn,LazyRow]
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-惰性列表(LazyColumn.LazyRow)

## 惰性列(LazyColumn)

在 LazyListScpoe 中，可以使用以下语句进行操作：

- item 添加单个列表项
- items 添加一组列表项
- itemsIndexed 添加一组列表项
- stickyHeader 添加粘性头部

```kotlin
@Composable
fun LazyColumn(
    // 修饰符
    modifier: Modifier = Modifier,
    // 列表状态
    state: LazyListState = rememberLazyListState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 反转滚动和布局方向
    reverseLayout: Boolean = false,
    // 垂直排列
    verticalArrangement: Arrangement.Vertical = if (!reverseLayout) Arrangement.Top else Arrangement.Bottom,
    // 水平排列
    horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyListScope.() -> Unit
): Unit
```





## 惰性行(LazyRow)

```kotlin
@Composable
fun LazyRow(
    // 修饰符
    modifier: Modifier = Modifier,
    // 列表状态
    state: LazyListState = rememberLazyListState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 反转滚动和布局方向
    reverseLayout: Boolean = false,
    // 水平排列
    horizontalArrangement: Arrangement.Horizontal = if (!reverseLayout) Arrangement.Start else Arrangement.End,
    // 垂直排列
    verticalAlignment: Alignment.Vertical = Alignment.Top,
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyListScope.() -> Unit
): Unit
```

## 惰性垂直网格(LazyVerticalGrid)

```kotlin
@Composable
fun LazyVerticalGrid(
    // 每行个数
    columns: GridCells,
    // 修饰符
    modifier: Modifier = Modifier,
    // 网格状态
    state: LazyGridState = rememberLazyGridState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 反转滚动和布局方向
    reverseLayout: Boolean = false,
    // 垂直排列
    verticalArrangement: Arrangement.Vertical = if (!reverseLayout) Arrangement.Top else Arrangement.Bottom,
    // 水平排列
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyGridScope.() -> Unit
): Unit
```

## 惰性水平网格(LazyHorizontalGrid)

```kotlin
@Composable
fun LazyHorizontalGrid(
    // 每列个数
    rows: GridCells,
    // 修饰符
    modifier: Modifier = Modifier,
    // 网格状态
    state: LazyGridState = rememberLazyGridState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 反转滚动和布局方向
    reverseLayout: Boolean = false,
    // 水平排列
    horizontalArrangement: Arrangement.Horizontal = if (!reverseLayout) Arrangement.Start else Arrangement.End,
    // 垂直排列
    verticalArrangement: Arrangement.Vertical = Arrangement.Top,
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyGridScope.() -> Unit
): Unit
```

## 惰性垂直流式网格(LazyVerticalStaggeredGrid)

```kotlin
@ExperimentalFoundationApi
@Composable
fun LazyVerticalStaggeredGrid(
    // 每行个数
    columns: StaggeredGridCells,
    // 修饰符
    modifier: Modifier = Modifier,
    // 网格状态
    state: LazyStaggeredGridState = rememberLazyStaggeredGridState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 垂直排列
    verticalArrangement: Arrangement.Vertical = Arrangement.spacedBy(0.dp),
    // 水平排列
    horizontalArrangement: Arrangement.Horizontal = Arrangement.spacedBy(0.dp),
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyStaggeredGridScope.() -> Unit
): Unit
```

## 惰性水平流式网格(LazyHorizontalStaggeredGrid)

```kotlin
@ExperimentalFoundationApi
@Composable
fun LazyHorizontalStaggeredGrid(
    // 每列个数
    rows: StaggeredGridCells,
    // 修饰符
    modifier: Modifier = Modifier,
    // 网格状态
    state: LazyStaggeredGridState = rememberLazyStaggeredGridState(),
    // 内容padding
    contentPadding: PaddingValues = PaddingValues(0.dp),
    // 垂直排列
    verticalArrangement: Arrangement.Vertical = Arrangement.spacedBy(0.dp),
    // 水平排列
    horizontalArrangement: Arrangement.Horizontal = Arrangement.spacedBy(0.dp),
    // 甩动偏好
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    // 禁止用户滚动，但可以通过编程方式滚动
    userScrollEnabled: Boolean = true,
    content: LazyStaggeredGridScope.() -> Unit
): Unit
```

## 惰性布局(LazyLayout)

```kotlin
@ExperimentalFoundationApi
@Composable
fun LazyLayout(
    itemProvider: LazyLayoutItemProvider,
    // 修饰符
    modifier: Modifier = Modifier,
    prefetchState: LazyLayoutPrefetchState? = null,
   //  测量策略
    measurePolicy: LazyLayoutMeasureScope.(Constraints) -> MeasureResult
): Unit
```

