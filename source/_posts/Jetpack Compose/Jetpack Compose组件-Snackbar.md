---
title: Jetpack Compose组件-Snackbar
date: 2023-01-13 10:31:28
tags: Snackbar
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-Snackbar

## Snackbar

将很多的组件摆放在这个平面之上，我们可以设置这个平面的边框，圆角，颜色等等。

Surface实际上是Box的重新包装。

```kotlin
@Composable
fun Snackbar(
    // Snackbar 数据, 来自SnackbarHost的回调
    snackbarData: SnackbarData,
    // 修饰符
    modifier: Modifier = Modifier,
    // 动作放在单独一行，在长文本时设置为true，action位于右下角，否则在右侧
    actionOnNewLine: Boolean = false,
    // 形状
    shape: Shape = MaterialTheme.shapes.small,
    // 背景颜色
    backgroundColor: Color = SnackbarDefaults.backgroundColor,
    // 内容颜色
    contentColor: Color = MaterialTheme.colors.surface,
    // 动作颜色
    actionColor: Color = SnackbarDefaults.primaryActionColor,
    // 高度(阴影)
    elevation: Dp = 6.dp
): Unit

@Composable
fun Snackbar(
    // 修饰符
    modifier: Modifier = Modifier,
    // 动作回调
    action: (@Composable () -> Unit)? = null,
    // 动作放在单独一行，在长文本时设置为true，action位于右下角，否则在右侧
    actionOnNewLine: Boolean = false,
    // 形状
    shape: Shape = MaterialTheme.shapes.small,
    // 背景颜色
    backgroundColor: Color = SnackbarDefaults.backgroundColor,
    // 内容颜色
    contentColor: Color = MaterialTheme.colors.surface,
    // 高度(阴影)
    elevation: Dp = 6.dp,
    content: @Composable () -> Unit
): Unit
```

## SnackbarHost

```kotlin
@Composable
fun SnackbarHost(
	// 状态
    hostState: SnackbarHostState,
    // 修饰符
    modifier: Modifier = Modifier,
    // Snackbar
    snackbar: @Composable (SnackbarData) -> Unit = { Snackbar(it) }
): Unit
```

```kotlin
val scaffoldState = rememberScaffoldState()
val scope = rememberCoroutineScope()
Scaffold(
    scaffoldState = scaffoldState,
    snackbarHost = {
        // reuse default SnackbarHost to have default animation and timing handling
        SnackbarHost(it) { data ->
            // custom snackbar with the custom border
            Snackbar(
                modifier = Modifier.border(2.dp, MaterialTheme.colors.secondary),
                snackbarData = data,
                actionOnNewLine = false
            )
        }
    },
    floatingActionButton = {
        var clickCount by remember { mutableStateOf(0) }
        ExtendedFloatingActionButton(
            text = { Text("Show snackbar") },
            onClick = {
                scope.launch {
                    scaffoldState.snackbarHostState.showSnackbar(
                        "Loooooooooooooooooooong Snackbar # ${++clickCount}",
                        actionLabel = "action",
                        duration = SnackbarDuration.Indefinite
                    )
                }
            }
        )
    },
    content = { innerPadding ->
        Text(
            text = "Custom Snackbar Demo",
            modifier = Modifier
                .padding(innerPadding)
                .fillMaxSize()
                .wrapContentSize()
        )
    }
)
```

