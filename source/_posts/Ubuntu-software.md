---
title: Ubuntu裝機軟件
date: 2017-04-08 00:17:47
---

# oh-my-zsh
- Install zsh first
```
sudo apt-get install zsh
```
- Install OMZ
```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
- set zsh as default
```
chsh -s $(which zsh)
```
Or
```
sudo vi /etc/passwd
```
Find the line:
```
username:x:some-number:some-number:Your-name:/home/username:/bin/bash
```
and replace bash with zsh

# Rime
# Shadowsocks
# Git
## config
```
git config --global user.email "my-email"
git config --global user.name "my-name"
```
## Use ssh
```
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa
```
and add the new SSH key to github

