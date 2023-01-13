---
title: Jetpack Compose组件-单选按钮(RadioButton)
date: 2023-01-11 17:00:23
tags: RadioButton
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-单选按钮(RadioButton)

## 单选按钮(RadioButton)

```kotlin
@Composable
fun RadioButton(
    // 是否选中
    selected: Boolean,
    // 点击回调
    onClick: (() -> Unit)?,
    // 修饰符
    modifier: Modifier = Modifier,
    // 单选按钮使能
    enabled: Boolean = true,
    // 单选按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 单选按钮的各种颜色
    colors: RadioButtonColors = RadioButtonDefaults.colors()
): Unit
```

```kotlin
val radioOptions = listOf("Calls", "Missed", "Friends")
val (selectedOption, onOptionSelected) = remember { mutableStateOf(radioOptions[0]) }
// Note that Modifier.selectableGroup() is essential to ensure correct accessibility behavior
Column(Modifier.selectableGroup()) {
    radioOptions.forEach { text ->
        Row(
            Modifier
                .fillMaxWidth()
                .height(56.dp)
                .selectable(
                    selected = (text == selectedOption),
                    onClick = { onOptionSelected(text) },
                    role = Role.RadioButton
                )
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            RadioButton(
                selected = (text == selectedOption),
                onClick = null // null recommended for accessibility with screenreaders
            )
            Text(
                text = text,
                style = MaterialTheme.typography.body1.merge(),
                modifier = Modifier.padding(start = 16.dp)
            )
        }
    }
}
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

