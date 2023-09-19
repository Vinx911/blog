---
title: Modifier 滑动(Swipeable)
date: 2022-12-30 12:12:39
id: 44
tags: Swipeable
categories: Jetpack Compose
copyright: true
---

# Modifier 滑动(Swipeable)

`swipeable` 修饰符允许开发者通过锚点设置从而实现组件呈现吸附效果的动画，常用于开关等动画，也可用于下拉刷新等特殊效果的实现。

`swipeable` 修饰符不会为被修饰的组件提供任何默认动画，只能为组件提供手势偏移量等信息。开发者可根据自身需求根据偏移量结合其他修饰符定制动画展示。

```kotlin
fun <T : Any?> Modifier.swipeable(
    state: SwipeableState<T>,
    anchors: Map<Float, T>,
    orientation: Orientation,
    enabled: Boolean = true,
    reverseDirection: Boolean = false,
    interactionSource: MutableInteractionSource? = null,
    thresholds: (from, to) -> ThresholdConfig = { _, _ -> FixedThreshold(56.dp) },
    resistance: ResistanceConfig? = resistanceConfig(anchors.keys),
    velocityThreshold: Dp = VelocityThreshold
)
```

- state: 通过 swipeableState 的设置可以获取到当前手势的偏移量信息
- anchors: 锚点，可以通过锚点设置在不同状态时所应该对应的偏移量信息
- orientation: 手势方向，被修饰组件的手势方向只能是水平或垂直
- enabled: 启用滑动
- reverseDirection:  反转滚动的方向，从上到下滚动将表现为从下到上，从左到右将表现为从右到左。
- interactionSource: 滑动时发出此状态
- thresholds: 常用作定制不同锚点间吸附效果的临界阈值，常用有 `FixedThreshold(Dp)` 和`FractionalThreshold(Float)`等
- resistance: 控制滑动越过边界时将应用多少阻力。
- velocityThreshold: 最终速度必须超过的阈值（以 dp 为单位）以动画到下一个状态

```kotlin
@OptIn(ExperimentalMaterialApi::class)
@Composable
fun SwipeableDemo() {
    // Draw a slider-like composable consisting of a red square moving along a
    // black background, with three states: "A" (min), "B" (middle), and "C" (max).
    val width = 350.dp
    val squareSize = 50.dp

    val swipeableState = rememberSwipeableState("A")
    val sizePx = with(LocalDensity.current) { (width - squareSize).toPx() }
    val anchors = mapOf(0f to "A", sizePx / 2 to "B", sizePx to "C")

    Box(
        modifier = Modifier
            .width(width)
            .swipeable(
                state = swipeableState,
                anchors = anchors,
                thresholds = { _, _ -> FractionalThreshold(0.5f) },
                orientation = Orientation.Horizontal
            )
            .background(Color.Black)
    ) {
        Box(
            Modifier
                .offset { IntOffset(swipeableState.offset.value.roundToInt(), 0) }
                .size(squareSize)
                .background(Color.Red),
            contentAlignment = Alignment.Center
        ) {
            Text(swipeableState.currentValue, color = Color.White, fontSize = 24.sp)
        }
    }
}
```

