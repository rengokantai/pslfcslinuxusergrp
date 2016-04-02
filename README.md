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
######id
```
id -g
id -G
id -Gn
```
######create user account
```
sudo useradd -N user2 -g gp1-G gp2  //do not add to default group    -s /bin/sh
```
######managing user pass
```
echo 'user:pass' |sudo chpasswd
echo password | sudo passed user2 --stdin  //only in redhat
```
######password age
```
chage -l usename
sudo pwconv //cannot read password from /etc/passwd
```
######account defaults
```
less /etc/login.defs
```
```
vi /etc/default/useradd
```
######modify and delete accounts
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
