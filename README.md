#### pslfcslinuxusergrp
#####intro
```
getent passwd   //list database
getent group
getent network
getent services
grep passwd /etc/nsswitch.conf
```
#####login scripts
######centos 7
login shell (run once, store path variables)
```
~/.bash_profile  /etc/profile
~/.bashrc /etc/.bashrc
```
non-login shell (show every times)
```
~/.bashrc /etc/.bashrc
```
######execution order
```
su -  = su -l  //provide login shell
```
```
su  //not login shell, still current user home dic
```
ps1, \W = relative path \w = full path
######home dict
```
ls -A do not show . ..
```

```
ls -A /etc/skel  (skeleton, template for new users)
```
```
.bash_logout
```
#####creating local users
```
