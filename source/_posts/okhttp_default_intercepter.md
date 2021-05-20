---
title: OkHttp 原理解析 - 默认插值器功能
date: 2020-02-01 21:37:24
tags: Http
---


## 默认intercepter

```java
val interceptors = mutableListOf<Interceptor>()
    interceptors += client.interceptors
    interceptors += RetryAndFollowUpInterceptor(client)
    interceptors += BridgeInterceptor(client.cookieJar)
    interceptors += CacheInterceptor(client.cache)
    interceptors += ConnectInterceptor
    if (!forWebSocket) {
      interceptors += client.networkInterceptors
    }
    interceptors += CallServerInterceptor(forWebSocket)

```

### RetryAndFollowUpInterceptor

