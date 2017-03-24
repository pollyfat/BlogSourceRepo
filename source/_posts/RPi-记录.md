---
title: RPi 记录
date: 2017-03-23 21:08:00
tags: RPi
---
-----------------------------
# 配置
重新烧了RPi的官方系统，仍然connection refused。查了半天才发现2016.11之后官方系统默认关闭SSH了。：（
解决办法：在SD卡根目录下建一个名为ssh的空白文件。

# ssh
## 用nmap查看RPi的IP
### 先查看本机IP
- Linux
$hostname -I
- Mac OS
系统偏好设置->网络->右边状态信息里
- Windows
cmd->ipconfig
### 用nmap查看
` nmap -sn your-ip/24`
## 链接
` ssh pi@<pi-ip>
默认密码：raspberry

# Open VNC(Virtual network computin)
## Check update
``` sudo apt-get update
sudo apt-get install realvnc-vnc-server realvnc-vnc-viewer
```
## Enabling VNC server
` sudo raspi-config`
and 
- Navigate to **Interfacing Options**
- **VNC**->Yes
## VNC Viewer
- [download](https://www.realvnc.com/download/viewer/)
- Enter Pi's username & pwd
