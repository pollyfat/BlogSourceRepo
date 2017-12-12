---
title: Create git repo in vps
date: 2017-12-12 12:25:42
tags: linux
---

# In VPS 
## new user [可选]

```
# 可能需要root权限
useradd git[or other name you like]
passwd git 
```

## Install git 

CentOS/Fedora: `yum install git`
Ubuntu/Debian: `apt-get install git`

## Add SSH key
看到有人说用这个
```
cat .ssh/id_rsa.pub | ssh user@ip "cat >> ~/.ssh/authorized_keys"
```
但是没太看懂这个命令, 直接把~/.ssh/id_rsa.pub复制到~/.ssh/authorized_keys里算数了 XD

## init git
```
git init --bare repo-name.git
```

至此服务器也算配完了, 其实也没干嘛, 新建用户懒得再配权限所以最终用的也是之前的用户登的...

# In local

## 配置端口
banwagon的VPS端口随机分配的, 而git默认的端口是22. 在~/.ssh/config里配置
```
Host ip[or domain]
Port 端口号
```

## 在项目中init git后配置remote
```
git remote set-url origin [user]@[ip]:[repo-name.git]
# 第一次push配置一下url
git push --set-upstream origin master
```
然后正常使用Git就OK
