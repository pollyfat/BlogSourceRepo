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
o.睡先
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


# Aria2

## 安装
```
sudo apt-get install aria2
sudo mkdir /etc/aria2
sudo touch /etc/aria2/aria2.conf /etc/aria2/aria2.session
```

## 配置
```
#文件的保存路径(可使用绝对路径或相对路径)，默认：当前启动位置 
dir=/mnt/usbdisk 
#启用磁盘缓存，0为禁用缓存，需1.16以上版本，默认：16M 
disk-cache=32M 
#文件预分配方式，能有效降低磁盘碎片，默认：prealloc，NTFS建议使用falloc，EXT3/4建议trunc，MAC下需要注释此项 
#预分配所需时间：none < falloc ? trunc < prealloc，falloc和trunc则需要文件系统和内核支持 
file-allocation=none 
#断点续传 
continue=true 
#最大同时下载任务数，运行时可修改，默认：5 
max-concurrent-downloads=4 
#同一服务器连接数，添加时可指定，默认：1 
max-connection-per-server=4 
#最小文件分片大小，添加时可指定，取值范围1M -1024M，默认：20M 
min-split-size=10M 
#单个任务最大线程数，添加时可指定，默认：5 
split=4 
#整体下载速度限制，运行时可修改，默认：0 
#max-overall-download-limit=0 
#单个任务下载速度限制，默认：0 
#max-download-limit=0 
#整体上传速度限制，运行时可修改，默认：0 
#max-overall-upload-limit=0 
#单个任务上传速度限制，默认：0 
#max-upload-limit=0 
#禁用IPv6，默认：false 
disable-ipv6=true 
#从会话文件中读取下载任务 
input-file=/etc/aria2/aria2.session 
#在Aria2退出时保存错误/未完成的下载任务到会话文件 
save-session=/etc/aria2/aria2.session 
#定时保存会话，0为退出时才保存，需1.16.1以上版本, 默认：0 
#save-session-interval=60 
#启用RPC，默认：false 
enable-rpc=true 
#允许所有来源，默认：false 
rpc-allow-origin-all=true 
#允许非外部访问，默认：false 
rpc-listen-all=true 
#RPC监听端口，端口被占用时可以修改，默认：6800 
rpc-listen-port=6800 
#设置的RPC授权令牌，v1.18.4新增功能，取代 --rpc-user 和 --rpc-passwd 选项 
rpc-secret=lanseyujietestkey01 
#rpc-secret=<TOKEN>
# 设置的RPC访问用户名，此选项新版已废弃，建议改用 --rpc-secret 选项 
#rpc-user=<USER>
# 设置的RPC访问密码，此选项新版已废弃，建议改用 --rpc-secret 选项 
#rpc-passwd=<PASSWD> 
#事件轮询方式，取值：[epoll, kqueue, port, poll, select]，不同系统默认值不同 
#event-poll=select 
#当下载的是一个种子(以.torrent结尾)时，自动开始BT任务，默认：true 
#follow-torrent=true 
#BT监听端口，当端口被屏蔽时使用，默认：6881-6999 
listen-port=51413 
#单个种子最大连接数：默认：55 
#bt-max-peers=55 
#打开DHT功能，PT需要禁用，默认：true 
enable-dht=false 
#打开IPv6 DHT功能，PT需要禁用 
enable-dht6=false 
#DHT网络监听端口，默认：6881-6999 
#dht-listen-port=6881-6999 
#本地节点查找，PT需要禁用，默认：false 
#bt-enable-lpd=false 
#种子交换，PT需要禁用，默认:true 
enable-peer-exchange=false 
#每个种子限速，对少种的PT很有用，默认:50K 
#bt-request-peer-speed-limit=50K 
#客户端伪装，PT需要 
peer-id-prefix=-ND2017- 
user-agent=Transmission/4.1 
#当种子的分享率达到这个数时，自动停止做种，0为一直做种, 默认：1.0 
seed-ratio=0 
#强制保存会话，即使任务已经完成，默认:false，较新的版本开启后会在任务完成后依然保留.aria2文件 
force-save=true 
#BT校验相关，默认：true 
#bt-hash-check-seed=true 
#继续之前的BT任务时，无需再次校验，默认：false bt-seed-unverified=true 
#保存磁力链接元数据为种子文件(.torrent文件)，默认：false 
bt-save-metadata=true
```

##  测试，无报错则后台运行
```
sudo aria2c --conf-path=/etc/aria2/aria2.con

# 无报错则ctrl+c取消

# 后台运行
sudo aria2c --conf-path=/etc/aria2/aria2.conf -D
```

## 配置开机自启

### 编辑配置文件/etc/init.d/aria2c

```

#!/bin/sh 
### BEGIN INIT INFO 
# Provides:          aria2 
# Required-Start:    $remote_fs $network 
# Required-Stop:     $remote_fs $network 
# Default-Start:     2 3 4 5 
# Default-Stop:      0 1 6 
# Short-Description: Aria2 Downloader 
### END INIT INFO 
case "$1" in 
	start) 
		echo "Starting aria2c..." 
		sudo -u pi aria2c --conf-path=/etc/aria2/aria2.conf -D 
		;;
	stop) 
		echo "Stopping aria2c..." 
		killall aria2c 
		;; 
	restart) 
	$0 stop && sleep 3 && $0 start 
	;; 
esac 
exit 
```

### 设置文件权限
```
sudo chmod +x /etc/init.d/aria2c
```

### 设置开机自启
```
sudo update-rc.d aria2c defaults
```

# Nginx

## 安装
```
mkdir ~/Nginx && cd Nginx/ 
#Openssl补丁 
git clone https://github.com/cloudflare/sslconfig.git 
wget -O openssl.tar.gz -c https://github.com/openssl/openssl/archive/OpenSSL_1_0_2k.tar.gz 
tar zxvf openssl.tar.gz 
mv openssl-OpenSSL_1_0_2k/ openssl/ 
cd openssl/ 
patch -p1 < ../sslconfig/patches/openssl__chacha20_poly1305_draft_and_rfc_ossl102j.patch 
cd ../ 

#下载最新Nginx 
wget -c http://nginx.org/download/nginx-1.12.0.tar.gz 
tar zxvf nginx-1.12.0.tar.gz 
cd nginx-1.12.0/ 

#安装 PCRE 正则依赖库 
sudo apt install libpcre3 libpcre3-dev 
./configure --with-openssl=../openssl --user=pi --group=pi --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_v2_module --with-http_gzip_static_module --with-http_sub_module 
#configure无错误后进行编译 
make 

sudo make install 
sudo ln -s /usr/local/nginx/sbin/nginx /usr/local/sbin/
```

## 配置

```
sudo vi /usr/local/nginx/conf/nginx.conf
```

## 安装Aria2 WebUI

```
git clone https://github.com/ziahamza/webui-aria2.git /usr/local/nginx/html
```

## 测试 & 运行

```
#测试配置是否正确 
sudo nginx -t 
#启动Nginx 
sudo nginx 
#重载Nginx 
sudo nginx -s reload 
#停止Nginx 
sudo nginx -s stop
```

## 开机自启

### 编辑配置文件 /etc/init.d/nginx

```
#!/bin/sh 
### BEGIN INIT INFO 
# Provides:          nginx
# Required-Start:    $remote_fs $network 
# Required-Stop:     $remote_fs $network 
# Default-Start:     2 3 4 5 
# Default-Stop:      0 1 6 \
# Short-Description: Nginx Server 
### END INIT INFO 
case "$1" in 
	start) 
		echo "Starting Nginx..." 
		sudo -u pi nginx 
		;; 
	stop) 
		echo "Stopping Nginx..." 
		sudo nginx -s stop 
		;; 
	restart) 
		$0 stop && sleep 3 && $0 start 
		;; 
esac 
exit
```

### 设置权限
```
sudo chmod +x /etc/init.d/nginx
```

### 添加
```
sudo update-rc.d nginx defaults
```

# Ngrok

Ngrok可以将局域网端口映射到公网，相同服务还有花生壳


