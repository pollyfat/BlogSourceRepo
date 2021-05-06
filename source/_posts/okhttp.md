---
title: OkHttp 原理解析
date: 2020-01-29 13:17:25
tags: Http
---


```java
// 基本使用

OkHttpClient.newCall(Request).enqueue(Callback)

```

## 线程调度

1. newCall 返回 RealCall 对象

2. 调用 RealCall 对象的 enqueue 方法：

	```java
	// RealCall.class
	  override fun enqueue(responseCallback: Callback) {
	    synchronized(this) {
	      check(!executed) { "Already Executed" }
	      executed = true
	    }
	    callStart() // 调用前期准备，如添加监听
	    3. 使用调度器将 call 加入队列
	    client.dispatcher.enqueue(AsyncCall(responseCallback)) 
	  }
	```

3. 使用调度器将 call 加入队列

	```java
	// Dispatcher.class
	  internal fun enqueue(call: AsyncCall) {
	    synchronized(this) {
	      readyAsyncCalls.add(call)
	      if (!call.call.forWebSocket) {
	        val existingCall = findExistingCallWithHost(call.host)
	        if (existingCall != null) call.reuseCallsPerHostFrom(existingCall)
	      }
	    }
	    promoteAndExecute() // 4. 从 readyAsyncCalls 中拿出 call 并放入 runningAsyncCalls，执行任务
	  }
```

4. promoteAndExecute()
	每当 maxRequests/maxRequestsPerHost/call 调用 enqueue/call finish，都会执行promoteAndExecute()

	```java
		private fun promoteAndExecute(): Boolean {
	  	this.assertThreadDoesntHoldLock()
	    val executableCalls = mutableListOf<AsyncCall>()
	    val isRunning: Boolean
	    synchronized(this) {
	      val i = readyAsyncCalls.iterator()
	      while (i.hasNext()) {
	        val asyncCall = i.next()
	        if (runningAsyncCalls.size >= this.maxRequests) break // Max capacity.
	        if (asyncCall.callsPerHost.get() >= this.maxRequestsPerHost) continue // Host max capacity.
	        i.remove()
	        asyncCall.callsPerHost.incrementAndGet()
	        // 将 call 加入可执行列表
	        executableCalls.add(asyncCall)
	        // 将 call 加入运行中列表
	        runningAsyncCalls.add(asyncCall)
	      }
	      isRunning = runningCallsCount() > 0
	    }
	    for (i in 0 until executableCalls.size) {
	      // 遍历可执行列表中的任务并放入线程池 executorService 中执行
	      val asyncCall = executableCalls[i]
	      asyncCall.executeOn(executorService)
	    }
	    return isRunning
	  }
	```