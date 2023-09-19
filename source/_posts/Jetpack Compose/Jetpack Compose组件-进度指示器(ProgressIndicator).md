---
title: Jetpack Compose组件-进度指示器(ProgressIndicator)
date: 2023-01-13 09:37:48
id: 69
tags: ProgressIndicator
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-进度指示器(ProgressIndicator)

## 线性进度指示器(LinearProgressIndicator)

```kotlin
// 等待条
@Composable
fun LinearProgressIndicator(
    // 修饰符
    modifier: Modifier = Modifier,
    // 进度颜色
    color: Color = MaterialTheme.colors.primary,
    // 背景颜色
    backgroundColor: Color = color.copy(alpha = IndicatorBackgroundOpacity),
    // 进度条末端样式
    strokeCap: StrokeCap = StrokeCap.Butt
): Unit

@Composable
fun LinearProgressIndicator(
    // 进度
    progress: Float,
    // 修饰符
    modifier: Modifier = Modifier,
    // 进度颜色
    color: Color = MaterialTheme.colors.primary,
    // 背景颜色
    backgroundColor: Color = color.copy(alpha = IndicatorBackgroundOpacity),
    // 进度条末端样式
    strokeCap: StrokeCap = StrokeCap.Butt
): Unit
```

```kotlin
var progress by remember { mutableStateOf(0.1f) }
val animatedProgress by animateFloatAsState(
    targetValue = progress,
    animationSpec = ProgressIndicatorDefaults.ProgressAnimationSpec
)

Column(horizontalAlignment = Alignment.CenterHorizontally) {
    LinearProgressIndicator(progress = animatedProgress)
    Spacer(Modifier.requiredHeight(30.dp))
    OutlinedButton(
        onClick = {
            if (progress < 1f) progress += 0.1f
        }
    ) {
        Text("Increase")
    }
}
```

## 圆形进度指示器(CircularProgressIndicator)

```kotlin
@Composable
fun CircularProgressIndicator(
    // 修饰符
    modifier: Modifier = Modifier,
    // 进度颜色
    color: Color = MaterialTheme.colors.primary,
    // 进度条宽度
    strokeWidth: Dp = ProgressIndicatorDefaults.StrokeWidth,
    // 背景颜色
    backgroundColor: Color = Color.Transparent,
    // 进度条末端样式
    strokeCap: StrokeCap = StrokeCap.Square
): Unit

@Composable
fun CircularProgressIndicator(
    // 进度
    progress: Float,
    // 修饰符
    modifier: Modifier = Modifier,
    // 进度颜色
    color: Color = MaterialTheme.colors.primary,
    // 进度条宽度
    strokeWidth: Dp = ProgressIndicatorDefaults.StrokeWidth,
    // 背景颜色
    backgroundColor: Color = Color.Transparent,
    // 进度条末端样式
    strokeCap: StrokeCap = StrokeCap.Butt
): Unit
```

```kotlin
var progress by remember { mutableStateOf(0.1f) }
val animatedProgress by animateFloatAsState(
    targetValue = progress,
    animationSpec = ProgressIndicatorDefaults.ProgressAnimationSpec
)

Column(horizontalAlignment = Alignment.CenterHorizontally) {
    CircularProgressIndicator(progress = animatedProgress)
    Spacer(Modifier.requiredHeight(30.dp))
    OutlinedButton(
        onClick = {
            if (progress < 1f) progress += 0.1f
        }
    ) {
        Text("Increase")
    }
}
```

