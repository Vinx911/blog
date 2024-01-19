---
title: Jetpack Compose 使用技巧 - HorizontalPager
date: 2023-06-06 11:22:44
id: 98
tags: HorizontalPager
categories: Jetpack Compose
copyright: true
---

# Jetpack Compose 使用技巧 - HorizontalPager

### 1. HorizontalPager 页面变化回调

```kotlin
fun PhotoPagerScreen(
    images: List<Image>,
    page: Int,
    onPageChange: (Int) -> Unit
) {
    val pagerState = rememberPagerState()
    LaunchedEffect(pagerState) {
        snapshotFlow { pagerState.currentPage }.collect { page ->
            onPageChange(page)
        }
    }
    HorizontalPager(count = images.size, state = pagerState) { page ->
        // load composable
    }
    LaunchedEffect(page) {
        pagerState.scrollToPage(page)
    }
}
```



## 2. HorizontalPager 无限循环(伪)

```kotlin
@SuppressLint("UnusedMaterial3ScaffoldPaddingParameter")
@OptIn(ExperimentalFoundationApi::class, ExperimentalMaterial3Api::class)
@Composable
fun MainScreen() {
    val images = remember {
        mutableStateListOf(
            "https://images.pexels.com/photos/1450082/pexels-photo-1450082.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/1366919/pexels-photo-1366919.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/1743165/pexels-photo-1743165.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/2437291/pexels-photo-2437291.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/1670187/pexels-photo-1670187.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/1266810/pexels-photo-1266810.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/2832034/pexels-photo-2832034.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/1743366/pexels-photo-1743366.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/4555468/pexels-photo-4555468.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
            "https://images.pexels.com/photos/4552789/pexels-photo-4552789.jpeg?auto=compress&cs=tinysrgb&fm=jpg&w=640&h=960",
        )
    }
    
    val imageCount = images.count()    // 图片的个数
    val dummyPageCount = Int.MAX_VALUE // 将页面个数设置为Int最大值，所以理论上是有尽头的

    val pagerState = rememberPagerState(
        // 起始位置为中间，以便左右都能翻页
        initialPage = (dummyPageCount / imageCount / 2) * imageCount
    )
    val matrix = remember { ColorMatrix() }

    Scaffold(modifier = Modifier.padding(vertical = 48.dp)) {
        HorizontalPager(
            pageCount = dummyPageCount, // 设置页面个数
            state = pagerState
        ) { dummyIndex ->
            // 实际页面索引
            val index = dummyIndex % imageCount
           // 实际当前页面索引
            val currentIndex = pagerState.currentPage % imageCount
            val pagerOffset = (currentIndex - index) + pagerState.currentPageOffsetFraction
            val imageSize by animateFloatAsState(
                targetValue = if (pagerOffset != 0.0f) 0.75f else 1f,
                animationSpec = tween(durationMillis = 300)
            )

            LaunchedEffect(key1 = imageSize) {
                if (pagerOffset != 0.0f) {
                    matrix.setToSaturation(0f)
                } else {
                    matrix.setToSaturation(1f)
                }
            }

            AsyncImage(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(16.dp)
                    .graphicsLayer {
                        scaleX = imageSize
                        scaleY = imageSize
                    }
                    .clip(RoundedCornerShape(16.dp)),
                model = ImageRequest.Builder(LocalContext.current)
                    .data(images[index])
                    .build(),
                contentDescription = "Image",
                contentScale = ContentScale.Crop,
                colorFilter = ColorFilter.colorMatrix(matrix)
            )
        }
    }
}
```

