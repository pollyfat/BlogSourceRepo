---
title: Android多线程机制
date: 2019-12-15 21:15:36
tags: Java
---


## Looper/Handler

- 每个线程都有自己的Looper
- Looper中的loop函数不断检查MessageQueue中是否有任务，如有任务则通过Handler中定义的处理函数handleMessage对Message进行处理。
Handler可通过post()向MessageQueue中添加新的Message。
- loop()
	-> 获得MessageQueue中的第一个Message
	  -> 如果Message callback 中的 runnable 不为空，则执行 runnable
	  -> 如果 Message callback 中的 runnable 为空，Handler 本身的 callback 不为空，则执行 Handler callback
	  -> 如果 Message callback / Handler callback 都为空， 则通过 Handler 的 handleMessage 进行处理
	-> 处理完毕后返回第一步

```
                     Add msg to queue
                  ┌────────────────────┐
                  │                    │
┌─────────────────┼───────────────┐    │ ┌───────────────────────────┐
│ Looper◄────────────────┐        │    │ │ Handler                   │
│ ┌────────────┐  │      │        │    │ │ ┌──────────┐              │
│ │MessageQueue◄──┘      └────────┼──────┼─┤looper    │              │
│ └────────┬───┘                  │    │ │ └──────────┘              │
│          │                      │    │ │ ┌──────────┐              │
│          │                      │    └─┼─┤post(msg) │              │
│ ┌────────┼─────────┐            │      │ └──────────┘              │
│ │loop()  │         │            │      │ ┌─────────────────┐       │
│ │        ▼         │            │      │ │ handleMessage   │       │
│ │Endless loop     ◄├────────────┼──────┼─┤                 │       │
│ └──────────────────┘            │      │ └─────────────────┘       │
│                                 │      │                           │
└─────────────────────────────────┘      └───────────────────────────┘
```

## ThreadLocal

同一个对象在不同的线程中指向的值不同