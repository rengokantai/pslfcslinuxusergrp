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
grep user /etc/group
```
```
newgrp wheel  //change group in session,change current user to a new group
```
delete a group from a user
```
gpasswd -d user group
usermod -G "" username //remove all secondary group
```
add group
```
groupadd new
```
######Manage Group Membership
```
usermod -G new,wheel
gpasswd -M user1,user2 group
```
######Making Use of the SGID Permission
```
yum install -y httpd w3m
chgrp -R apache /var/www
chmod -R o= /var/www
```
######Group Passwords
```
id -Gn
newgrp adm  //will prompt a password
gpasswd adm
```
#####Using PAM to Control User Access
######Automating Home Directory Creation at User Login
```
ls /etc/pam.d
ls /lib64/security
ls /etc/security
```
change 
```
vim /etc/login.defs
```
if
```
CREATE_HOME no,
```
then will not create home dic automatically.  
Search oddjob
 ```
 rpm -qa | grep oddjob
 ```
 If not present,
 ```
 yum install oddjob-mkhomedir -y
 ```
 start oddjob
 ```
 systemctl start oddjobd
 ```
 start autocinfig
 ```
 authconfig --enablemkhomedir --update
 cd /etc/pam.d
 grep mkhomedir *
 ```
 ######Implementing Password Policies
 ```
 cat /etc/pam.d/system-auth
 vim /etc/security/pwquality.conf
 pwscore
```
######Restricting or Limiting Access to Resources
```
ulimit -a
ulimit -u  //max user process
```
create a test sctipt
```
#!/bin/basj
echo "x"
$0
```
```
chmod +x test.sh
```
config permanent:
```
vim /etc/security/limits.conf
```
edit(examples)
```
* - maxlogins 4
@users soft nproc 10
```
######Adding Login Time Restrictions
```
vim /etc/pam.d/sshd
```
edit
```
account required pam.time.so
```
then
```
vim /etc/security/time.conf
```
add
```
*;*;user1|user2;!Wk0800-1800
```
#####Implementing OpenLDAP Directories on CentOS 7
######Automating Home Directory Creation at User Login
server1:
```
netstat -ltn
firewall-cmd --permanent --add-service=ldap && firewall-cmd --reload
```
install:
```
yum install -y openldap openldap-clients openldap-servers migrationtools.noarch
```
config DB
```
cp /usr/share/openldap-servers/DB_CONFIG.example  /var/lib/ldap/DB_CONFIG
slaptest  //fail this time
```
change owner
```
chown ldap:ldap /var/lib/ldap/*
systemctl start slapd
```

add h
```
cd /etc/openldap/schema
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f nis.ldif
```
create password
```
cd
slappasswd -s pass -n > passwd
cat passwd
```
create config
```
vim config.ldif (needs to create)
```
