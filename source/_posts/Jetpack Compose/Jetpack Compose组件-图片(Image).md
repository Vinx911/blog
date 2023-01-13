---
title: Jetpack Compose组件-图片(Image)
date: 2023-01-12 11:21:43
tags: Image
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-图片(Image)

## 图片(Image)

```kotlin
@Composable
@NonRestartableComposable
fun Image(
    // 位图对象，可以显示 JPG，PNG 等格式的图标
    bitmap: ImageBitmap,
    // 无障碍功能, 图片描述信息
    contentDescription: String?, 
    // 修饰符
    modifier: Modifier = Modifier, 
    // 对齐方式
    alignment: Alignment = Alignment.Center,
    // 图片缩放方式
    contentScale: ContentScale = ContentScale.Fit,
    // 图片透明度
    alpha: Float = DefaultAlpha,
    // 颜色滤镜
    colorFilter: ColorFilter? = null,
    // 缩放时采样算法
    filterQuality: FilterQuality = DefaultFilterQuality
): Unit

@Composable
fun Image(
    // 画笔，可以使用画笔在 Canvas 上直接绘制图标，可以通过 res/ 下的图片资源来设置图标
    painter: Painter,
    // 无障碍功能, 图片描述信息
    contentDescription: String?, 
    // 修饰符
    modifier: Modifier = Modifier,
    // 对齐方式
    alignment: Alignment = Alignment.Center,
    // 图片缩放方式
    contentScale: ContentScale = ContentScale.Fit,
    // 图片透明度
    alpha: Float = DefaultAlpha,
    // 颜色滤镜
    colorFilter: ColorFilter? = null
): Unit

@Composable
@NonRestartableComposable
fun Image(
    // 矢量图对象，可以显示 SVG 格式的图标
    imageVector: ImageVector,
    // 无障碍功能, 图片描述信息
    contentDescription: String?, 
    // 修饰符
    modifier: Modifier = Modifier,
    // 对齐方式
    alignment: Alignment = Alignment.Center,
    // 图片缩放方式
    contentScale: ContentScale = ContentScale.Fit,
    // 图片透明度
    alpha: Float = DefaultAlpha,
    // 颜色滤镜
    colorFilter: ColorFilter? = null
): Unit
```

```kotlin
Image(
        painter = painterResource(id = R.drawable.newbanner4),
        contentDescription = null,
        contentScale = ContentScale.Inside,
        colorFilter = ColorFilter.tint(Color.Red, blendMode = BlendMode.Color)
    )
```

## 图片相关库

```kotlin
// Coil
implementation "io.coil-kt:coil-compose:2.2.2"
// Coil SVG
implementation "io.coil-kt:coil-svg:2.2.2"
// 图片放大缩小
implementation "com.github.skydoves:landscapist-coil:1.4.5"
```

