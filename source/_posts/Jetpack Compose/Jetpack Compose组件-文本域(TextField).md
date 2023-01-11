---
title: Jetpack Compose组件-文本域(TextField)
date: 2023-01-11 10:32:11
tags: TextField
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-文本域(TextField)

## 基础文本域(BasicTextField)

```kotlin
@Composable
fun BasicTextField(
    // 文本域的值
    value: TextFieldValue,
    // 文本改变回调
    onValueChange: (TextFieldValue) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 组件使能启用
    enabled: Boolean = true,
    // 组件只读
    readOnly: Boolean = false,
    // 文本样式
    textStyle: TextStyle = TextStyle.Default,
    // 键盘类型，如数字键盘
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
    // 功能键事件监听
    keyboardActions: KeyboardActions = KeyboardActions.Default,
    // 单行文本
    singleLine: Boolean = false,
    // 最大行数
    maxLines: Int = if (singleLine) 1 else Int.MAX_VALUE,
    // 最小行数，1.4新增
    minLines: Int = 1,
    // 虚拟转换，比如使用PasswordVisualTransformation将输入字符转换为密码•••••
    visualTransformation: VisualTransformation = VisualTransformation.None,
    // 计算新文本布局时执行的回调
    onTextLayout: (TextLayoutResult) -> Unit = {},
    // 状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 光标画刷
    cursorBrush: Brush = SolidColor(Color.Black),
    // 文本域的装饰
    decorationBox: @Composable (@Composable innerTextField: () -> Unit) -> Unit = @Composable { innerTextField -> innerTextField() }
): Unit

@Composable
fun BasicTextField(
    // 文本域的值
    value: String,
    // 其他参数同上 ...................
): Unit
```

### decorationBox示例

```kotlin
var value by rememberSaveable { mutableStateOf("initial value") }
BasicTextField(
    value = value,
    onValueChange = { value = it },
    decorationBox = { innerTextField ->
        Row(
            Modifier
                .background(Color.LightGray, RoundedCornerShape(percent = 30))
                .padding(16.dp)
        ) {
            Icon(Icons.Default.MailOutline, contentDescription = null)
            Spacer(Modifier.width(16.dp))
            innerTextField()
        }
    }
)
```



## 文本域(TextField)

TextField是基于BasicTextField遵循 Material Design 准则的文本域。

```kotlin
@Composable
fun TextField(
    // 文本域的值
    value: TextFieldValue,
    // 文本改变回调
    onValueChange: (TextFieldValue) -> Unit,
    // 修饰符
    modifier: Modifier = Modifier,
    // 组件使能启用
    enabled: Boolean = true,
    // 组件只读
    readOnly: Boolean = false,
    // 文本样式
    textStyle: TextStyle = LocalTextStyle.current,    
    // 文本域标签
    label: (@Composable () -> Unit)? = null,
    // 文本域空白时提示内容
    placeholder: (@Composable () -> Unit)? = null,
    // 文本域头部(左侧)图标
    leadingIcon: (@Composable () -> Unit)? = null,
    // 文本域尾部(右侧)图标
    trailingIcon: (@Composable () -> Unit)? = null,
    // 如果为true，默认情况下标签、底部指示器和尾随图标将以错误颜色显示
    isError: Boolean = false,
    // 虚拟转换，比如使用PasswordVisualTransformation将输入字符转换为密码•••••
    visualTransformation: VisualTransformation = VisualTransformation.None,
    // 键盘类型，如数字键盘
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
    // 功能键事件监听
    keyboardActions: KeyboardActions = KeyboardActions(),
    // 单行文本
    singleLine: Boolean = false,
    // 最大行数
    maxLines: Int = if (singleLine) 1 else Int.MAX_VALUE,
    // 最小行数，1.4新增
    minLines: Int = 1,
    // 状态源
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    // 文本域的形状
    shape: Shape = TextFieldDefaults.TextFieldShape,
    // 文本域的各种颜色
    colors: TextFieldColors = TextFieldDefaults.textFieldColors()
): Unit

@Composable
fun TextField(
    // 文本域的值
    value: String,
    // 其他参数同上 ...................
): Unit
```



### 轮廓线文本域(OutlinedTextField)

`OutlinedTextField`是有轮廓线的文本域。

```kotlin
@Composable
fun OutlinedTextField(
    // 参数与TextField一致，仅默认值不一样
): Unit
```

