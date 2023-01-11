---
title: Icon加载资源图片显示黑色
date: 2022-12-29 17:20:57
tags: Icon
categories:  Jetpack Compose
copyright: true
typora-root-url: ./Icon加载资源图片显示黑色
---

# Icon加载资源图片显示黑色

Painter：

```kotlin
//获取图片资源，R.drawable.xx或者R.mipmap.xx
Icon(painter = painterResource(id = R.mipmap.head_icon), null)
```

效果如图：

![img](548a85e3dc764c498a948f83680ca471.png)

Icon加载资源图片显示黑色没有加载出图片？别慌，因为默认的tint模式是AmbientContentColor.current，我们需要去掉它默认的着色模式，所以需要将tint的属性设置为Color.Unspecified

```kotlin
//获取图片资源，R.drawable.xx或者R.mipmap.xx
Icon(painter = painterResource(id = R.mipmap.head_icon), null, tint = Color.Unspecified)
```

此时图片的显示效果就正常了

![img](b801a3f852ce44f49c2d1deb3be1383f.png)

