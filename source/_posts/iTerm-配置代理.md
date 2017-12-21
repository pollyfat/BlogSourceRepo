---
title: iTerm 配置代理
date: 2017-12-21 12:42:07
tags:
---


## install
```
brew install privoxy
```

## config
```
# listen-address 0.0.0.0 其他设备可访问
# listen-address 127.0.0.1 其他设备不可访问
# :8118 HTTP代理默认端口
echo 'listen-address 127.0.0.1:8118\nforward-socks5 / localhost:1080 .' >> /usr/local/etc/privoxy/config
```

## start service
```
sudo /usr/local/sbin/privoxy /usr/local/etc/privoxy/config
```

## check is start success
```
netstat -na | grep 8118
```

if:
```
tcp4 0   0  *.8118       *.*     LISTEN
```
then success

else: fail

## debug
```
# show log
cat /usr/local/var/log/privoxy/logfile
```
### Can't bind
```
Fatal error: can't bind to x.x.x.x:xxxx: Can't assign requested address
```

then check is the ip address right. [白眼]

### Port be taken
```
Fatal error: can't bind to 127.0.0.1:8118: There may be another Privoxy or some other proxy running on port 8118
```

then change port to 8119 or other availiable port

## Add switcher

```
# ~/.bash_profile Add follow

function proxy_off(){
    unset http_proxy
    unset https_proxy
    echo -e "Proxy off"
}
function proxy_on() {
    export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
    export http_proxy="http://127.0.0.1:8118"
    export https_proxy=$http_proxy
    echo -e "Proxy on"
}

```

Update config
```
source ~/.bash_profile
```

## Open proxy
每开一个窗口都需要开一下
```
proxy_on
```

## Check proxy
```
curl ip.gs
```

如果显示的IP是SS配置的服务器IP then hooray!!!!




