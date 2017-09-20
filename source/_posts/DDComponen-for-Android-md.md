---
title: 组件化
date: 2017-09-18 17:52:54
tags: Android
---

-------------
看到篇文章讲组件化的 [Android彻底组件化方案实践](http://www.jianshu.com/p/1b1d77f58e84)
觉得很好
顺带看了下maven /gradle plugin相关

## Demo的依赖结构

```
+----+   +-------------+  +-------------+
|root|   |component one|  |component two|  ...
+-+--+   +------+------+  +-----+-------+
  |             ^               |
  ^--------+    |      +--------^
           |    |      |
         +-+----+------+--+
         |componentservice|
         +--+-------+-----+
            ^       ^
            |       |
     +------+-+  +--+---------+
     |basicres|  |componentlib|
     +---+----+  +------------+
         ^
     +---+----+
     |basiclib|
     +--------+
```

## basiclib
依赖通用库，如网络请求，图片加载等

## basicres
文件资源/图片资源/文字资源等

## componentlib

build.gradle 里包含两个task
- 将class打成jar包
- 把jar包复制到release

文件结构：

```
+----------------+   +----------------+   +------+
|IApplicationLike|   |IComponentRouter|   |Router|
+----------------+   +--------+-------+   +------+
                              ^
                              |
                         +----+----+
                         |IUIRouter|
                         +----+----+
                              ^
                              |
                          +---+----+
                          |UIRouter|
                          +--------+
```
### IApplicationLike
模拟生命周期
- onCreate()
- onStop()

### IComponentRouter
- 发送uri，类Intent
- 单例

### IUIRouter
- 注册或解注册
- 单例

### UIRouter
- 实现类
- List<IComponentRouter> uiRouters
- HashMap<IComponentRouter, Integer> priorities 

### Router
- HashMap<String, Object> services 
- HashMap<String, IApplicationLike> components

## componentservice
各个组件在此注册供root调用

每个组件在此定义一个Interface, 阐明提供的服务，并在组件内实现该interface
并于ApplicationLike中注册实现类

## 组件们[root / component one / component two / ...]
通过gradle插件实现自动切换application或library

### 插件参数
- isRegisterCompoAuto
- applicationName

### 

