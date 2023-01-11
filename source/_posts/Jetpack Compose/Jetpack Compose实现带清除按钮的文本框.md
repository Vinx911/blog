# Jetpack Compose实现带清除按钮的文本框

使用`trailingIcon`参数可在尾部添加一个自定义图标，并实现点击功能。

```kotlin
@Composable
fun TextFieldWithClean() {
    Column {
        var text by remember { mutableStateOf("") }
        val isVisible by remember { derivedStateOf { text.isNotBlank() } }

        OutlinedTextField(
            value = text,
            label = { Text("User Name") },
            onValueChange = { text = it },
            trailingIcon = {
                if (isVisible) {
                    IconButton(onClick = { text = "" }) {
                        Icon(
                            imageVector = Icons.Default.Clear, 
                            contentDescription = "Clear"
                        )
                    }
                }
            }
        )
    }
}
```

