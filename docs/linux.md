# Comandos LINUX 

- [APUNTES LPIC](https://apuntes.de/linux-certificacion-lpi/#gsc.tab=0)  

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

## COMANDOS SYSADMIN  

Linux Commands frequently used by Linux Sysadmins – Part 1:
1. ip – from Iproute2, a collection of utilities for controlling TCP/IP networking and traffic control in Linux.  
2. ls – list directory contents.  
3. df – display disk space usage.  
4. du – estimate file space usage.  
5. free – display memory usage.  
6. scp – securely Copy Files Using SCP, with examples.  
7. find – locates files based on some user-specified criteria.  
8. ncdu – a disk utility for Unix systems.  
9. pstree – display a tree of processes.  
10. last – show a listing of last logged in users.  
11. w – show a list of currently logged in user sessions.  
12. grep – Search a file for a pattern of characters, then display all matching lines.  

 

Linux Commands frequently used by Linux Sysadmins – Part 2:  
13. uptime – shows system uptime and load average.  
14. top – shows an overall system view.  
15. vmstat – shows system memory, processes, interrupts, paging, block I/O, and CPU info.  
16. htop – interactive process viewer and manager.  
17. dstat – view processes, memory, paging, I/O, CPU, etc., in real-time. All-in-one for vmstat, iostat, netstat, and ifstat.  
18. iftop – network traffic viewer.  
19. nethogs – network traffic analyzer.  
20. iotop – interactive I/O viewer. Get an overview of storage r/w activity.  
21. iostat – for storage I/O statistics.  
22. netstat – for network statistics.  
23. ss – utility to investigate sockets.  
24. atop – For Linux server performance analysis.  
25. Glances and nmon – htop and top Alternatives:  
26. ssh – secure command-line access to remote Linux systems.  
27. sudo – execute commands with administrative privilege.  
28. cd – directory navigation.  
29. pwd – shows your current directory location.  
30. cp – copying files and folders.  
31. mv – moving files and folders.  
32. rm – removing files and folders.  
33. mkdir – create or make new directories.  
34. touch – used to update the access date and/or modification date of a computer file or directory.  
35. man – for reading system reference manuals.  
36. apropos – Search man page names and descriptions.  

 

Linux Commands frequently used by Linux Sysadmins – Part 3:  
37. rsync – remote file transfers and syncing.  
38. tar – an archiving utility.  
39. gzip – file compression and decompression.  
40. b2zip – similar to gzip. It uses a different compression algorithm.  
41. zip – for packaging and compressing (to archive) files.  
42. locate – search files in Linux.  
43. ps – information about the currently running processes.  
44. Making use of Bash scripts. Example: ./bashscript.sh  
45. cron – set up scheduled tasks to run.  
46. nmcli – network management.  
47. ping – send ICMP ECHO_REQUEST to network hosts.  
48. traceroute – check the route packets take to a specified host.  
49. mtr – network diagnostic tool.  
50. nslookup – query Internet name servers (NS) interactively.  
51. host – perform DNS lookups in Linux.  
52. dig – DNS lookup utility.  

 

Linux Commands frequently used by Linux Sysadmins – Part 4:  
53. wget – retrieve files over HTTP, HTTPS, FTP, and FTPS.  
54. curl – transferring data using various network protocols. (supports more protocols than wget)  
55. dd – convert and copy files.  
56. fdisk – manipulate the disk partition table.  
57. parted – for creating and manipulating partition tables.  
58. blkid – command-line utility to locate/print block device attributes.  
59. mkfs – build a Linux file system.  
60. fsck –  tool for checking the consistency of a file system.  
61. whois – client for the whois directory service.  
62. nc – command-line networking utility. (Also, see 60 Linux Networking commands and scripts.)  
63. umask – set file mode creation mask.  
64. chmod – change the access permissions of file system objects.  
65. chown – change file owner and group.  
66. chroot – run command or interactive shell with a special root directory.  
67. useradd – create a new user or update default new user information.  
68. userdel – used to delete a user account and all related files.  
69. usermod – used to modify or change any attributes of an existing user account.  

 

Linux Commands frequently used by Linux Sysadmins – Part 5:  
70. vi – text editor.  
71. cat – display file contents.  
72. tac – output file contents, in reverse.  
73. more – display file contents one screen/page at a time.  
74. less – similar to the more command with additional features.  
75. tail – used to display the tail end of a text file or piped data.  
76. dmesg – prints the message buffer of the kernel ring.  
77. journalctl – query the systemd journal.  
78. kill – terminate a process.  
79. killall  – Sends a kill signal to all instances of a process by name.  
80. sleep – suspends program execution for a specified time.  
81. wait – Suspend script execution until all jobs running in the background have been terminated.  
82. nohup – Run Commands in the Background.  
83. screen – hold a session open on a remote server. (also a full-screen window manager)  
84. tmux – a terminal multiplexer.  
85. passwd – change a user’s password.  
86. chpassword –  
87. mount / umount – provides access to an entire filesystem in one directory.  
88. systemctl – Managing Services (Daemons).  
89. clear – clears the screen of the terminal.  
90. env -Run a command in a modified environment.  

 

Misc commands:  
91. cheat – allows you to create and view interactive cheatsheets on the command-line.”  
92. tldr – Collaborative cheatsheets for console commands.  
93. bashtop – the ‘cool’ top alternative.  
94. bpytop – Python port of bashtop.  


This list of Linux Networking commands and scripts will receive ongoing updates, similar to the other lists on this blog…

aria2 – downloading just about everything. Torrents included.  

arpwatch – Ethernet Activity Monitor.  

bmon – bandwidth monitor and rate estimator.  

bwm-ng – live network bandwidth monitor.  

curl – transferring data with URLs. (or try httpie)  

darkstat – captures network traffic, usage statistics.  

dhclient – Dynamic Host Configuration Protocol Client  

dig – query DNS servers for information.  

dstat – replacement for vmstat, iostat, mpstat, netstat and ifstat.

ethtool – utility for controlling network drivers and hardware.

gated – gateway routing daemon.

host – DNS lookup utility.

hping – TCP/IP packet assembler/analyzer.

ibmonitor – shows bandwidth and total data transferred.

ifstat – report network interfaces bandwidth.

iftop – display bandwidth usage.

ip (PDF file) – a command with more features that ifconfig (net-tools).

iperf3 – network bandwidth measurement tool. (above screenshot Stacklinux VPS)

iproute2 – collection of utilities for controlling TCP/IP.

iptables – take control of network traffic.

IPTraf – An IP Network Monitor.

iputils – set of small useful utilities for Linux networking.

iw – a new nl80211 based CLI configuration utility for wireless devices.

jwhois (whois) – client for the whois service.

“lsof -i” – reveal information about your network sockets.

mtr – network diagnostic tool.

net-tools – utilities include: arp, hostname, ifconfig, netstat, rarp, route, plipconfig, slattach, mii-tool, iptunnel and ipmaddr.

ncat – improved re-implementation of the venerable netcat.

netcat – networking utility for reading/writing network connections.

nethogs – a small ‘net top’ tool.

Netperf – Network bandwidth Testing.

netplan – Netplan is a utility for easily configuring networking on a linux system.

netsniff-ng – Swiss army knife for daily Linux network plumbing.

netwatch – monitoring Network Connections.

ngrep – grep applied to the network layer.

nload – display network usage.

nmap – network discovery and security auditing.

nmcli – a command-line tool for controlling NetworkManager and reporting network status.

nmtui – provides a text interface to configure networking by controlling NetworkManager.

nslookup – query Internet name servers interactively.

ping – send icmp echo_request to network hosts.

route – show / manipulate the IP routing table.

slurm – network load monitor.

snort – Network Intrusion Detection and Prevention System.

smokeping – keeps track of your network latency.

socat – establishes two bidirectional byte streams and transfers data between them.

speedometer – Measure and display the rate of data across a network.

speedtest-cli – test internet bandwidth using speedtest.net

ss – utility to investigate sockets.

ssh – secure system administration and file transfers over insecure networks.

tcpdump – command-line packet analyzer.

tcptrack – Displays information about tcp connections on a network interface.

telnet – user interface to the TELNET protocol.

tracepath – very similar function to traceroute.

traceroute – print the route packets trace to network host.

vnStat – network traffic monitor.

websocat – Connection forwarder from/to web sockets to/from usual sockets, in style of socat.

wget – retrieving files using HTTP, HTTPS, FTP and FTPS.

Wireless Tools for Linux – includes iwconfig, iwlist, iwspy, iwpriv and ifrename.

Wireshark – network protocol analyzer.







































































