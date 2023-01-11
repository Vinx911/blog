---
title: Modifier 点击(Clickable & CombinedClickable)
date: 2022-12-30 11:43:52
tags: Clickable
categories:  Jetpack Compose
copyright: false
---

# Modifier 点击(Clickable & CombinedClickable)

## 1. Clickable 点击

Clickable 修饰符用来监听组件的点击操作，并且当点击事件发生时会为被点击的组件施加一个波纹涟漪效果动画的蒙层。

```kotlin
fun Modifier.clickable(
  enabled: Boolean = true,
  onClickLabel: String? = null,
  role: Role? = null,
  onClick: () -> Unit
)
	
fun Modifier.clickable(
    interactionSource: MutableInteractionSource,
    indication: Indication?,
    enabled: Boolean,
    onClickLabel: String?,
    role: Role?,
    onClick: () -> Unit
)
```

- enabled: 启用点击

- onClickLabel: 无障碍提示文本
- role: 无障碍提示控件的类型
- indication: 默认为LocalIndication.current，设置为null禁用水波纹效果
- interactionSource: 点击状态源

```kotlin
Box(modifier = Modifier
      .size(200.dp)
      .background(Color.Green)
      .clickable(enabled = enableState) {
        Log.d(TAG, "发生单击操作了～")
      }
  )
```



## 2. CombinedClickable 复合点击

对于长按点击、双击等复合类点击手势，我们可以使用 CombinedClickable 修饰符来实现手势监听。与 Clickable 修饰符一样，他同样也可以监听单击手势，并且也会为被点击的组件施加一个波纹涟漪效果动画的蒙层。

```kotlin
fun Modifier.combinedClickable(
    enabled: Boolean = true,
    onClickLabel: String? = null,
    role: Role? = null,
    onLongClickLabel: String? = null,
    onLongClick: (() -> Unit)? = null,
    onDoubleClick: (() -> Unit)? = null,
    onClick: () -> Unit
)

fun Modifier.combinedClickable(
    interactionSource: MutableInteractionSource,
    indication: Indication?,
    enabled: Boolean = true,
    onClickLabel: String? = null,
    role: Role? = null,
    onLongClickLabel: String? = null,
    onLongClick: (() -> Unit)? = null,
    onDoubleClick: (() -> Unit)? = null,
    onClick: () -> Unit
)
```

- onLongClick: 长按点击
- onDoubleClick: 双击
- onClick: 点击

```kotlin
Box(modifier = Modifier
    .size(200.dp)
    .background(Color.Green)
    .combinedClickable(
      enabled = enableState,
      onLongClick = {
        Log.d(TAG, "发生长按点击操作了～")
      },
      onDoubleClick = {
        Log.d(TAG, "发生双击操作了～")
      },
      onClick = {
        Log.d(TAG, "发生单击操作了～")
      }
    )
  )
```



## 3. 不同点击状态下的按钮状态

创建 Data class 来记录不同状态下按钮的样式

```kotlin
data class ButtonState(var text: String, var textColor: Color, var buttonColor: Color)
```

使用  MutableInteractionSource() 获取状态，根据不同状态创建样式

- collectIsPressedAsState(): 按下

- collectIsHoveredAsState(): 鼠标移动到上方

- collectIsFocusedAsState(): 获取到焦点

- collectIsDraggedAsState(): 拖动

```kotlin
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

## 4. 如何禁用涟漪（水波纹）效果

### 4.1 可以将indication设为null

```kotlin
modifier = Modifier
            .clickable(onClick = {}, indication = null, interactionSource = remember {
                MutableInteractionSource()
            })
```

### 4.2 使用pointerInput设置点击事件

```kotlin
modifier = Modifier.pointerInput(Unit) {
                    detectTapGestures(
                    // 长按事件
                    onLongPress = {}, 
                    // 点击事件
                    onTap = { })
                }
```

### 4.3 创建自定义 MutableInteractionSource 类

此方法可用于Button/Switch等控件

```kotlin
class NoRippleInteractionSource : MutableInteractionSource {
    override val interactions: Flow<Interaction> = emptyFlow()
    override suspend fun emit(interaction: Interaction) {}    
    override fun tryEmit(interaction: Interaction) = true
}

interactionSource = NoRippleInteractionSource()
```

### 4.4 创建自定义无波纹主题

```kotlin
private object NoRippleTheme : RippleTheme {
    @Composable
    override fun defaultColor() = Color.Unspecified

    @Composable
    override fun rippleAlpha(): RippleAlpha = RippleAlpha(0.0f,0.0f,0.0f,0.0f)
}
```

