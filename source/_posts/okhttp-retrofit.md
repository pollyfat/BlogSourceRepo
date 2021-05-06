---
title: OkHttp 及 Retrofit 原理解析
date: 2020-01-29 13:17:25
tags: Http
---

# Retrofit

## 创建接口实现类

 1. 动态代理：根据接口创建出具体实现类

 ```
  // 核心代码（Retrofit.class）

  public <T> T create(final Class<T> service) {
    validateServiceInterface(service);
    // 通过 InvocationHandler 将接口转换成实现类
    return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
        new InvocationHandler() {
          private final Platform platform = Platform.get();
          private final Object[] emptyArgs = new Object[0];

          @Override public @Nullable Object invoke(Object proxy, Method method,
              @Nullable Object[] args) throws Throwable {
            // If the method is a method from Object then defer to normal invocation.
            if (method.getDeclaringClass() == Object.class) {
              return method.invoke(this, args);
            }
            if (platform.isDefaultMethod(method)) {
              return platform.invokeDefaultMethod(method, service, proxy, args);
            }
            // 2. loadServiceMethod(method) 构建接口中方法的具体实现，即把接口的抽象方法，根据注解及参数构建出具体的实现方法
            // 3. 调用2中构建出的方法，返回 Call 对象
            return loadServiceMethod(method).invoke(args != null ? args : emptyArgs);
          }
        });
  }
 ```
 2. 构建方法[loadServiceMethod(method)]

 ```
 // 核心代码（HttpServiceMethod.class）：

  static <ResponseT, ReturnT> HttpServiceMethod<ResponseT, ReturnT> parseAnnotations(
      Retrofit retrofit, Method method, RequestFactory requestFactory) {
    boolean isKotlinSuspendFunction = requestFactory.isKotlinSuspendFunction;
    boolean continuationWantsResponse = false;
    boolean continuationBodyNullable = false;

    Annotation[] annotations = method.getAnnotations(); // 方法注解
    Type adapterType; // 返回数据类型
    if (isKotlinSuspendFunction) {
      Type[] parameterTypes = method.getGenericParameterTypes();
      Type responseType = Utils.getParameterLowerBound(0,
          (ParameterizedType) parameterTypes[parameterTypes.length - 1]);
      if (getRawType(responseType) == Response.class && responseType instanceof ParameterizedType) {
        responseType = Utils.getParameterUpperBound(0, (ParameterizedType) responseType);
        continuationWantsResponse = true;
      } else {
      }

      adapterType = new Utils.ParameterizedTypeImpl(null, Call.class, responseType);
      annotations = SkipCallbackExecutorImpl.ensurePresent(annotations);
    } else {
      adapterType = method.getGenericReturnType();
    }

    CallAdapter<ResponseT, ReturnT> callAdapter =
        createCallAdapter(retrofit, method, adapterType, annotations);
    Type responseType = callAdapter.responseType();
    if (responseType == okhttp3.Response.class) {
      throw methodError(method, "'"
          + getRawType(responseType).getName()
          + "' is not a valid response body type. Did you mean ResponseBody?");
    }
    if (responseType == Response.class) {
      throw methodError(method, "Response must include generic type (e.g., Response<String>)");
    }
    // TODO support Unit for Kotlin?
    if (requestFactory.httpMethod.equals("HEAD") && !Void.class.equals(responseType)) {
      throw methodError(method, "HEAD method must use Void as response type.");
    }
	
		// 将返回的数据使用如 GsonConverterFactory 进行转换，转换为定义接口时的返回类对象
    Converter<ResponseBody, ResponseT> responseConverter =
        createResponseConverter(retrofit, method, responseType);

    okhttp3.Call.Factory callFactory = retrofit.callFactory;
    if (!isKotlinSuspendFunction) {
    	// CallAdapted 继承了 HttpServiceMethod，实现了 adapt 方法
      return new CallAdapted<>(requestFactory, callFactory, responseConverter, callAdapter);
    } else if (continuationWantsResponse) {
      //noinspection unchecked Kotlin compiler guarantees ReturnT to be Object.
      return (HttpServiceMethod<ResponseT, ReturnT>) new SuspendForResponse<>(requestFactory,
          callFactory, responseConverter, (CallAdapter<ResponseT, Call<ResponseT>>) callAdapter);
    } else {
      //noinspection unchecked Kotlin compiler guarantees ReturnT to be Object.
      return (HttpServiceMethod<ResponseT, ReturnT>) new SuspendForBody<>(requestFactory,
          callFactory, responseConverter, (CallAdapter<ResponseT, Call<ResponseT>>) callAdapter,
          continuationBodyNullable);
    }
  }
 ```
 3. 调用2中构建出的方法，返回 Call 对象 [invoke(args != null ? args : emptyArgs)]

 ```
 	// 核心代码

 	（HttpServiceMethod.class）// 实际是CallAdapted对象
 	  @Override final @Nullable ReturnT invoke(Object[] args) {
 	  // 构建 Call 对象，该 call 的 enqueue 方法会将代码放到后台运行
    Call<ResponseT> call = new OkHttpCall<>(requestFactory, args, callFactory, responseConverter);
    // CallAdapted 中的 adapt 方法，将Call<RespObj> 对象转换成 RespObj
    return adapt(call, args); 
  }

  （CallAdapted.class）
    @Override protected ReturnT adapt(Call<ResponseT> call, Object[] args) {
    	// 此处 callAdapter 为 HttpServiceMethod.parseAnnotations 中创建的
    	// 4. createCallAdapter(retrofit, method, adapterType, annotations);
      return callAdapter.adapt(call);
    }
 ```
4. 构造CallAdapter [createCallAdapter(retrofit, method, adapterType, annotations)]

	追溯 createCallAdapter 直到出现具体实现类：
```
	// 核心代码（DefaultCallAdapterFactory.class）

  @Override public @Nullable CallAdapter<?, ?> get(
      Type returnType, Annotation[] annotations, Retrofit retrofit) {
    if (getRawType(returnType) != Call.class) {
      return null;
    }
    if (!(returnType instanceof ParameterizedType)) {
      throw new IllegalArgumentException(
          "Call return type must be parameterized as Call<Foo> or Call<? extends Foo>");
    }
    final Type responseType = Utils.getParameterUpperBound(0, (ParameterizedType) returnType);

    final Executor executor = Utils.isAnnotationPresent(annotations, SkipCallbackExecutor.class)
        ? null
        : callbackExecutor;

    return new CallAdapter<Object, Call<?>>() {
      @Override public Type responseType() {
        return responseType;
      }

      @Override public Call<Object> adapt(Call<Object> call) {
        return executor == null
            ? call // HttpServiceMethod.invoke 中创建的 OkHttpCall 对象
            // 5. 拓展 Call
            : new ExecutorCallbackCall<>(executor, call); 
      }
    };
  }
```

 5. 拓展 Call

 ```
 	// 核心代码（ExecutorCallbackCall.class）

    @Override public void enqueue(final Callback<T> callback) {
      Objects.requireNonNull(callback, "callback == null");

      delegate.enqueue(new Callback<T>() {
        @Override public void onResponse(Call<T> call, final Response<T> response) {
          // 6. 使用 callbackExecutor 将 Call 放入线程池运行 
          callbackExecutor.execute(() -> {
            if (delegate.isCanceled()) {
              // Emulate OkHttp's behavior of throwing/delivering an IOException on cancellation.
              callback.onFailure(ExecutorCallbackCall.this, new IOException("Canceled"));
            } else {
              callback.onResponse(ExecutorCallbackCall.this, response);
            }
          });
        }

        @Override public void onFailure(Call<T> call, final Throwable t) {
          callbackExecutor.execute(() -> callback.onFailure(ExecutorCallbackCall.this, t));
        }
      });
    }
 ```

 6. 使用 callbackExecutor 将 Call 放入线程池运行 [new ExecutorCallbackCall<>(executor, call]
 
 追溯 callbackExecutor

 ```
   // 核心代码（retrofit2.Platform.Android）
   static final class Android extends Platform {
    Android() {
      super(Build.VERSION.SDK_INT >= 24);
    }

    @Override public Executor defaultCallbackExecutor() {
      return new MainThreadExecutor(); // 新建主线程中的Executor
    }

    static class MainThreadExecutor implements Executor {
      private final Handler handler = new Handler(Looper.getMainLooper());

      @Override public void execute(Runnable r) {
        handler.post(r); // 将callback 放入前台运行
      }
    }
  }
 ```