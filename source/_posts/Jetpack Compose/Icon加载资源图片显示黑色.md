---
title: Icon加载资源图片显示黑色
date: 2022-12-29 17:20:57
id: 42
tags: Icon
categories: Jetpack Compose
copyright: true
---

# Icon加载资源图片显示黑色

Painter：

```kotlin
//获取图片资源，R.drawable.xx或者R.mipmap.xx
Icon(painter = painterResource(id = R.mipmap.head_icon), null)
```

效果如图：

![](Icon加载资源图片显示黑色/assets/image-20230918113323559.png)

Icon加载资源图片显示黑色没有加载出图片？别慌，因为默认的tint模式是AmbientContentColor.current，我们需要去掉它默认的着色模式，所以需要将tint的属性设置为Color.Unspecified

```kotlin
//获取图片资源，R.drawable.xx或者R.mipmap.xx
Icon(painter = painterResource(id = R.mipmap.head_icon), null, tint = Color.Unspecified)
```

此时图片的显示效果就正常了

![](Icon加载资源图片显示黑色/assets/image-20230918113335230.png)

