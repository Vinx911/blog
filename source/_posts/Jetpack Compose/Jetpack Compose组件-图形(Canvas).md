---
title: Jetpack Compose组件-图形(Canvas)
date: 2023-01-12 12:52:25
id: 62
tags: Canvas
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-图形(Canvas)

## 图形(Canvas)

`Canvas`需要指定大小尺寸，使用`Modifier.size`指定精确大小或者使用`Modifier.fillMaxSize`指定相对于父项的大小。

```kotlin
@Composable
fun Canvas(modifier: Modifier, onDraw: DrawScope.() -> Unit): Unit

@ExperimentalFoundationApi
@Composable
fun Canvas(modifier: Modifier, contentDescription: String, onDraw: DrawScope.() -> Unit): Unit
```

```kotlin
Canvas(modifier = Modifier.size(100.dp)) {
    drawRect(Color.Magenta)
    inset(10.0f) {
        drawLine(
            color = Color.Red,
            start = Offset.Zero,
            end = Offset(size.width, size.height),
            strokeWidth = 5.0f
        )
    }
}
```

## DrawScope

### 画点(drawPoints)

```kotlin
fun drawPoints(
    // 点列表
    points: List<Offset>,
    // 点模式，Points(画点), Lines(每两个点画一条线，奇数个点忽略最后一个), Polygon(将点的整个序列画成一条直线)
    pointMode: PointMode,
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 笔划宽度
    strokeWidth: Float = Stroke.HairlineWidth,
    // 线段末端的处理, Butt(没有延伸), Round(半圆延伸), Square(半方形延伸)
    cap: StrokeCap = StrokeCap.Butt,
    // 可选效果或模式
    pathEffect: PathEffect? = null,
    // 透明度
    alpha: Float = 1.0f,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画线(drawLine)

```kotlin
fun drawLine(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 起始点
    start: Offset,
    // 结束点
    end: Offset,
    // 笔划宽度
    strokeWidth: Float = Stroke.HairlineWidth,
    // 线段末端的处理, Butt(没有延伸), Round(半圆延伸), Square(半方形延伸)
    cap: StrokeCap = Stroke.DefaultCap,
    // 可选效果或模式，Android 10 以下dashPathEffect不起作用
    pathEffect: PathEffect? = null,
    // 透明度
    alpha: Float = 1.0f,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画路径(drawPath)

```kotlin
fun drawPath(
    // 路径
    path: Path,
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit

```

### 绘制矩形(drawRect)

```kotlin
fun drawRect(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 尺寸
    size: Size = this.size.offsetSize(topLeft),
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 绘制圆角矩形(drawRoundRect)

```kotlin
fun drawRoundRect(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 尺寸
    size: Size = this.size.offsetSize(topLeft),
    // 圆角半径
    cornerRadius: CornerRadius = CornerRadius.Zero,
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画圆(drawCircle)

```kotlin
fun drawCircle(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 半径
    radius: Float = size.minDimension / 2.0f,、
    // 中心点
    center: Offset = this.center,
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画椭圆(drawOval)

```kotlin
fun drawOval(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 尺寸
    size: Size = this.size.offsetSize(topLeft),
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画弧线(drawArc)

```kotlin
fun drawArc(
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 起始角度
    startAngle: Float,
    // 顺时针方向，弧线角度大小
    sweepAngle: Float,
    // true包含中心点为扇形
    useCenter: Boolean,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 尺寸
    size: Size = this.size.offsetSize(topLeft),
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```

### 画图像(drawImage)

```kotlin
fun drawImage(
    // 图像
    image: ImageBitmap,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit

open fun drawImage(
    // 图像
    image: ImageBitmap,
    // 源图像位置偏移
    srcOffset: IntOffset = IntOffset.Zero,
    // 源图像大小
    srcSize: IntSize = IntSize(image.width, image.height),
    // 目标位置偏移
    dstOffset: IntOffset = IntOffset.Zero,
    // 目标大小
    dstSize: IntSize = srcSize,
    // 透明度
    alpha: Float = 1.0f,
    // 路径是否被描边或填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode,
    // 缩放并绘制到目标时采样算法
    filterQuality: FilterQuality = DefaultFilterQuality
): Unit
```

### 画文本(drawText)

```kotlin
@ExperimentalTextApi
fun DrawScope.drawText(
    // 测量和放置文本
    textMeasurer: TextMeasurer,
    // 要绘制的文本
    text: AnnotatedString,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 文本样式
    style: TextStyle = TextStyle.Default,
    // 溢出
    overflow: TextOverflow = TextOverflow.Clip,
    // 软换行
    softWrap: Boolean = true,
    // 最大行数
    maxLines: Int = Int.MAX_VALUE,
    // 占位符
    placeholders: List<AnnotatedString.Range<Placeholder>> = emptyList(),
    // 尺寸
    size: Size = Size.Unspecified
): Unit

@ExperimentalTextApi
fun DrawScope.drawText(
    // 测量和放置文本
    textMeasurer: TextMeasurer,
    // 要绘制的文本
    text: String,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 文本样式
    style: TextStyle = TextStyle.Default,
    // 溢出
    overflow: TextOverflow = TextOverflow.Clip,
    // 软换行
    softWrap: Boolean = true,
    // 最大行数
    maxLines: Int = Int.MAX_VALUE,
    // 尺寸
    size: Size = Size.Unspecified
): Unit

@ExperimentalTextApi
fun DrawScope.drawText(
    // 要绘制的文本布局
    textLayoutResult: TextLayoutResult,
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 左上角坐标
    topLeft: Offset = Offset.Zero,
    // 透明度
    alpha: Float = Float.NaN,
    // 文本的阴影效果
    shadow: Shadow? = null,
    // 下划线或删除线
    textDecoration: TextDecoration? = null,
    // 文本是否被描边或填充
    drawStyle: DrawStyle? = null
): Unit
```

### 画轮廓(drawOutline)

```kotlin
fun DrawScope.drawOutline(
    // 要绘制的轮廓
    outline: Outline,
    // 颜色/画刷
    color: Color, // brush: Brush,
    // 透明度
    alpha: Float = 1.0f,
    // 轮廓是描边还是填充
    style: DrawStyle = Fill,
    // 颜色过滤
    colorFilter: ColorFilter? = null,
    // 路径的混合算法
    blendMode: BlendMode = DefaultBlendMode
): Unit
```



### 属性

```
// 中心点
open val center: Offset
// 绘制上下文
val drawContext: DrawContext
// 布局方向
val layoutDirection: LayoutDirection
// 尺寸
open val size: Size
```

### drawIntoCanvas(在Canvas中绘制)

提供直接使用底层`Canvas`. 

```kotlin
inline fun DrawScope.drawIntoCanvas(block: (Canvas) -> Unit): Unit
```

### clipPath(剪辑路径)

```kotlin
inline fun DrawScope.clipPath(
    // 路径
    path: Path,
    // Difference: 从现有区域中减去新区域, Intersect: 将新区域与现有区域相交
    clipOp: ClipOp = ClipOp.Intersect,
    block: DrawScope.() -> Unit
): Unit
```

### clipRect(剪辑矩形)

```kotlin
inline fun DrawScope.clipRect(
    // 左侧
    left: Float = 0.0f,
    // 上方
    top: Float = 0.0f,
    // 右侧
    right: Float = size.width,    
    // 下方
    bottom: Float = size.height,
    // Difference: 从现有区域中减去新区域, Intersect: 将新区域与现有区域相交
    clipOp: ClipOp = ClipOp.Intersect,
    block: DrawScope.() -> Unit
): Unit
```

### rotate(旋转)

```kotlin
// 按照角度旋转
inline fun DrawScope.rotate(
    // 顺时针旋转角度
    degrees: Float, 
    // 枢轴点的坐标，默认为坐标空间的中心
    pivot: Offset = center, 
    block: DrawScope.() -> Unit
): Unit
// 按照弧度旋转
inline fun DrawScope.rotateRad(
    // 顺时针旋转弧度
    radians: Float, 
    // 枢轴点的坐标，默认为坐标空间的中心
    pivot: Offset = center, 
    block: DrawScope.() -> Unit
): Unit
```

### scale(缩放)

```kotlin
inline fun DrawScope.scale(
    // X方向缩放比例
    scaleX: Float,
    // Y方向缩放比例
    scaleY: Float,
    // 缩放中心点
    pivot: Offset = center,
    block: DrawScope.() -> Unit
): Unit

inline fun DrawScope.scale(
    // X/Y方向缩放比例
    scale: Float, 
    // 缩放中心点
    pivot: Offset = center, 
    block: DrawScope.() -> Unit
): Unit
```

### inset(调整边界)

可以使用 *DrawScope*.*inset*() 函数来调整当前作用域的默认参数，以更改绘图边界并相应地转换绘图

```kotlin
inline fun DrawScope.inset(
    // 插入左边界的像素数
    left: Float,
    // 插入上边界的像素数
    top: Float,
    // 插入右边界的像素数
    right: Float,
    // 插入下边界的像素数
    bottom: Float,
    block: DrawScope.() -> Unit
): Unit

inline fun DrawScope.inset(
    // 插入左、上、右和下边界的像素数
    inset: Float, 
    block: DrawScope.() -> Unit
): Unit

inline fun DrawScope.inset(
    // 插入水平方向的像素数
    horizontal: Float = 0.0f,
    // 插入垂直方向的像素数
    vertical: Float = 0.0f,
    block: DrawScope.() -> Unit
): Unit
```

### translate(平移坐标)

```kotlin
inline fun DrawScope.translate(
    left: Float = 0.0f, 
    top: Float = 0.0f, 
    block: DrawScope.() -> Unit
): Unit
```



#### withTransform(组合转换)

如果同时想要有平移跟旋转的效果，可以使用withTransform。

```kotlin
inline fun DrawScope.withTransform(
    transformBlock: DrawTransform.() -> Unit, 
    drawBlock: DrawScope.() -> Unit
): Unit
```

```kotlin
Canvas(modifier = Modifier.fillMaxSize()){
    val canvasSize = size
    val canvasWidth = size.width
    val canvasHeight = size.height

    withTransform({
        translate(left = canvasWidth/5F)
        rotate(degrees=45F)
    }) {
        drawRect(
            color = Color.Gray,
            topLeft = Offset(x = canvasWidth / 3F, y = canvasHeight / 3F),
            size = canvasSize / 3F
        )
    }
}
```

