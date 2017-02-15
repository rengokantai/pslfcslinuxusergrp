# pslfcslinuxusergrp
##1. intro
```
getent passwd   //list database
getent group
getent networks
getent services
grep passwd /etc/nsswitch.conf
```
##2. login scripts
###1 Login Scripts in centos 7
login shell (run once, store path variables)
```
~/.bash_profile  /etc/profile
~/.bashrc /etc/.bashrc
```
non-login shell (show every times)
```
~/.bashrc /etc/.bashrc
```
###2 execution order
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
###5
```
ls /etc/bash*
ls /etc/profile*
```
ps1, \W = relative path \w = full path
###home dicaxureaxt
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
echo 'user2:pass' |sudo chpasswd   //redhat
echo pass | sudo passwd user3 --stdin  //only in redhat
```
######password age
```
chage -l usename
sudo pwwnconv //show encrypted password in /etc/passwd
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

##5. managing local group
###1 creating local group
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
gpasswd user group
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
##7. Implementing OpenLDAP Directories on CentOS 7
###2 Installing OpenLDAP and Firewall Configuration
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
ls -l /var/lib/ldap
slaptest  //fail this time
```
change owner
```
chown ldap:ldap /var/lib/ldap/* && systemctl start slapd   // ldap.ldap  = ldap:ldap
```

add h
```
cd /etc/openldap/schema
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f cosine.ldif && ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f nis.ldif
```
create password
```
cd && slappasswd -s pass -n > passwd && cat passwd
```
create config
```
vim config.ldif (needs to create)
```
content
```
dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=mylabserver,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=mylabserver,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
#Password is Password1 or add your own
olcRootPW: {SSHA}8cUY6QugFU/Az2XjG1bhAw8U7cyPq1Va

dn: cn=config
changetype: modify
replace: olcLogLevel
olcLogLevel: 0

dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=Manager,dc=mylabserver,dc=com" read by * none
```
then
```
ldapmodify -Y EXTERNAL -H ldapi:/// -f config.ldif
```
######Creating the Directory Tree
create structure.ldif
```
dn: dc=mylabserver,dc=com
dc: mylabserver
objectClass: top
objectClass: domain

dn: ou=people,dc=mylabserver,dc=com
ou: people
objectClass: top
objectClass: organizationalUnit

dn: ou=group,dc=mylabserver,dc=com
ou: group
objectClass: top
objectClass: organizationalUnit
```
then
```
ldapadd -x -W -D "cn=Manager,dc=mylabserver,dc=com" -f structure.ldif
```
search
```
ldapsearch -x -W -D "cn=Manager,dc=mylabserver,dc=com" -b "dc=mylabserver,dc=com" -s sub "(objectclass=organizationalUnit)"
```
######Adding Users and Groups
create group.ldif
```
dn: cn=ldapusers,ou=group,dc=mylabserver,dc=com
objectClass: posixGroup
cn: ldapusers
gidNumber: 4000
```
then
```
ldapadd -x -W -D "cn=Manager,dc=mylabserver,dc=com" -f group.ldif
```

edit
```
vim /usr/share/migrationtools/migrate_common.ph
```
edit
```
# Default DNS domain
$DEFAULT_MAIL_DOMAIN = "mylabserver.com";

# Default base
$DEFAULT_BASE = "dc=mylabserver,dc=com";
```
then
```
grep -w user /etc/passwd >passwd   //this may return mutiple users contain word 'user'
/usr/share/migrationtools/migrate_passwd.pl passwd user.ldif
```
then edit, change to `ke`
```
vim user.ldif
```
add rule
```
ldapadd -x -W -D "cn=Manager,dc=mylabserver,dc=com" -f user.ldif
```
user.ldif:
```
dn: uid=ke,ou=People,dc=mylabserver,dc=com
uid: ke
cn: ke
objectClass: account
objectClass: posixAccount
objectClass: top
objectClass: shadowAccount
userPassword: {crypt}$6$/vxShd.R$eN1cnFtmcwT5nMlMWp8Rlq3NwTVILAYbIGFu3tiRcmPb00F
dNQDJ0KuoBHFyOg/PkGxrbDEg6ga3oBbE9Qcom/
shadowLastChange: 16423
shadowMin: 0
shadowWarning: 7
loginShell: /bin/bash
uidNumber: 4000
gidNumber: 4000
homeDirectory: /home/ke
gecos: ke
```
#####Implementing OpenLDAP Authentication in CentOS 7
######Installing the OpenLDAP Client
server2
```
echo "172.31.114.180 rengokantai1.mylabserver.com" >> /etc/hosts
```
install oddjob
```
yum install -y oddjob oddjob-mkhomedir
systemctl start oddjobd
yum install openldap-clients.x86_64 nss-pam-ldapd -y
```
gui:
```
authconfig-tui
```
then: choose useldap, use ldap auth->next
server: ldap://rengokantai1.mylabserver.com,then exit
```
authconfig --enableldap --ldapserver=rengokantai1.mylabserver.com --ldapbasedn="dc=mylabserver,dc=dom" --enablemkhomedir --update
```
then
```
grep passwd /etc/nsswitch.conf
```
######list user and groups
```
grep ldap /etc/nsswitch.conf
```
edit: /etc/nsswitch.conf
```
netgroup: files sss
automount: files
```
######Searching LDAP Users
```
ldapsearch -x -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com
ldapsearch -x -LLL -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com
ldapsearch -x -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com "(objectclass=account)"
ldapsearch -x -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com "(&(objectclass=account)(uid=ke))"
ldapsearch -x -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com "(&(objectclass=account)(uid=ke))" uidNumber uid
```
create new user
```
ldapsearch -x -H ldap://rengokantai1.mylabserver.com -b dc=mylabserver,dc=com "(&(objectclass=account)(uid=ke))" > newuser.ldif
vim newuser.ldif
```
replace ke to sally, 4000->4001
```
ldapadd -x -W -D "cn=Manager,dc=mylabserver,dc=com" -f newuser.ldif
```
#####Implementing Kerberos Authentication
######Configuring NTP
```
yum install -y ntp
vim /etc/ntp.conf
```
edit
```
restrict 172.31.127.100 mask 255.255.255.0 nomodify nostrap
```
then
```
systemctl start ntpd
firewall-cmd --add-service=ntp --permanent && firewall-cmd --reload
```

edit /etc/hosts in server1, add server2 hostname.(do this in server2).  
For server2:
```
yum install -y
vim /etc/ntp.conf
```
edit,add
```
server rengokantai1.mylabserver.com iburst perfer
```
then
```
systemctl start ntpd
ntpq -p
```
######Configuring the Kerberos Services
server1
```
yum install -y rng-tools.x86_64 krb5-server krb5-workstation pam_krb5 && systemctl start rngd
```
if bug arises
```
vim /usr/lib/systemd/system/rngd.service
```
edit
```
ExecStart=/sbin/rngd -f -r /dev/urandom
```
config
```
vim /etc/krb5.conf
```
edit
```
[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = mylabserver.COM
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
 mylabserver.COM = {
  kdc = rengokantai1.mylabserver.com
  admin_server = rengokantai1.mylabserver.com
 }

[domain_realm]
.mylabserver.com = mylabserver.COM
mylabserver.com = mylabserver.COM
```
then create master pass
```
kdb5_util create -s -r mylabserver.COM
systemctl start krb5kdc kadmin
```
######Adding Kerberos Principals
```
netstat -ltn
firewall-cmd --add-service=kpasswd --permanent && firewall-cmd --add-service=kpasswd --permanent && firewall-cmd --add-service=749/tcp --permanent && firewall-cmd --reload
```
go to panal
```
kadmin.local
: listprincs
: addprinc root/admin (type pass)
: addprinc user
: addprinc -randkey host/rengokantai1.mylabserver.com
: ktadd host/rengokantai1.mylabserver.com
quit
```
######Enabling Kerberos Authentication
SERVER1:
```
 vim /etc/ssh/ssh_config
```
edit
```
GSSAPIAuthentication yes
GSSAPIDelegateCredentials yes
```   
restart
```
systemctl restart sshd && authconfig --enablekrb5 --update
```
then switch to user account.
```
exit
klist //not found
kinit (login, then klist)
kdestroy  //delete key
```
######Adding Additional Hosts
server2
```
yum install -y krb5-workstation pam_krb5
scp user@rengokantai1:/etc/krb5.conf /etc
```
connect (not kadmin.local)    (here it fails, should return back to server1 and use kadmin.local
```
kadmin
```
then
```
addprinc -randkey host/rengokantai2.mylabserver.com
ktadd host/rengokantai2.mylabserver.com
```
then
```
 vim /etc/ssh/ssh_config
```
edit
```
GSSAPIAuthentication yes
GSSAPIDelegateCredentials yes
```   
restart
```
systemctl restart sshd && authconfig --enablekrb5 --update
```
