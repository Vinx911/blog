---
title: Modifier 互斥选择（Selectable）
date: 2022-12-30 12:12:39
tags: Draggable
categories:  Jetpack Compose
copyright: false

---

# Modifier 互斥选择（Selectable）

`selectableGroup` 使用此修饰符将Tabs 或 RadioButtons 等项目列表组合在一起。

`selectable` 修饰符指示此控件可选中

```kotlin
fun Modifier.selectableGroup()

fun Modifier.selectable(
    selected: Boolean,
    enabled: Boolean = true,
    role: Role? = null,
    onClick: () -> Unit
)

fun Modifier.selectable(
    selected: Boolean,
    interactionSource: MutableInteractionSource,
    indication: Indication?,
    enabled: Boolean = true,
    role: Role? = null,
    onClick: () -> Unit
)
```

- selected: 此项是否在互斥集中被选中
- enabled: 启用选中
- interactionSource: 选中时发出此状态
- indication: 按下修改后的元素时显示的指示。设置为`null`不显示任何指示。
- role: 用户界面元素的类型。
- onClick: 单击此项目时调用的回调

```kotlin
@Composable
fun SelectableDemo() {
    val option1 = Color.Red
    val option2 = Color.Blue
    var selectedOption by remember { mutableStateOf(0) }
    Column {
        Text("Selected: $selectedOption")
        Column {
            listOf(
                option1,
                option2,
                option1,
                option2,
                option1,
                option2,
                option1,
                option2
            ).forEachIndexed { index, color ->
                val selected = selectedOption == index
                Box(
                    Modifier
                        .size(100.dp)
                        .background(color = color)
                        .selectable(
                            selected = selected,
                            onClick = { selectedOption = index }
                        )
                )
            }
        }
    }
}
```

