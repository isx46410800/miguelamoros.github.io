# Archivos importantes de Linux

+ Usuarios del sistema (user.pass,uid,gid,gecos,home,shell)  
`/etc/passwd`  
+ Grupos del sistema (gnamegroup,pass,gid,list users)  
`/etc/group`  
+ Contraseñas de los usuarios (user,pass,last passwd change,min dias para cambiar pass,max dias con misma pass,warning dias aviso pass,inactivity,expire account)  
`/etc/shadow`  
+ Dominio DNS  
`/etc/resolv.conf`  
+ Lo que monta el sistema al encenderse(filesystem,mountpoint,type,options,dump,check):  
`/etc/fstab`
+ Hosts del sistema (IP->nombre)  
`/etc/hosts` # #192.168.1.41 miguel
+ Cambiar host de nombre:  
`/etc/hostname` miguel  
+ File para hacer crons:  
`/etc/crontab`  
+ Grub del sistema:  
`/boot/grub2/grub.cfg`
+ Negar Ips ssh:  
`/etc/hosts.deny`
+ Ficheros SSH
`/etc/ssh/sshd_config` `/etc/ssh/ssh_config`
+ Ficheros xinetd:  
`/etc/xinetd.d`
+ Cosas exportables:  
`/etc/exports`
+ Samba:  
`/etc/samba/smb.conf`
+ Named:  
`/etc/named`
+ APACHE HTTP:  
`/etc/httpd/conf/httpd.conf`  
`/var/www/html/index.html`
+ Mail:  
`/var/spool/mail`  
`/etc/mail/sendmailcf-mc`
+ Volumenes en Docker:  
`/var/lib/docker/volumes`
+ Usuarios con los que hemos hecho contacto SSH:  
`/.ssh/known_hosts`  
`/.ssh/authorized_keys`
