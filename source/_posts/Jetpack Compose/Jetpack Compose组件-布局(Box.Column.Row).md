---
title: Jetpack Compose组件-布局(Box.Column.Row)
date: 2023-01-11 10:24:54
tags: Box
categories: Jetpack Compose
copyright: false

---

#  Jetpack Compose组件-布局(Box.Column.Row)

## 堆叠布局(Box)

Box 是一个能够将里面的子项依次按照顺序堆叠的布局组件。

```kotlin
@Composable
inline fun Box(
    // 修饰符
    modifier: Modifier = Modifier,
    // 内容对齐方式
    contentAlignment: Alignment = Alignment.TopStart,
    // 是否应将传入的最小约束传递给内容
    propagateMinConstraints: Boolean = false,
    content: @Composable BoxScope.() -> Unit
): Unit

// 一个没有内容的框
@Composable
fun Box(modifier: Modifier): Unit
```

## 水平布局(Row)

Row 组件能够将里面的子项按照从左到右的方向水平排列。

```kotlin
@Composable
inline fun Row(
    // 修饰符
    modifier: Modifier = Modifier,
    // 水平对齐方式
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    // 垂直对齐方式
    verticalAlignment: Alignment.Vertical = Alignment.Top,
    content: @Composable RowScope.() -> Unit
): Unit
```



## 垂直布局(Column)

Column 是一个布局组件，它能够将里面的子项按照从上到下的顺序垂直排列。

```kotlin
@Composable
inline fun Column(
    // 修饰符
    modifier: Modifier = Modifier,
    // 垂直对齐方式
    verticalArrangement: Arrangement.Vertical = Arrangement.Top,,
    // 水平对齐方式
    horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    content: @Composable ColumnScope.() -> Unit
): Unit
```



## 空白布局(Spacer)

`Spacer` 能够提供一个空白的布局，可以使用 `Modifier.width`, `Modifier.height` 和 `Modifier.size` 来填充。

```kotlin
@Composable
fun Spacer(modifier: Modifier): Unit
```

## 自适应布局(BoxWithConstraints)

```kotlin
@Composable
@UiComposable
fun BoxWithConstraints(
    // 修饰符
    modifier: Modifier = Modifier,
    // 内容对齐方式
    contentAlignment: Alignment = Alignment.TopStart,
    // 是否应将传入的最小约束传递给内容
    propagateMinConstraints: Boolean = false,
    content: @Composable @UiComposable BoxWithConstraintsScope.() -> Unit
): Unit
```

```kotlin
BoxWithConstraints {
    val rectangleHeight = 100.dp
    if (maxHeight < rectangleHeight * 2) {
        Box(Modifier.size(50.dp, rectangleHeight).background(Color.Blue))
    } else {
        Column {
            Box(Modifier.size(50.dp, rectangleHeight).background(Color.Blue))
            Box(Modifier.size(50.dp, rectangleHeight).background(Color.Gray))
        }
    }
}
```

## 可滚动布局

```kotlin
@Composable
fun ScrollBoxes() {
    Column(
        modifier = Modifier
            .background(Color.LightGray)
            .size(100.dp)
            .verticalScroll(rememberScrollState())
    ) {
        repeat(10) {
            Text("Item $it", modifier = Modifier.padding(2.dp))
        }
    }
}
```

