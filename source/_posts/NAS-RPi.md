---
title: Raspberry pi 搭建NAS流程
date: 2017-07-25 12:30:50
tags: Raspberry pi
---

---------------
周末想把硬盘拿回家看电影的时候估计没正确umount硬盘，然后拔错插头RPi断电了，反正重启后RPi的ssh连不上了，很烦。之前秉承够用就行只是跟着很多教程去搭了个框架，但是原理不懂出问题或者哪个步骤不对都很难Debug。记个流水帐以防出现要重装这个最坏的可能性。
---------------
# linux基础相关
## 设置Root用户
`sudo passwd --unlock root & su`


# 挂载硬盘
```
# 查看插入的设备信息,确定Device地址
sudo fdisk -l
# 新建挂载点,名字路径随意
sudo mkdir /samba
# 设置权限
sudo chmod 777 /samba
# 直接挂载
sudo mount /dev/sda1 /samba
```
## 设置开机自动挂载
`sudo vi /etc/fstab`
add 
`/dev/sda1       /home/pi/Share/usb      ext4    defaults,noatime        0       0`
to the bottom

## 卸载
` sudo umount /samba `

## 测试是否成功
` cd /samba `
` ls `

## Issues
### 出现`mount: wrong fs type, bad option, bad superblock on /dev/sda1,missing codepage or helper program, or other error`

上次没umount成功硬拔的...出问题了？？？
-----
今天又可以了hahaha

# 安装samba
之前还装了miniDLNA，但是没怎么用，就直接通过samba打开视频了。刚看到好像miniDLNA的速度要比samba快很多，还以为是因为路由器老旧才慢...有空再弄吧
## 安装
`sudo apt-get install samba samba-common-bin`

## 配置
`sudo vi /etc/samba/smb.conf`

## 重启samba服务
`sudo /etc/init.d/samba restart`

## 添加用户
`sudo smbpasswd -a pi`
然后密码

## 设置开机自启
`sudo vi /etc/rc.local`

在exit 0前添加重启服务的命令

------
至此应该可以在Ubuntu的文件管理器里通过Network->share访问到了(当然要先输入用户名和密码)

# 安装迅雷固件
Raspberry Pi 3B 用的是
## 下载并解压后放到树莓派里
............................

我觉得这件事情很操蛋的...

迅雷的远程服务17号停了....

我说怎么...一直... the license is not passed now

我...日...

等找到其他远程下载的工具再写吧，但是估计很难有像迅雷这样可以跑满速的了

唉...
