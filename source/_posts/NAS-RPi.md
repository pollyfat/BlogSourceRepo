---
title: Raspberry pi 搭建NAS流程
date: 2017-07-25 12:30:50
tags: Raspberry pi
---

---------------
周末想把硬盘拿回家看电影的时候估计没正确umount硬盘，反正RPi的ssh连不上了，很烦。之前秉承够用就行只是跟着很多教程去搭了个框架，但是原理不懂出问题或者哪个步骤不对都很难Debug。记个流水帐以防出现要重装这个最坏的可能性。
---------------
# linux基础相关
## 设置Root用户
' sudo passwd --unlock root & su '


# 挂载硬盘
'''
# 查看插入的设备信息,确定Device地址
sudo fdisk -l
# 新建挂载点
sudo mkdir /mnt/jelly(Or any name you like)
# 修改fstab文件，fstab中存储了系统启动时的挂载信息
sudo vi /etc/fstab
# 在文件最后一行添加以下内容。参数1:Device地址，2:挂载地址，umask前四位为读写权限，0000为所有人可读可写可执行
# 仅修改文件则需重启才生效
/dev/sda1 /mnt/jelly auto defaults,noexec,umask=0000 0 0
'''

## 卸载
' sudo umount /mnt/jelly '

## 测试是否成功
' cd /mnt/jelly '
' ls '

