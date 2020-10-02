# Comandos LINUX 

+ Hacer un listado:  
`ls -la`
+ Manual de un comando(1-ordenes, 5-ficheros, 8-admin):  
`man comando`
+ Ayuda de un comando:  
`comando --help`
+ Crear/ver particiones:  
`fdisk`  
`fdisck /dev/sda0`  
+ Editor:  
`vim file.txt`
+ Ver un archivo:  
`cat file.txt`
+ Montar algo:  
`mount`  
`mount -t type device dir`  #mount -t ext4 /dev/sda5 /mnt
`mount /dir`  
+ Montar todo lo que tenemos para montar:  
`mount -a`
+ Ver tipo de cosas montadas o si está montado algo:  
`mount -t ext4`
+ Cambiar directorio:  
`cd dir`  
`cd ..`  
`cd dir/file.txt`  
`cd /var/tmp`  
+ Ver path de donde estoy:  
`pwd`  
+ Crear directorio:  
`mkdir dir`  
`mkdir -p /dir1/dir2/dir2/`
+ Borrar directorio(vacío):  
`rmdir dir`  
+ Borrar dir/ficheros:  
`rm -rf dir/file`
+ Buscar una cadena, palabra..:  
`grep [opciones] [el qué] [donde]` #grep -i web install.txt
+ Fecha/hora:  
`date`
+ Calendario:  
`cal`  
`cal 3 2020`
+ Información de nuestro usuario:  
`who`
+ Indica el usuario:  
`whoami`
+ Información de la sesión:  
`w`  
+ Cual es el S.O.:  
`uname -a`
+ Tiempo de la sesión:  
`uptime`
+ Cual es nuestro host:  
`hostname`
+ Info de los usuarios del sistema:  
`finger`
+ Numero identificación del usuario en el sistema:  
`id`
+ Ejecutable y man de un comando:  
`whereis comando`
+ Lo que hace el ejecutable de un comando:  
`which comando`  
+ Buscar un fichero o algo de esa palabra en el sistema:  
`locate palabra`
+ Primeras o ultimas 10 lineas de un fichero o busqueda:  
`head -n10 /etc/passwd`  
`tail -n10 /etc/group`  
+ Ver procesos en tiempo real:  
`top`  
`htop`
+ Tipo de fichero:  
`file`  
+ Contar lineas de un archivo:  
`nl file.txt`  
+ Dar un numero aleatorio de un rango de numeros:  
`shuf -i 10-20 -n 1`  
+ Texto que imprime o carga el kernel:  
`dmesg`
+ Procesos:  
`ps`  
`ps -u isx46410800`  
`ps -ax` 
`ps -p nºproces` #indica cual es el proceso  
`pidof nameproceso` #pids de este proceso  
`kill proceso` `kill -nº proceso` `killall proceso`  
`kill -l` #9 mata #15 termina  #19 para
`jobs`  `kill %job`  
`ordre &` #hacerlo en backgroung  
`fg %job` #hacerlo en foreground  
`nohup orden &`  #desliga un proceso de la terminal
`disown %job` # lo mismo  
+ Contar palabras, lineas...
`wc`  
`wc -l`  
`wc -c`  
+ Ver estructura de árbol de directorios:  
`tree`
+ Copiar ficheros:  
`cp [cosas..] [a donde]`  
`cp -r [dir/(cosas)] [a donde]`
+ Cambiar nombre de fichero o directorio:  
`mv nombre nuevonombre`  
+ Meter cosas en ficheros:  
`echo "hola" > file.txt`  
`cat > file.txt`  
`ls -la > file.txt`
+ Pathname Expansion:  
```
* puede ser nada o muchas cosas
ls *.txt
? cada ? es un char
ls ???.*
[25] coge 2 o 5
ls fit[25].txt
[1-4] coge un char del 1 al 4
ls fit[-4].txt
[7am7-8][0-9] coge un char del primero y otro del segundo
ls fit[7am7-8][0-9].txt # fita8.txt
[^abc] que no sea ni a ni b ni c
ls fit[^abc].txt
```
+ Info técnica de un file:  
`stat file.txt`
+ Ver inodos:  
`ls -i`
+ Crear hard link(no entre dirs ni entre file system diferentes, tamaño mismo):  
`ln [de que cosa] [hacia donde cosa nueva]` # ln file.txt /tmp/filenou.txt (mismo inodo apuntan, misma xixa)
+ Crear simbolic link(equivale a un acceso directo, tamaño es el nombre):  
`ln -s [de que cosa] [simbolic link creas nuevo]` # ln -s file.txt file2.txt
+ Renombre de muchos archivos:  
`rename [donde dice tal cosa] [poner tal cosa] [a estos ficheros]` # rename foo foo0 foo*
+ Comparar ficheros:   
`cmp/diff/diff3 file1 file2`
+ Separar ficheros:  
`split -n3/-b10k file prefijo`
+ Fichero ejecutable:  
`chmod +x file`
+ Comprimir/descomprimir ficheros:  
```
gzip file ->  file.gz
gunzip file.gz
bzip2 file -> file.bzp2
bunzip2 file.bzp2
```
+ Apagar o cerrar sesión:  
`exit/poweroff/reboot/logout`
+ Instalar un paquete:  
`dnf install paquete -y`
+ Buscar un paquete:  
`dnf search paquete`
+ Donde esta el paquete:  
`dnf provides paquete`
+ Lista contenido de un paquete:  
`rpm -ql paquete` | grep bin #busca los ejecutables
+ Lista de paquetes instalados:  
`rpm -qa`
+ Acciones con paquetes:  
`dnf upgrade/update/reinstall/info paquete`
+ Repositorios:  
`dnf repolist --all`
+ Permisos(r-leer,mirar,copiar/w-leer,modificar/x-ejecutable):  
`chmod 640 file/dir`  
`chmod +rx file/dir`  
+ Cambiar el propietario de un file/dir(root):  
`chown user.group file/dir`  
+ Cambiar el grupo de un file/dir:  
`chgrp grupo file`
+ Agregar usuario:  
`useradd usuario`
`useradd usuario -g gprincipal -G gsecundario`
+ Contraseña usuario:  
`passwd usuario`  
+ Crear grupo:  
`groupadd grupo`
+ Borrar usuario y todo suyo:  
`userdel -r usuario`
+ Redireccionamientos:  
```
0 - stdin
1 - stdout
2 - stderr
2> salida de errores
< entrada
> salida
2>&1 donde esta la salida de errores rederiger a la stdout
```
+ Traducir:  
`tr -s '[a-z]' '[A-Z]'`  
`tr -s '[:blanck:]' ' '`
+ Ver espacio ocupado en disco:  
`du / du -sh /tmp`
+ Ver variables predefinidas del sistema:  
`set`
+ Crear/eliminar variables, mayus SISTEMA, minus USUARIO:  
```
nom=valor
nom="el valor"
usuario=$(id)
unset nom
```
+ Crear subbash:  
`bash`
+ Arbol de procesos:  
`pstree`
+ Exportar variable a otros niveles ENVIROMENT:  
`export variable`
+ Crear alias:  
```
alias listar='ls -la'
alias quiensoy='id;whoami'
unalias listar
```
+ Command substitution:  
`$(orden)` # file $(ls)
+ Brace expansion:  
```
mkdir dir{1..20}
echo hisx{1,2}-{01-20}
```
+ Aritmetic expansion:  
`echo $((2*8))`
+ Cron o tareas programadas:  
```
at 9:12 --> >cal, date.. #crear tarea programada
atq # lista de tareas
atrm # borra tareas
cron (file /etc/crontab)
(min-horas-dia-mes-diasemana(0-7)-ordre)
15 14 1  * * script.sh
crontab -l #lista
crontab -e #crea o edita
crontab -r #borra
```
+ Ordenar:  
```
sort
sort -r
sort -t: -k3 /etc/passwd #campo 3
sort -t: -k3rg,3 /etc/passwd #descente y de numeric
sort -u #unico
```
+ Lista hardware:  
`lshw`
+ Hora del hardware clock:  
`hwclock`
+ Ordens grub:
```
grub2-install /dev/sda
grub2-mkconfig -o /boot/grub2/grub.cfg
```
+ Ordenes en debian:  
```
apt-get install paquete
dpkg -i paquete
```
+ Compresión de archivos:  
```
tar -cvf nombreTar archivosAcomprimir
-c crea
-v verbose
-x descomprimir
-f nombre archivo
-p permisos para dir
tar -zcvf nombreGZIP ficheros
tar -jcvf nombreBZIP2 ficheros
tar -Jcvf nombreXZ ficheros
```
+ Backup:  
`tar --listed-incremental fichero.snar -czpf fichero-incremental.tar.gz directorio/.`
+ SSH:
```
systemctl start sshd
ssh hostname/ip #conectarte
ssh user@server -P puerto
ssh -p 22 i03/0.0.0.0 #conectarte
ssh-keygen #crea llaves
ssh -P puerto [fichero] [ip:a donde/.] #copiar fichero
scp user@server:file  user@server:/dirdestino
scp origen destino
ssh-copy-id user@ip #copia mi publica a ese ip con ese usuario
```
+ FTP:  
```
ftp ip/host
get file
put filecopy filedesti
wget schema://host-uri-ip/ruta-files #wget ftp://user10@localhost/file.txt
```
+ Routing:  
```
ifconfig
ip a
ip r
nslookup host/web
netstat -putano
ping -c3 web/ip
nmap ip/localhost/host #ver puertos abiertos
telnet gost/ip puerto #GET / HTTP/1.0
```
+ Netcat conectar:  
```
nc -l puerto #conectarte ponte el tuyo a escuchar
nc hostname puerto #conectarse al puerto tuyo
```
+ Activar servicios:  
`systemctl start/stop/enable/disable servicio`
+ Cargar de nuevo los demonios:  
`systemctl daemon-reload`
+ Culpa de lo que tarda cada cosa al encenderse:  
`systemd-analyse blame`
+ Ver errores del sistema:  
`journalctl / journalctl -u servicio`
+ Cargar las cosas montables:  
`exportfs -rv`
+ SAMBA:  
```
meter lo compartido en smb.conf
//server/recurso
smbtree -L #lista
smbtree -D #ver el dominio
smbtree -S #ver el servicio
smbclient //j17/manuals (-U marta)
smbget smb://localhost/manuals/man1/ls
mount -t -v cifs //localhost/manuals /mnt -o guest
smbpasswd -a miguel #añade user samba
pdbeddit -L #lista de cuentas samba
```
+ MAIL:  
`mail -v -s asunto aquien`  
`sendmail -bv user`  
`mailq`
+ Conectarte a AMAZON AWS:  
`ssh -i ~/ssh/key.pem fedora@IPamazon`
+ Servicios mas comunes:  
```
/sbin/httpd
/sbin/sshd
```









































































