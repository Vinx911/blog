---
title: Jetpack Compose组件-滑动条(Slider)
date: 2023-01-13 09:23:18
tags: Slider
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-滑动条(Slider)

## 滑动条(Slider)

`Slider` 允许用户从一定范围的数值中进行选择。

`Slider` 反映了一个沿条的数值范围，用户可以从中选择一个单一的数值。它们是调整音量、亮度或应用图像过滤器等设置的理想选择。

```kotlin
@Composable
fun Slider(
    // 滑动条值
    value: Float,
    // 值改变回调
    onValueChange: (Float) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 滑动条使能
    enabled: Boolean = true,
    // 值的范围
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    // 将整个范围均匀分割为(steps + 1) 份，并且滑动时自动吸附到对应份的最近边界
    steps: Int = 0,
    // 滑动条值改变完成
    onValueChangeFinished: (() -> Unit)? = null,
    // 滑动条状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 滑动条的各种颜色
    colors: SliderColors = SliderDefaults.colors()
): Unit
```

```kotlin
var sliderPosition by remember { mutableStateOf(0f) }
Text(text = sliderPosition.toString())
Slider(
    value = sliderPosition,
    onValueChange = { sliderPosition = it },
    valueRange = 0f..100f,
    onValueChangeFinished = {
        Log.d("1234", "value: $sliderPosition")
        // launch some business logic update with the state you hold
        // viewModel.updateSelectedSliderValue(sliderPosition)
    },
    steps = 4,
    colors = SliderDefaults.colors(
        thumbColor = MaterialTheme.colors.secondary,
        activeTrackColor = MaterialTheme.colors.secondary
    )
)
```

## 范围滑动条(RangeSlider)

RangeSlider滑动左右两个滑块选定一个范围。

```kotlin
@Composable
@ExperimentalMaterialApi
fun RangeSlider(
    // 滑动条当前值范围
    value: ClosedFloatingPointRange<Float>,
    // 值改变回调
    onValueChange: (ClosedFloatingPointRange<Float>) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 滑动条使能
    enabled: Boolean = true,
    // 滑动条最大值范围
    valueRange: ClosedFloatingPointRange<Float> = 0f..1f,
    // 将整个范围均匀分割为(steps + 1) 份，并且滑动时自动吸附到对应份的最近边界
    steps: Int = 0,
    // 滑动条值改变完成
    onValueChangeFinished: (() -> Unit)? = null,
    // 滑动条的各种颜色
    colors: SliderColors = SliderDefaults.colors()
): Unit
```

```kotlin
var sliderPosition by remember { mutableStateOf(0f..100f) }
Text(text = sliderPosition.toString())
RangeSlider(
    steps = 4,
    value = sliderPosition,
    onValueChange = { sliderPosition = it },
    valueRange = 0f..100f,
    onValueChangeFinished = {
        // launch some business logic update with the state you hold
        // viewModel.updateSelectedSliderValue(sliderPosition)
    },
    colors = SliderDefaults.colors(
        thumbColor = MaterialTheme.colors.secondary,
        activeTrackColor = MaterialTheme.colors.secondary
    )
)
```

