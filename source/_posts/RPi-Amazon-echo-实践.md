---
title: RPi Amazon echo 实践
date: 2017-03-24 14:30:34
tags: RPi
---
--------------------------
感觉标题这种东西，真的就只有那几个词可以用。=益=
把Echo的环境搭好了，环境步骤就不详讲了具体要翻的话看这里->[How to Build YourOwn Amazon Echo](http://lifehacker.com/how-to-build-your-own-amazon-echo-with-a-raspberry-pi-1787726931)
这里记一下重启需要重新部署的命令。
--------------------------

# In First Terminal windows
```
cd ~/Desktop/alexa-avs-sample-app/samples
cd companionService && npm start
```

# Second Terminal
```
cd ~/Desktop/alexa-avs-sample-app/samples
cd javaclient && mvn exec:exec
```
**Click Yes** and than **DO NOT** click Ok yet.
Login to Amazon balabala...
after seen "device tokens ready"
click THE OK.

# Third Terminal
```
cd to samples path
cd wakeWordAgent/src && ./wakeWordAgent -e kitt_ai
```

---------    **ALL DONE**     --------------
