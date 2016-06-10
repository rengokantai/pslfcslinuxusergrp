#### pslfcslinuxusergrp
#####intro
```
getent passwd   //list database
getent group
getent networks
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
show current user
```
echo $USER
```
######06
```
ls /etc/bash*
ls /etc/profile*
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
######id
```
id -g //primary group
id -G //primary+secondary
id -Gn
```
######create user account
```
sudo useradd -N user2 -g gp1-G gp2  //do not add to default group    -s /bin/sh   // -N no private group -g primarygroup -G secondary group
```
here we test
```
useradd -m user1
useradd -N user2 -g users -G adm
useradd user3 -G adm -s /bin/sh
```
######managing user pass
```
passwd user1
echo 'user2:pass' |sudo chpasswd
echo pass | sudo passwd user3 --stdin  //only in redhat
```
######password age
```
chage -l usename
sudo pwconv //cannot read password from /etc/passwd,recover.
change -M user
```
######account defaults
```
less /etc/login.defs
```
show useradd defaults
```
useradd -D
```
set default to ubun shell
```
useradd -Ds /bin/sh
```
the original file,permanent change
```
vi /etc/default/useradd
```
######modify and delete accounts
modify
```
usermod -c "ke" user1
```
change login shell
```
chsh -l
chsh -s /bin/sh bob  = usermod -s /bin/sh bob
userdel -r user  //remove home dict, cronjob
find /home -uid 1111 -delete
```

#####managing local group
######creating local group
```
newgrp wheel  //change group in session
```


```
