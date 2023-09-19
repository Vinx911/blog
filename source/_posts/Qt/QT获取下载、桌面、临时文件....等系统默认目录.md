---
title: QT获取下载、桌面、临时文件....等系统默认目录
date: 2023-05-19 11:50:13
id: 87
tags: Qt
categories: Qt
copyright: true
---

# QT获取下载、桌面、临时文件....等系统默认目录

## **头文件:**

```cpp
#include <QStandardPaths>
```

## **示例代码:**

```cpp
    qDebug()<<"系统字体目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::FontsLocation);
    qDebug()<<"系统桌面目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::DesktopLocation);
    qDebug()<<"用户文档目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::DocumentsLocation);
    qDebug()<<"用户音乐目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::MusicLocation);
    qDebug()<<"用户图片目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::PicturesLocation);
    qDebug()<<"系统临时文件目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::TempLocation);
    qDebug()<<"系统缓存目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::CacheLocation);
    qDebug()<<"系统下载目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::DownloadLocation);
    qDebug()<<"系统缓存目录路径:"<<QStandardPaths::standardLocations(QStandardPaths::CacheLocation);
```

## 每个枚举的功能参见手册
