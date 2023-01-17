---
title: Jetpack Compose组件-左右滑动删除(SwipeToDismiss)
date: 2023-01-13 17:22:36
tags: SwipeToDismiss
categories: Jetpack Compose
copyright: true
---

#  Jetpack Compose组件-左右滑动删除(SwipeToDismiss)

## 左右滑动删除(SwipeToDismiss)

`Divider`是将列表和布局中的内容分组的细线。

```kotlin
@Composable
@ExperimentalMaterialApi
fun SwipeToDismiss(
    // 滑动状态
    state: DismissState,
    // 修饰符
    modifier: Modifier = Modifier,
    // 滑动的方向
    directions: Set<DismissDirection> = setOf(EndToStart, StartToEnd),
    // 滑动阈值
    dismissThresholds: (DismissDirection) -> ThresholdConfig = {
        FixedThreshold(DISMISS_THRESHOLD)
    },
    // 背景
    background: @Composable RowScope.() -> Unit,
    // 删除的内容
    dismissContent: @Composable RowScope.() -> Unit
): Unit
```

```kotlin
var unread by remember { mutableStateOf(false) }
val dismissState = rememberDismissState(
    confirmStateChange = {
        if (it == DismissValue.DismissedToEnd) unread = !unread
        false
    }
)
SwipeToDismiss(
    state = dismissState,
    modifier = Modifier.padding(vertical = 4.dp),
    dismissThresholds = {
        FixedThreshold(20.dp)
    },
    directions = setOf(DismissDirection.StartToEnd, DismissDirection.EndToStart),
    background = {
        val direction = dismissState.dismissDirection ?: return@SwipeToDismiss
        val color by animateColorAsState(
            when (dismissState.targetValue) {
                DismissValue.Default -> Color.LightGray
                DismissValue.DismissedToEnd -> Color.Green
                DismissValue.DismissedToStart -> Color.Red
            }
        )
        val alignment = when (direction) {
            DismissDirection.StartToEnd -> Alignment.CenterStart
            DismissDirection.EndToStart -> Alignment.CenterEnd
        }
        val icon = when (direction) {
            DismissDirection.StartToEnd -> Icons.Default.Done
            DismissDirection.EndToStart -> Icons.Default.Delete
        }
        val scale by animateFloatAsState(
            if (dismissState.targetValue == DismissValue.Default) 0.75f else 1f
        )

        Box(
            Modifier
                .fillMaxSize()
                .background(color)
                .padding(horizontal = 20.dp),
            contentAlignment = alignment
        ) {
            Icon(
                icon,
                contentDescription = "Localized description",
                modifier = Modifier
                    .scale(scale)
                    .clickable {
                        Log.d("1234", "SwipeToDismiss DismissedToStart")
                    }
            )
        }
    },
    dismissContent = {
        Card(
            elevation = animateDpAsState(
                if (dismissState.dismissDirection != null) 4.dp else 0.dp
            ).value
        ) {
            ListItem(
                text = {
                    Text("SwipeToDismiss", fontWeight = if (unread) FontWeight.Bold else null)
                },
                secondaryText = { Text("Swipe me left or right!") }
            )
        }
    }
)
```

