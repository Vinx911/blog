---
title: Jetpack Compose组件-复选按钮(Checkbox)
date: 2023-01-11 16:54:39
id: 57
tags: Checkbox
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-复选按钮(Checkbox)

## 复选按钮(Checkbox)

```kotlin
@Composable
fun Checkbox(
     // 是否选中
    checked: Boolean,
    // 选中状态改变回调
    onCheckedChange: (Boolean) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 复选按钮使能
    enabled: Boolean = true,
    // 复选按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 复选按钮的各种颜色
    colors: CheckboxColors = CheckboxDefaults.colors()
): Unit
```

```kotlin
val checkedState = remember { mutableStateOf(true) }
Checkbox(
    checked = checkedState.value,
    onCheckedChange = { checkedState.value = it }
)
```



## 三态复选按钮(TriStateCheckbox)

`TriStateCheckbox`具有选中/未选中/不确定三种状态

```kotlin
@Composable
fun TriStateCheckbox(
    // 选中状态
    state: ToggleableState,
    // 点击回调
    onClick: (() -> Unit)?,
    // 修饰符
    modifier: Modifier = Modifier,
    // 复选按钮使能
    enabled: Boolean = true,
    // 复选按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 复选按钮的各种颜色
    colors: CheckboxColors = CheckboxDefaults.colors()
): Unit
```

```kotlin
Column {
    // define dependent checkboxes states
    val (state, onStateChange) = remember { mutableStateOf(true) }
    val (state2, onStateChange2) = remember { mutableStateOf(true) }

    // TriStateCheckbox state reflects state of dependent checkboxes
    val parentState = remember(state, state2) {
        if (state && state2) ToggleableState.On
        else if (!state && !state2) ToggleableState.Off
        else ToggleableState.Indeterminate
    }
    // click on TriStateCheckbox can set state for dependent checkboxes
    val onParentClick = {
        val s = parentState != ToggleableState.On
        onStateChange(s)
        onStateChange2(s)
    }

    TriStateCheckbox(
        state = parentState,
        onClick = onParentClick,
        colors = CheckboxDefaults.colors(
            checkedColor = MaterialTheme.colors.primary
        )
    )
    Spacer(Modifier.size(25.dp))
    Column(Modifier.padding(10.dp, 0.dp, 0.dp, 0.dp)) {
        Checkbox(state, onStateChange)
        Spacer(Modifier.size(25.dp))
        Checkbox(state2, onStateChange2)
    }
}
```


