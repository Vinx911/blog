---
title: Jetpack Compose 禁用硬件加速
date: 2023-01-12 15:24:45
id: 63
tags: 硬件加速
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose 禁用硬件加速

Compose 默认开启硬件加速，这会导致一些绘图操作在低版本无效，此时需要关闭硬件加速。

以下代码实现了一个软件层，`SoftwareLayer`包裹的代码将关闭硬件加速。

```kotlin
@Composable
fun SoftwareLayer(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit,
) {
    AndroidView(
        factory = { context ->
            ComposeView(context).apply {
                setLayerType(View.LAYER_TYPE_SOFTWARE, null)
            }
        },
        update = { composeView ->
            composeView.setContent(content)
        },
        modifier = modifier,
    )
}
```

使用

```kotlin
SoftwareLayerComposable(Modifier) {
    // your view
}
```

