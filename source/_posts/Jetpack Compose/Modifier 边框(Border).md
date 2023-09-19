---
title: Modifier 边框(Border)
date: 2023-01-03 09:29:39
id: 49
tags: Border
categories: Jetpack Compose
copyright: true
---

# Modifier 边框(Border)

```kotlin
fun Modifier.border(border: BorderStroke, shape: Shape)
fun Modifier.border(width: Dp, color: Color, shape: Shape)
fun Modifier.border(width: Dp, brush: Brush, shape: Shape)
```



```kotlin
Column {
    Text(
        "Text with  square border",
        modifier = Modifier
        .border(4.dp, Color.Magenta,
                shape = CutCornerShape(8.dp))
        .padding(10.dp)
    )

    Text(
        "Text with gradient border",
        modifier = Modifier
        .border(
            border = BorderStroke(2.dp, Color.Blue),
            shape = CutCornerShape(8.dp)
        )
        .padding(10.dp)
    )

    val gradientBrush = Brush.horizontalGradient(
        colors = listOf(Color.Red, Color.Blue, Color.Green),
        startX = 0.0f,
        endX = 500.0f,
        tileMode = TileMode.Repeated
    )

    Text(
        "Text with gradient border",
        modifier = Modifier
        .border(width = 2.dp, brush = gradientBrush, shape = CircleShape)
        .padding(10.dp)
    )
}
```

