---
title: Java I/O
date: 2019-09-18 17:52:54
tags: Java
---

## File I/O

Stream[数据传输格式：二进制]:
流创建的实质： 开辟一块内存，储存文件相关信息，如：文件位置/大小/当前指向位置等。 所以流使用完毕后应close以释放资源。
	- Output
	  FileOutputStream: write(byte)
	- Input
	  FileInputStream: [byte]read()

Reader[格式：字符]:
	- 根据流数据转换： InputStreamReader
	- 根据文件路径获取： FileReader

Writer[格式：字符]:
	- 根据流数据转换： OutputStreamWriter
	- 根据文件路径获取： FileWriter

BufferReader[格式：字符串]: 一次性从文件中获取较大量的数据存入内存中，减少交互量提高性能。
	- 根据流数据转换： BufferInputStream
	- 根据Reader转换： BufferReader

BufferWriter[格式：字符串]: 输入暂存入缓冲区，缓冲区满后一次性写入文件，减少交互量提高性能。 未满时强行写入需flush或关闭流。
	- 根据流数据转换： BufferOutputStream
	- 根据Writer转换： BufferWriter

## Server I/O

Socket: 和外部端口建立TCP/IP链接
ServerSocket: 开辟一个端口，等待他人建立链接

建立Socket链接成功后可拿到其InputStream/OutputSteam，通过Stream在Socket中传输数据。

## Okio
对原生封装的三方库，提供了更方便使用的Buffer工具

input -> [source --read--> buffer --read--> 字符串]
		 [bufferSource --read--> 字符串]
output -> [文件 --write--> sink --write--> buffer]
		 [bufferSink --write--> 字符串]