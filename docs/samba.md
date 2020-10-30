# SAMBA  

+ Dos demonios: smbd y nmbd.  

+ Fichero de conf smb.conf.  

+ Topologia de red Peer to Peer: una red entre iguales, a su bola. Cliente/servidor.  

+ Puertos 139(netbios-ssn) y 445(microsoft-ds)  

+ `testparm` test de configuracion de samba

+ `smbtree` hace un mensaje de broadcaste y salen los que contesten.  

+ Forma: `\\server\recurs`

## INSTALACION  

+ Dockerfile:  
```
# Version: 0.0.1
# @edt M06 2019-2020
# samba
# -------------------------------------
FROM fedora:27
LABEL author="Miguel Amoros"
LABEL description="SAMBA server 2019-2020 - PAM"
RUN dnf -y install procps samba samba-client nss-pam-ldapd cifs-utils passwd pam_mount authconfig
RUN mkdir /opt/docker
COPY * /opt/docker/
RUN chmod +x /opt/docker/install.sh /opt/docker/startup.sh
WORKDIR /opt/docker
CMD ["/opt/docker/startup.sh"]
```

+ Authconfig.sh:  
```
#! /bin/bash
authconfig --enableshadow --enablelocauthorize --enableldap --enableldapauth --enablemkhomedir --ldapserver='ldapserver' --ldapbase='dc=edt,dc=org' --updateall
```  

+ Install.sh:  
```
#! /bin/bash
# @edt ASIX M06 2019-2020
# instal.lacio
# -------------------------------------
# creacio usuaris locals
useradd local1
useradd local2
useradd local3
echo "local1" | passwd --stdin local1
echo "local2" | passwd --stdin local2
echo "local3" | passwd --stdin local3
# Configuració client autenticació ldap
bash /opt/docker/auth.sh
# SAMBA
## Configuracio shares Samba
mkdir /var/lib/samba/public
chmod 777 /var/lib/samba/public
cp /opt/docker/* /var/lib/samba/public/.
mkdir /var/lib/samba/privat
#chmod 777 /var/lib/samba/privat
cp /opt/docker/*.md /var/lib/samba/privat/.
cp /opt/docker/smb.conf /etc/samba/smb.conf
## creacio usuaris unix locals i samba locals
useradd lila
useradd roc
useradd patipla
useradd pla
echo -e "lila\nlila" | smbpasswd -a lila
echo -e "roc\nroc" | smbpasswd -a roc
echo -e "patipla\npatipla" | smbpasswd -a patipla
echo -e "pla\npla" | smbpasswd -a pla
```

+ Startup.sh:  
```
#! /bin/bash
# @edt ASIX M06 2019-2020
# startup.sh
# -------------------------------------
#instalacio / preparacio
/opt/docker/install.sh && echo "Install Ok"
# activar els serveis ldap
/sbin/nscd && echo "nscd Ok"
/sbin/nslcd && echo "nslcd Ok"
# activar els serveis samba
/usr/sbin/smbd && echo "smb Ok"
# creacion de users samba dels users ldap, creando sus cuentas y directorios
bash /opt/docker/usersSambaUnixLdap.sh
# servei per a detached
/usr/sbin/nmbd -F
```  

+ usersldap.sh:  
```
llistaUsers="pere marta anna pau pere jordi"
for user in $llistaUsers
do
	echo -e "$user\n$user" | smbpasswd -a $user
	line=$(getent passwd $user)
	uid=$(echo $line | cut -d: -f3)
	gid=$(echo $line | cut -d: -f4)
	homedir=$(echo $line | cut -d: -f6)
	echo "$user $uid $gid $homedir"
	if [ ! -d $homedir ]; then
		mkdir -p $homedir && echo "directori home creat"
		cp -ra /etc/skel/. $homedir && echo "skel copiado"
		chown -R $uid.$gid $homedir && echo "chown ok"
	fi
done
for user in user{01..05}
do
	echo -e "jupiter\njupiter" | smbpasswd -a $user && echo "usuari $user creat"
	line=$(getent passwd $user)
	uid=$(echo $line | cut -d: -f3)
	gid=$(echo $line | cut -d: -f4)
	homedir=$(echo $line | cut -d: -f6)
	echo "$user $uid $gid $homedir"
	if [ ! -d $homedir ]; then
		mkdir -p $homedir
		cp -ra /etc/skel/. $homedir
		chown -R $uid.$gid $homedir
	fi
done
for user in user{06..10}
do
	echo -e "jupiter\njupiter" | smbpasswd -a $user && echo "usuari $user creat"
	line=$(getent passwd $user)
	uid=$(echo $line | cut -d: -f3)
	gid=$(echo $line | cut -d: -f4)
	homedir=$(echo $line | cut -d: -f6)
	echo "$user $uid $gid $homedir"
	if [ ! -d $homedir ]; then
		mkdir -p $homedir
		cp -ra /etc/skel/. $homedir
		chown -R $uid.$gid $homedir
	fi 
done
```  

## FICHEROS  

+ smb.conf:  
```
[global]
        workgroup = MYGROUP
        server string = Samba Server Version %v
        log file = /var/log/samba/log.%m
        max log size = 50
        security = user
        passdb backend = tdbsam
        load printers = yes
        cups options = raw
[homes]
        comment = Home Directories
        browseable = no
        writable = yes
;       valid users = %S
;       valid users = MYDOMAIN\%S
[printers]
        comment = All Printers
        path = /var/spool/samba
        browseable = no
        guest ok = no
        writable = no
        printable = yes
[documentation]
        comment = Documentació doc del container
        path = /usr/share/doc
        public = yes
        browseable = yes
        writable = no
        printable = no
        guest ok = yes
[manpages]
        comment = Documentació man  del container
        path = /usr/share/man
        public = yes
        browseable = yes
        writable = no
        printable = no
        guest ok = yes
[public]
        comment = Share de contingut public
        path = /var/lib/samba/public
        public = yes
        browseable = yes
        writable = yes
        printable = no
        guest ok = yes
[privat]
        comment = Share d'accés privat
        path = /var/lib/samba/privat
        public = no
        browseable = no
        writable = yes
        printable = no
        guest ok = yes
```  

## ORDENES  

+ smbtree
+ smbtree -D
+ smbtree -S
+ smbclient (-N) //server/recurs
+ smbclient //server/recurs -U user%password
+ smbget -R smb://server/recurs/file
+ mount -t cifs //server/share / mnt -o guest
+ /etc/samba/smb.conf
+ /etc/samba/lmhosts
+ useradd lila -> smbpasswd -a lila
+ borrar: smbpasswd -x lila
+ smbclient -d0 //samba/public -u lila
+ pdbedit // pdbedit -Lv