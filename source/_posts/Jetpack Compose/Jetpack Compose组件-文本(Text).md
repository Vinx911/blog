---
title: Jetpack Compose组件-文本(Text)
date: 2023-01-11 10:24:54
id: 52
tags: Text
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-文本(Text)

## 基础文本(BasicText)

```kotlin
@Composable
fun BasicText(
    // 要显示的文本
    text: AnnotatedString,
    // 修饰符
    modifier: Modifier = Modifier,
    // 文本的样式配置，如颜色、字体、行高等
    style: TextStyle = TextStyle.Default,
    // 计算新文本布局时执行的回调
    onTextLayout: (TextLayoutResult) -> Unit = {},
    // 溢出
    overflow: TextOverflow = TextOverflow.Clip,
    // 软换行
    softWrap: Boolean = true,
    // 最大行数
    maxLines: Int = Int.MAX_VALUE,
    // 最小行数, 1.4新增
    minLines: Int = 1,
    // 可替换文本特定范围的可组合项
    inlineContent: Map<String, InlineTextContent> = mapOf()
): Unit

@Composable
fun BasicText(
    // 要显示的文本
    text: String,
    // 其他参数同上 ...................
): Unit
```

## 文本(Text)

Text是基于BasicText遵循 Material Design 准则的文本，

```kotlin
@Composable
fun Text(
    // 要显示的文本
    text: AnnotatedString,
    // 修饰符
    modifier: Modifier = Modifier,
    // 文本颜色
    color: Color = Color.Unspecified,
    // 字体大小
    fontSize: TextUnit = TextUnit.Unspecified,
    // 字体样式
    fontStyle: FontStyle? = null,
    // 字体粗细
    fontWeight: FontWeight? = null,
    // 字体
    fontFamily: FontFamily? = null,
    // 文字之间的空白
    letterSpacing: TextUnit = TextUnit.Unspecified,
    // 文本上绘制的删除线/下划线
    textDecoration: TextDecoration? = null,
    // 文本对齐
    textAlign: TextAlign? = null,
    // 行高
    lineHeight: TextUnit = TextUnit.Unspecified,
    // 溢出
    overflow: TextOverflow = TextOverflow.Clip,
    // 软换行
    softWrap: Boolean = true,
    // 最大行数
    maxLines: Int = Int.MAX_VALUE,
    // 最小行数, 1.4新增
    minLines: Int = 1,
    inlineContent: Map<String, InlineTextContent> = mapOf(),
    // 计算新文本布局时执行的回调
    onTextLayout: (TextLayoutResult) -> Unit = {},
    // 文本的样式配置，如颜色、字体、行高等
    style: TextStyle = LocalTextStyle.current
): Unit

@Composable
fun Text(
    // 要显示的文本
    text: String,
    // 其他参数同上 ...................
): Unit
```



## 可点击文本(ClickableText)

`onClick`回调的`offset`参数为被点击文字的位置，可通过`offset`获取对应的AnnotatedString，然后执行下一步操作。

```kotlin
@Composable
fun ClickableText(
    // 要显示的文本
    text: AnnotatedString,
    // 修饰符
    modifier: Modifier = Modifier,
    // 文本的样式配置，如颜色、字体、行高等
    style: TextStyle = TextStyle.Default,
    // 软换行
    softWrap: Boolean = true,
    // 溢出
    overflow: TextOverflow = TextOverflow.Clip,
    // 最大行数
    maxLines: Int = Int.MAX_VALUE,
    // 计算新文本布局时执行的回调
    onTextLayout: (TextLayoutResult) -> Unit = {},
    // 点击回调
    onClick: (Int) -> Unit
): Unit
```



## 可选择文本(SelectionContainer)

使用`SelectionContainer`包裹文本，可使文本可选中。如想排除部分文本，使用`DisableSelection`包裹。

```kotlin
SelectionContainer {
    Column {
        Text("This text is selectable")
        Text("This one too")
        Text("This one as well")
        DisableSelection {
            Text("But not this one")
            Text("Neither this one")
        }
        Text("But again, you can select this one")
        Text("And this one too")
    }
}
```

