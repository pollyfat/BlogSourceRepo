---
title: Python SocketServer原理解析
date: 2017-03-14 16:58:36
tags:
---
## 先解释下TCP和UDP
### 共同点

 - 用来传输数据的协议
 - 顶级协议（即不管是通过TCP还是UDP传包，该包都是传到一个IP地址）
### 各自特点
#### TCP（Transmission Control Protocol）
- 原理：

        Client --- send TCP packets ---> Server
        Server--- sends received msg --->Client
        Server--- send TCP packets stream --->Client
        Client --- send received msg --->Server
         
- 数据包接收严格按照发送顺序
- 如果服务器未收到消息确认包，则重新发送对应包

#### UDP（User Datagram Protocol）
- 不确保数据送达
- 不会重新发包

## 回到SocketServer
对基于Socket的服务器：

 - address family
  - AF_INET{,6}:IP sockets (default)
  - AF_UNIX:Unix domain sockets
  - others,e.g. AF_DECNET
 - socket type
  - // TODO 
  - 

        +------------+
        | BaseServer |
        +------------+
              |
              v
        +-----------+        +------------------+
        | TCPServer |------->| UnixStreamServer |
        +-----------+        +------------------+
              |
              v
        +-----------+        +--------------------+
        | UDPServer |------->| UnixDatagramServer |
        +-----------+        +--------------------+


    
    




