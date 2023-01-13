---
title: Jetpack Compose组件-对话框(Dialog)
date: 2023-01-13 10:46:38
tags: Dialog
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-对话框(Dialog)

## 警告对话框(AlertDialog)

```kotlin
@Composable
fun AlertDialog(
    // 关闭请求回调，点击对话框外部或者按下返回按钮关闭对话框时调用
    onDismissRequest: () -> Unit,
    // 对话框按钮
    buttons: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 标题
    title: (@Composable () -> Unit)? = null,
    // 文本
    text: (@Composable () -> Unit)? = null,
    // 形状
    shape: Shape = MaterialTheme.shapes.medium,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.surface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 对话框属性
    properties: DialogProperties = DialogProperties()
): Unit

@Composable
fun AlertDialog(
    // 关闭请求回调，点击对话框外部或者按下返回按钮关闭对话框时调用
    onDismissRequest: () -> Unit,
    // 确认按钮
    confirmButton: @Composable () -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 关闭按钮
    dismissButton: (@Composable () -> Unit)? = null,
    // 标题
    title: (@Composable () -> Unit)? = null,
    // 文本
    text: (@Composable () -> Unit)? = null,
    // 形状
    shape: Shape = MaterialTheme.shapes.medium,
    // 背景颜色
    backgroundColor: Color = MaterialTheme.colors.surface,
    // 内容颜色
    contentColor: Color = contentColorFor(backgroundColor),
    // 对话框属性
    properties: DialogProperties = DialogProperties()
): Unit

```

```kotlin
val openDialog = remember { mutableStateOf(false)  }

Button(onClick = {
    openDialog.value = true
}) {
    Text("Click me")
}

if (openDialog.value) {
    AlertDialog(
        onDismissRequest = {
            openDialog.value = false
        },
        title = {
            Text(text = "Dialog Title")
        },
        text = {
            Text("Here is a text ")
        },
        confirmButton = {
            Button(
                onClick = {
                    openDialog.value = false
                }) {
                Text("This is the Confirm Button")
            }
        },
        dismissButton = {
            Button(
                onClick = {
                    openDialog.value = false
                }) {
                Text("This is the dismiss Button")
            }
        }
    )
}
```

## 对话框(Dialog)

`AlertDialog` 在一些情况下有可能还是无法满足我们的业务要求，这时候我们就可以使用更底层的一个 @Composable 函数 —— Dialog

```kotlin
@Composable
fun Dialog(
	// 试图关闭对话框时执行
    onDismissRequest: () -> Unit,
    // 对话框属性
    properties: DialogProperties = DialogProperties(),
    content: @Composable () -> Unit
): Unit
```

```kotlin
var flag by remember{ mutableStateOf(false) }
Box(
    modifier = Modifier.fillMaxSize(),
    contentAlignment = Alignment.Center
) {
    Button(
        onClick = { flag = true }
    ) {
        Text("弹窗")
    }
}
if(flag) {
    Dialog(
        onDismissRequest = { flag = false }
    ) {
        Box(
            modifier = Modifier
                .size(300.dp)
                .background(Color.White),
            contentAlignment = Alignment.Center
        ) {
            Column {
                LinearProgressIndicator()
                Text("加载中 ing...")
            }
        }
    }
}
```

### 弹出窗口(Popup)

```kotlin
@Composable
fun Popup(
	// 对其方式
    alignment: Alignment = Alignment.TopStart,
    // 偏移
    offset: IntOffset = IntOffset(0, 0),
	// 试图关闭窗口时执行
    onDismissRequest: (() -> Unit)? = null,
    // 弹出窗口属性
    properties: PopupProperties = PopupProperties(),
    content: @Composable () -> Unit
): Unit

@Composable
fun Popup(
	// 弹出窗口的屏幕位置
    popupPositionProvider: PopupPositionProvider,
	// 试图关闭窗口时执行
    onDismissRequest: (() -> Unit)? = null,
    // 弹出窗口属性
    properties: PopupProperties = PopupProperties(),
    content: @Composable () -> Unit
): Unit
```

```kotlin
Box {
    val popupWidth = 200.dp
    val popupHeight = 50.dp
    val cornerSize = 16.dp

    Popup(alignment = Alignment.Center) {
        // Draw a rectangle shape with rounded corners inside the popup
        Box(
            Modifier
                .size(popupWidth, popupHeight)
                .background(Color.White, RoundedCornerShape(cornerSize))
        )
    }
}
```

