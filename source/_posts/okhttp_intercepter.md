---
title: OkHttp 原理解析 - 插值器及使用逻辑
date: 2020-02-01 21:37:24
tags: Http
---


## 插值器 intercepter

通过添加不同的 intercepter 可以自由配置请求，实现方式：
-> 根据 originRequest 创建 RealCall
-> 具体请求位于 RealCall 中的内部类 AsyncCall，AsyncCall 继承了 Runnable，run 方法中 调用了RealCall中的 getResponseWithInterceptorChain()
-> 在 getResponseWithInterceptorChain() 中创建 RealInterceptorChain，其中包含所有intercepters
-> 调用 RealInterceptorChain 的 proceed() 
-> proceed() 中获取到当前 index 指向的 intercepter，调用 interceptor.intercept(RealInterceptorChain.copy())
```java
// RealInterceptorChain
override fun proceed(request: Request): Response {
    ...

    // Call the next interceptor in the chain.
    val next = copy(index = index + 1, request = request)
    val interceptor = interceptors[index]

    @Suppress("USELESS_ELVIS")
    val response = interceptor.intercept(next) ?: throw NullPointerException(
        "interceptor $interceptor returned null")

    ...
    return response
}
```

-> interceptor.intercept() 中调用 RealInterceptorChain.proceed(), 执行链中的下一个 intercepter
```java
// RealInterceptorChain
override fun intercept(chain: Interceptor.Chain): Response {
    // 前置工作
    ...
    while (true) {
      call.enterNetworkInterceptorExchange(request, newExchangeFinder)

      var response: Response
      try {
       ...
        try {
          response = realChain.proceed(request)  // 将链向前推进
          newExchangeFinder = true
        } catch (e: RouteException) {
          ...
          continue
        } catch (e: IOException) {
          ...
          continue
        }

        // 后置工作
        ...
      }
    }
  }
```


```
 ┌───────────────────────────────────────┐
 │   chain                               │
 │                                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter0◄────────index=0        │
 │  └────────────┘                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter1│                       │
 │  └────────────┘                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter2│                       │
 │  └────────────┘                       │
 │                                       │
 │   ......                              │
 │                                       │
 │                                       │
 └──────┬────────────────────────────────┘
        │
        │ proceed(){ index+1; intercepter0.intercept(copy(self)))}
        │  
        │
 ┌──────▼────────────────────────────────┐
 │   chain                               │
 │                                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter0│                       │
 │  └────────────┘                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter1◄───────index=1         │
 │  └────────────┘                       │
 │                                       │
 │  ┌────────────┐                       │
 │  │intercepter2│                       │
 │  └────────────┘                       │
 │                                       │
 │   ......                              │
 │                                       │
 │                                       │
 └───────────────────────────────────────┘
```