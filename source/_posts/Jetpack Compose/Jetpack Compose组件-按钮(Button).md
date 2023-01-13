---
title: Jetpack Compose组件-按钮(Button)
date: 2023-01-11 16:47:27
tags: Button
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-按钮(Button)

## 按钮(Button)

**Compose** 的 `Button` 是基于 **Material Design** 理念设计的

```kotlin
@Composable
fun Button(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 按钮使能
    enabled: Boolean = true,
    // 按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 按钮的高度(阴影)
    elevation: ButtonElevation? = ButtonDefaults.elevation(),
    // 按钮的形状
    shape: Shape = MaterialTheme.shapes.small,
    // 按钮的边框
    border: BorderStroke? = null,
    // 按钮的各种颜色
    colors: ButtonColors = ButtonDefaults.buttonColors(),    
    // 按钮内容边距
    contentPadding: PaddingValues = ButtonDefaults.ContentPadding,
    content: @Composable RowScope.() -> Unit
): Unit
```

### 基本使用

```kotlin
Button(onClick = { /*TODO*/ }) {
    Icon(
        // Material 库中的图标，有 Filled, Outlined, Rounded, Sharp, Two Tone 等
        Icons.Filled.Favorite,
        contentDescription = null,
        modifier = Modifier.size(ButtonDefaults.IconSize)
    )
    // 添加间隔
    Spacer(Modifier.size(ButtonDefaults.IconSpacing))
    Text("喜欢")
}
```

### 不同点击状态下的按钮状态

```kotlin
// 创建 Data class 来记录不同状态下按钮的样式
data class ButtonState(var text: String, var textColor: Color, var buttonColor: Color)

// 使用 MutableInteractionSource() 获取状态，根据不同状态创建样式
// 获取按钮的状态
val interactionState = remember { MutableInteractionSource() }

// 使用 Kotlin 的解构方法
val (text, textColor, buttonColor) = when {
    interactionState.collectIsPressedAsState().value  -> ButtonState("Just Pressed", Color.Red, Color.Black)
    else -> ButtonState( "Just Button", Color.White, Color.Red)
}

Button(
    onClick = { /*TODO*/ },
    interactionSource = interactionState,
    elevation = null,
    shape = RoundedCornerShape(50),
    colors = ButtonDefaults.buttonColors(
        backgroundColor = buttonColor,
    ),
    modifier = Modifier.width(IntrinsicSize.Min).height(IntrinsicSize.Min)
) {
    Text(
        text = text, color = textColor
    )
}
```



## 轮廓线按钮(OutlinedButton)

`OutlinedButton`是有轮廓线的按钮。

```kotlin
@Composable
@NonRestartableComposable
fun OutlinedButton(
  // 参数与Botton一致，仅默认值不一样
): Unit
```



## 文本按钮(TextButton)

文本按钮通常用于不太明显的操作，包括那些位于对话框和卡片中的操作。内部`Text`组件的默认文本样式将设置为`Typography.button`。

```kotlin
@Composable
@NonRestartableComposable
fun TextButton(
    // 参数与Botton一致，仅默认值不一样
): Unit
```



## 图标按钮(IconButton)

IconButton 是一个可点击的图标，用于表示动作。IconButton 的整体最小触摸目标尺寸为 48 x 48dp，以满足无障碍指南。`content`在 IconButton 内居中。

```kotlin
@Composable
fun IconButton(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 按钮使能
    enabled: Boolean = true,
    // 按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    content: @Composable () -> Unit
): Unit
```

```kotlin
IconButton(onClick = { /* doSomething() */ }) {
    Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
}
```

## 图标切换按钮(IconToggleButton)

```kotlin
@Composable
fun IconToggleButton(
    // 是否选中
    checked: Boolean,
    // 选中状态改变回调
    onCheckedChange: (Boolean) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 按钮使能
    enabled: Boolean = true,
    // 按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    content: @Composable () -> Unit
): Unit
```

```kotlin
IconToggleButton(checked = checked, onCheckedChange = { checked = it }) {
    val tint by animateColorAsState(if (checked) Color(0xFFEC407A) else Color(0xFFB0BEC5))
    Icon(Icons.Filled.Favorite, contentDescription = "Localized description", tint = tint)
}
```

## 浮动按钮(FloatingActionButton)

```kotlin
@Composable
fun FloatingActionButton(
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 按钮的形状
    shape: Shape = MaterialTheme.shapes.small.copy(CornerSize(percent = 50)),
    // 按钮的背景颜色
    backgroundColor: Color = MaterialTheme.colors.secondary,
    // 按钮的内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 按钮的高度(阴影)
    elevation: FloatingActionButtonElevation = FloatingActionButtonDefaults.elevation(),
    content: @Composable () -> Unit
): Unit
```

```kotlin
FloatingActionButton(onClick = { /*do something*/ }) {
    Icon(Icons.Filled.Favorite, contentDescription = "Localized description")
}
```

## 扩展的浮动按钮(ExtendedFloatingActionButton)

带有图标 + 文字的浮动按钮。

```kotlin
@Composable
fun ExtendedFloatingActionButton(
	// 文本
    text: @Composable () -> Unit,
    // 点击回调
    onClick: () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 图标
    icon: (@Composable () -> Unit)? = null,
    // 按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 按钮的形状
    shape: Shape = MaterialTheme.shapes.small.copy(CornerSize(percent = 50)),
    // 按钮的背景颜色
    backgroundColor: Color = MaterialTheme.colors.secondary,
    // 按钮的内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 按钮的高度(阴影)
    elevation: FloatingActionButtonElevation = FloatingActionButtonDefaults.elevation()
): Unit
```

