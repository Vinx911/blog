---
title: Modifier 拖动(Draggable)
date: 2022-12-30 12:12:39
tags: Draggable
categories:  Jetpack Compose
copyright: false

---

# Modifier 拖动(Draggable)

`draggable` 修饰符允许开发者监听UI组件的拖动手势偏移量，通过偏移量从而可以定制UI动画效果。

`draggable` 修饰符只能监听垂直方向偏移或水平方向偏移。

```kotlin
fun Modifier.draggable(
    state: DraggableState,
    orientation: Orientation,
    enabled: Boolean = true,
    interactionSource: MutableInteractionSource? = null,
    startDragImmediately: Boolean = false,
    onDragStarted: suspend CoroutineScope.(startedPosition: Offset) -> Unit = {},
    onDragStopped: suspend CoroutineScope.(velocity: Float) -> Unit = {},
    reverseDirection: Boolean = false
)
```

- state: 通过 draggableState 可以获取到拖动手势的偏移量，并允许开发者动态控制偏移量
- orientation: 监听的拖动手势方向，只能是水平方向(Orientation.Horizontal)或垂直方向(Orientation.Vertical)
- enabled: 启用拖动
- interactionSource: 拖动时发出此状态
- startDragImmediately: 设置为true时，DragTable将立即开始拖动，并防止其他手势检测器对“向下”事件作出反应（以阻止合成的基于按键的手势）
- onDragStarted: 开始拖动的回调
- onDragStopped: 停止拖动的回调
- reverseDirection: 反转滚动的方向，从上到下滚动将表现为从下到上，从左到右将表现为从右到左。

```
@Preview
@Composable
fun draggableTest(){
    var offsetX by remember { mutableStateOf(0f) }
    Text(
        modifier = Modifier
            .offset { IntOffset(offsetX.roundToInt(), 0) }
            .draggable(
                orientation = Orientation.Horizontal,
                state = rememberDraggableState { delta ->
                    offsetX += delta
                },
                onDragStarted = {
                    Log.e("ccm","startDrag")
                },
                onDragStopped = {
                    Log.e("ccm","endDrag")
                }
            ),
        text = "Drag me!"
    )
}
```

