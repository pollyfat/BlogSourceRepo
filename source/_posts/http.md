---
title: Http笔记
date: 2018-11-21 11:38:29
tags: Android
---

# HTTP

无状态协议

TCP/IP常用端口: 80, 但其他也可以
```
            request
           --------->
HOST                     CLIENT
           <--------- 
            response
```

```
http://www.domain.com:1234/path/to/resource?a=b&x=y
----   -------------- ----                  -------
  |           |         |                       |
protecol     host      port                   query
```

### protecol:

- http
- https
- ftp
- smtp
....

### Verbs:

- GET 获取已存在的资源, 地址中包含所有服务器所需信息
- POST 创建新的资源, 通常会携带请求体
- PUT 更新一个已存在资源, 请求体中通常包含需更新的资源
- DELETE 删除已存在资源
- TRACE 
- OPTIONS

### Status Codes:

- 1xx: informational msg
- 2xx: 
 - 200: OK
 - 204: No Content
 - 205: Reset Content
 - 206: Partial Content
- 3xx: 重定向
 - 304: Not Modified
- 4xx: 客户端错误
 - 401: 未认证
 - 403: 无权限
 - 405: 请求方法错误(GET->POST等)
 - 404: 无资源
 - 409: Conflict
- 5xx: 服务端错误

### http vs https
```
    **http**            **https**

    HTTP                  HTTP
    TCP                   SSL or TLS
    IP                    TCP
                          IP                   
```

### 请求过程
```
                    
 +-----------+     B                     D  Process
 |    Host   |      +-----------+         +----------+
 +-----------+      |           |         |          |
                    |           |         |          |
                    |           |         |          |
                    |           |         |          |
                    |           |         |          |
                    |           |         |          |
 +-----------+      |           |         |          |
 |  Client   |      |  connect  |         | request  |
 +-----------+    A +          C+---------+          +------->     ------->
                DNS lookup                          E   response   F close
```

- A: 通过域名从dns服务器中找到相应ip地址
- A->C: 和服务器建立链接
- C->D: 向服务器发送请求
- D: 服务器处理请求
- D->E: 服务器返回信息
- F: 关闭链接

### http协议版本区别

- HTTP/1.0: 每处理一个链接即刻关闭
- HTTP/1.1: 打开链接直到客户端关闭它
  parallel connections + persistent connections

### 服务端认证方式

- Request Headers
- Client-IP
- Fat Urls
- Cookies

### http请求结构

Request line  POST /path/to/server/file HTTP/1.1
Headers       Host: www.abc.com:8080
              Content-Length: 25
Empty line
Body(opt)     Must match Content-Length

### 认证过程

```
Web Browser                                  Web Server
                    GET /some/file
request      ------------------------------>

                     401 Unauthrized
             <------------------------------
enter             www-Authenticate:Basic...
user&pwd                 
                     GET /some/file
              ------------------------------>
                  Authorized:Basic ....

                          201 OK
              <------------------------------
                   <html>Hello..</html>
```

### 缓存

Caching(Browser)

- Last-Modified
- ETag
- Expires
- Max-Age

Cache-control(Server)

- public
- private
- no-cache



> Reference
>- [Http-Part 1](https://code.tutsplus.com/tutorials/http-the-protocol-every-web-developer-must-know-part-1--net-31177)
>- [Http-Part 2](https://code.tutsplus.com/tutorials/http-the-protocol-every-web-developer-must-know-part-2--net-31155)
>- [Caching](https://betterexplained.com/articles/how-to-optimize-your-site-with-http-caching/)
