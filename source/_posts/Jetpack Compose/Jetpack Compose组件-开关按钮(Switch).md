---
title: Jetpack Compose组件-开关按钮(Switch)
date: 2023-01-11 17:03:48
tags: Checkbox
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-开关按钮(Switch)

## 开关按钮(Switch)

```kotlin
@Composable
fun Switch(
     // 是否选中
    checked: Boolean,
    // 选中状态改变回调
    onCheckedChange: ((Boolean) -> Unit)?,
    // 修饰符
    modifier: Modifier = Modifier,
    // 开关按钮使能
    enabled: Boolean = true,
    // 开关按钮的状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 开关按钮的各种颜色
    colors: SwitchColors = SwitchDefaults.colors()
): Unit
```

```kotlin
val checkedState = remember { mutableStateOf(true) }
Switch(
    checked = checkedState.value,
    onCheckedChange = { checkedState.value = it }
)

var pineappleOnPizza by remember { mutableStateOf(true) }

Row(
    Modifier
        .padding(16.dp)
        .toggleable(
            role = Role.Switch,
            value = pineappleOnPizza,
            onValueChange = { pineappleOnPizza = it },
        )
) {
    Switch(checked = pineappleOnPizza, onCheckedChange = null)
    Spacer(Modifier.width(8.dp))
    Text("Pineapple on pizza?")
}
```


