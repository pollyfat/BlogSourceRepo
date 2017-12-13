---
title: CentOs碰到的相关问题
date: 2017-12-13 17:37:45
tags: Linux
---

## 赋予用户root权限

试过
```
usermod -aG wheel <user>
```
但是没软用.最后用的下面的方法:
Root用户下
```
visudo #快捷用vi打开/etc/sudoers
#找到root ALL=(ALL) ALL这行,并在下面添加
<user> ALL=(ALL) ALL
```
DONE

## UTF-8
在VPS写了点东西, 在通过Git拉回来的时候发现乱码爆了, 应该是UTF-8没设置
日常求SF得出
```
localedef -c -f UTF-8 -i en_US en_US.UTF-8
export LC_ALL=en_US.UTF-8
```
但是还没确认是否生效 XD
