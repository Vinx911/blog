---
title: Ubuntu切换软件版本 - 以g++为例
date: 2022-11-29 09:58:22
id: 41
tags: Linux实用技巧
categories: Linux实用技巧
copyright: true
---
# Ubuntu切换软件版本 - 以g++为例

## 1. 将各个版本添加到列表中

```shell
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 11
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-12 12
```

## 2. 列出已经配置版本

```shell
sudo update-alternatives --list g++
```

## 3. 切换系统默认的g++版本

```shell
sudo update-alternatives --config g++
```

