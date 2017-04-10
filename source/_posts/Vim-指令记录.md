---
title: Vim 指令记录
date: 2017-03-22 16:37:49
tags: Util
---

--------------------
# 修改配置文件
- 打开配置文件
 ` $vi ~/.vimrc `
- 设置项
 ```
 set number //默认显示行号
 syntax on //打开语法高亮
 set autoindent //自动对齐
 set tabstop=4 //tab 键为4个空格
 ```
 如果出現`sorry,the command is not ...`,可能是因爲沒有安裝Vim，用`sudo apt-get -y install vim`安裝一下就好。
- 命令
 ```
 . //重复上一次操作
 N<command> //重复N次command
 dd //剪切
 NG //游标移动到第N行
 ctrl+o //回到上一次光标所在位置
 e // 到下一个单词的结尾
 ge // 到上一个单词的结尾
 0 // 到行头
 $ // 到行尾
 dw // 删除光标所在单词
 y // 复制（yy 复制整行）
 p // 粘贴
 u // 撤销
 ctrl+r // redo
 \* // 寻找游标所在单词
 n // 进入查找模式后查找下一个字符（N 为上一个）
 ```
- 传说中的高级操作
 ```
 :n // 切换下一文件（N为上一文件）
 :e new file // 打开新文件

 $ vim -r
 :ewcover file // 意外退出时恢复数据

 v // 选取(V 选整行）
 :sp file // 打开新的横向视窗编辑file(:vsp 纵向视窗)
 ctrl+w // 视窗跳转
 :new 打开新视窗 
 :w file_name // 另存为
```

# DON'T

在RPi里用了下Vim，发现方向键和delete不能用。后来查了发现vi里压根不希望你用方向键。QAQ
方向：
` H = Left, J = up, K = down, L = right`
Delete:
``` x = Deletes the charecter **under** the cursor
X = Deletes the charecter **before** the cursor
dw = Deletes from the cursor **to the next word**
dd = Deletes the **line** the cursor is on.

