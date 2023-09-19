---
title: Jetpack Compose组件-列表项(ListItem)
date: 2023-01-14 09:41:28
id: 74
tags: ListItem
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-列表项(ListItem)

## 列表项(ListItem)

ListItem(小列表项)类似设置界面中的菜单项，有单行/两行/三行文本三种样式，可添加左侧图标，可设置右侧内容，通常是Icon、CheckBox 或 Switch。

ListItem左侧图标和右侧内容默认为上方对齐，要想居中对齐需要将左侧图标大小(size + padding)设置为40dpx56dp，右侧内容高度(height+ padding)设置为56dp。

```kotlin
@Composable
@ExperimentalMaterialApi
fun ListItem(
    // 修饰符
    modifier: Modifier = Modifier,
    // 左侧图标
    icon: (@Composable () -> Unit)? = null,
    // 副文本
    secondaryText: (@Composable () -> Unit)? = null,
    // 副文本是否单行显示
    singleLineSecondaryText: Boolean = true,
    // 显示在主文本上面的文本
    overlineText: (@Composable () -> Unit)? = null,
    // 设置右侧内容，通常是Icon、CheckBox 或 Switch
    trailing: (@Composable () -> Unit)? = null,
    // 主文本
    text: @Composable () -> Unit
): Unit
```

```kotlin
var switched by remember { mutableStateOf(false) }
val onSwitchedChange: (Boolean) -> Unit = { switched = it }
Column(modifier = Modifier.background(Color.LightGray)) {
    ListItem(
        text = { Text("text") },
        icon = {
            Icon(
                imageVector = Icons.Default.BrokenImage,
                contentDescription = null,
                modifier = Modifier
                    .padding(vertical = 16.dp, horizontal = 8.dp)
                    .size(24.dp)
            )
        },
        secondaryText = {
            Text("secondaryText")
        },
        overlineText = {
            Text("overlineText")
        },
        trailing = {
            Switch(
                checked = switched,
                onCheckedChange = null,
                modifier = Modifier
                    .padding(16.dp)
                    .size(24.dp)
            )
        },
        modifier = Modifier
            .background(Color.White)
            .toggleable(
                role = Role.Switch,
                value = switched,
                onValueChange = onSwitchedChange
            )
    )
}
```


