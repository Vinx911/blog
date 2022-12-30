---
title: Modifier 开关（Toggleable）
date: 2022-12-30 12:12:39
tags: Draggable
categories:  Jetpack Compose
copyright: false

---

# Modifier 开关（Toggleable）

`toggleable` 修饰符非常适合用来做开关效果

`triStateToggleable` 修饰符为三态开关（启用、停用和不确定）

```kotlin
fun Modifier.toggleable(
    value: Boolean,
    enabled: Boolean,
    role: Role?,
    onValueChange: (Boolean) -> Unit
)

fun Modifier.toggleable(
    value: Boolean,
    interactionSource: MutableInteractionSource,
    indication: Indication?,
    enabled: Boolean,
    role: Role?,
    onValueChange: (Boolean) -> Unit
)

fun Modifier.triStateToggleable(
    state: ToggleableState,
    enabled: Boolean,
    role: Role?,
    onClick: () -> Unit
)

fun Modifier.triStateToggleable(
    state: ToggleableState,
    interactionSource: MutableInteractionSource,
    indication: Indication?,
    enabled: Boolean,
    role: Role?,
    onClick: () -> Unit
)
```

- value: Toggleable 是打开还是关闭
- enabled: 启用Toggleable 
- interactionSource: 按下时发出此状态
- indication: 按下修改后的元素时显示的指示。默认情况下，`LocalIndication`将使用指示。传递`null`以显示无指示，
- role: 用户界面元素的类型。
- onValueChange: 单击 toggleable 时调用的回调
- onClick: 在用户单击可切换时调用

```kotlin
@Composable
fun ToggleableDemo() {
    var checked by remember { mutableStateOf(false) }
    // content that you want to make toggleable
    Text(
        modifier = Modifier.toggleable(value = checked, onValueChange = { checked = it }),
        text = checked.toString()
    )
}

@Composable
fun TriStateToggleableDemo() {
    var checked by remember { mutableStateOf(ToggleableState.Indeterminate) }
    // content that you want to make toggleable
    Text(
        modifier = Modifier.triStateToggleable(
            state = checked,
            onClick = {
                checked =
                    if (checked == ToggleableState.On)
                        ToggleableState.Off
                    else
                        ToggleableState.On
            }
        ),
        text = checked.toString()
    )
}
```

