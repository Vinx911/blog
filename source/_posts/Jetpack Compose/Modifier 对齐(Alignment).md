---
title: Modifier 对齐(Alignment)
date: 2022-12-30 12:12:39
id: 47
tags: Alignment
categories: Jetpack Compose
copyright: true
---

# Modifier 对齐(Alignment)

```kotlin
// 用于Box
fun Modifier.align(alignment: Alignment)
// 用于Row
fun Modifier.align(alignment: Alignment.Vertical)
fun Modifier.alignByBaseline() // 基线对齐
fun Modifier.alignBy(alignmentLine: HorizontalAlignmentLine)
fun Modifier.alignBy(alignmentLineBlock: (Measured) -> Int)
// 用于Column
fun Modifier.align(alignment: Alignment.Horizontal)
fun Modifier.alignBy(alignmentLine: VerticalAlignmentLine)
fun Modifier.alignBy(alignmentLineBlock: (Measured) -> Int)
```

- Alignment.Start
- Alignment.End
- Alignment.CenterHorizontally
- Alignment.Top
- Alignment.Bottom
- Alignment.CenterVertically
- Alignment.TopCenter
- Alignment.TopStart
- Alignment.TopEnd
- Alignment.Center
- Alignment.CenterStart
- Alignment.CenterEnd
- Alignment.BottomCenter
- Alignment.BottomStart
- Alignment.BottomEnd

