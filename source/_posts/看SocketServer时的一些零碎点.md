---
title: 看SocketServer时的一些零碎点
date: 2017-03-15 12:07:25
tags:
---
# Python相关
### hasattr()
看到有人说别用哈哈哈。
用：

    try:
        print(x.y)
    except AttributeErrror:
        print("no y!")
替换hasattr()，除非打算写纯Python 3.