# LVM  

+ Logical Volum Management

+ 1 particion extensa, 4 primarias, 16 logicas. 

## COMANDOS  

CREAMOS IMAGEN Y ASIGNAMOS A UN LOOP  

+ dd if=/dev/zero of=file.img bs=1024 count=1024

+ losetup /dev/loop0 file.img

+ losetup -a/-d


CREAMOS UN PHYSICAL VOLUM

+ pvcreate /dev/loop0

+ pvdisplay /dev/loop0


CREAMOS UN VOLUME GROUP

+ vgcreate nameVG /dev/loop0{...}

+ vgdisplay nameVG

+ blkid

+ tree /dev/disk


CREAMOS UN LOGICAL VOLUME

+ lvcreate -L tamaño -n nameLV /dev/nomVG

+ lvcreate -l 100%libre -n nameLV /dev/nomVG

+ lvdisplay /dev/nameVG/nameLV


FORMATEAMOS

+ mkfs -t ext4 /dev/nameVG/nameLV

+ mkdir /mnt/dades

+ mount /dev/nameVG/nameLV /mnt/dades

+ df -h -t ext4


EXTENDEMOS

+ vgextend /dev/nameVG /dev/loop2

+ lvextend -L +30M /dev/nameVG/nameLV /dev/loop2

+ resize2fs /dev/nameVG/nameLV


REDUCIMOS

+ umount /dev/nameVG/nameLV

+ e2fsck -f /dev/nameVG/nameLV

+ resize2fs /dev/nameVG/nameLV 56M

+ mount /dev/nameVG/nameLV /mnt/xxx

+ lvreduce -L 56M -r /dev/nameVG/nameLV


DESHACEMOS

+ umount /mnt/dades

+ lvremove /dev/nameVG/nameLV

+ vgremove /dev/nameVG

+ pvremove /dev/loop0

+ losetup -d /dev/loop0


## EJERCICIO PRACTICA  

### RAID  

RAID
1.	Crear tres particions de 5GBytes al HD, corresponents a sda2, sda3 i sda4.
# Entramos en nuestra tabla de particiones de /dev/sda para crear las particiones:
[root@i21 ~]# fdisk /dev/sda

# creamos 3 particiones primarias(mismos pasos para las 3):
# opcion n para nueva particion:
Command (m for help): n

# indicamos que es primaria
Partition type
   p   primary (0 primary, 1 extended, 3 free)
   l   logical (numbered from 5)
Select (default p): p

# indicamos el numero de particion, enter para por defecto (este caso 2)
Partition number (2-4, default 2):

# indicamos desde donde empieza(por defecto desde el primer sitio libre)
First sector (429918208-468862127, default 429918208):

#indicamos la medida que queremos, en este caso 5GB
Last sector, +sectors or +size{K,M,G,T,P} (429918208-468862127, default 468862127): +5G

Created a new partition 2 of type 'Linux' and of size 5 GiB.
Partition #2 contains a ext4 signature.

# borramos la firma
Do you want to remove the signature? [Y]es/[N]o: y
Disk /dev/sda: 223.6 GiB, 240057409536 bytes, 468862128 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x173e314d

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1            2048 429918207 429916160  205G  5 Extended
/dev/sda2       429918208 440403967  10485760    5G 83 Linux
/dev/sda3       440403968 450889727  10485760    5G 83 Linux
/dev/sda4       450889728 461375487  10485760    5G 83 Linux
/dev/sda5            4096 209719295 209715200  100G 83 Linux
/dev/sda6  *    209721344 419436543 209715200  100G 83 Linux
/dev/sda7       419438592 429918207  10479616    5G 82 Linux swap / Solaris

# Para finalizar apretamos ‘w’ para guardar y hacemos un partprobe
[root@i21 ~]# partprobe



2.	Crear un RAID de nivell 1 utilitzant les particions anteriors. Usar dos discs més un de spare. Mostrar el raid: la descripció i el procés..
# creamos el raid 1 con dos discos (sda2 y sda3) y uno de spare(sda4)
[root@i21 ~]# mdadm -v --create /dev/md/raid --level=1 --raid-devices=2 /dev/sda2 /dev/sda3 --spare-devices=1 /dev/sda4
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 5238784K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md/raid started.

#Vemos la descripcion
[root@i21 ~]# mdadm --detail /dev/md/raid
/dev/md/raid:
        Version : 1.2
  Creation Time : Thu Feb 13 10:56:41 2020
     Raid Level : raid1
     Array Size : 5238784 (5.00 GiB 5.36 GB)
  Used Dev Size : 5238784 (5.00 GiB 5.36 GB)
   Raid Devices : 2
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Thu Feb 13 10:57:07 2020
          State : clean
 Active Devices : 2
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 1

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 17

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3

       2       8        4        -      spare   /dev/sda4

#Vemos el proceso
[root@i21 ~]# cat /proc/mdstat
Personalities : [raid1]
md127 : active raid1 sda4[2](S) sda3[1] sda2[0]
      5238784 blocks super 1.2 [2/2] [UU]
      
unused devices: <none>
 
3.	Assignar format al RAID i muntar-lo al directori /mnt/raid. Copiar-hi tot el directori /bin i posar-hi un fitxer xixa.dat de 3G. Mostrar amb df  -h l'ocupació.
# formateamos
[root@i21 ~]# mkfs -t ext4 /dev/md/raid
mke2fs 1.43.5 (04-Aug-2017)
Discarding device blocks: done                            
Creating filesystem with 1309696 4k blocks and 327680 inodes
Filesystem UUID: 983460ab-c0f1-41dc-91e0-7c99fa6a266c
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done



# creamos el directorio en el cual montaremos el raid
[root@i21 ~]# mkdir /mnt/raid

# lo montamos
[root@i21 ~]# mount /dev/md/raid /mnt/raid

# vemos que está montado
[root@i21 ~]# ll /mnt/raid/
total 16
drwx------. 2 root root 16384 Feb 13 11:02 lost+found

# copiamos el bin (estaba con simbolic link por lo que copiamos la info real)
[root@i21 ~]# cp -r /usr/bin/ /mnt/raid/

# creamos un file xixat.dat de 3g
[root@i21 ~]# dd if=/dev/zero of=xixa.dat bs=1k count=3M
3145728+0 records in
3145728+0 records out
3221225472 bytes (3.2 GB, 3.0 GiB) copied, 4.41942 s, 729 MB/s

# copiamos este fichero a mnt/raid
[root@i21 ~]# cp xixa.dat /mnt/raid/.

# vemos el contenido del directorio y su ocupacion
[root@i21 ~]# ll /mnt/raid/
total 3145800
dr-xr-xr-x. 2 root root      53248 Feb 13 11:12 bin
drwx------. 2 root root      16384 Feb 13 11:02 lost+found
-rw-r--r--. 1 root root 3221225472 Feb 13 11:10 xixa.dat
[root@i21 ~]# df -h -t ext4 /dev/md127
Filesystem      Size  Used Avail Use% Mounted on
/dev/md127      4.9G  3.8G  829M  83% /mnt/raid


4.	Passar el RAID a nivell 5. Contesta i mostra quants discs en total té el raid i quants d’actius (proc i descripció).
# pasamos a raid 5
[root@i21 ~]# mdadm --grow /dev/md/raid --level=5
mdadm: level of /dev/md/raid changed to raid5

#vemos el proceso
[root@i21 ~]# cat /proc/mdstat
Personalities : [raid1] [raid6] [raid5] [raid4]
md127 : active raid5 sda4[2](S) sda3[1] sda2[0]
      5238784 blocks super 1.2 level 5, 64k chunk, algorithm 2 [2/2] [UU]
      
unused devices: <none>

#vemos como está formado el raid5
[root@i21 ~]# mdadm --detail /dev/md/raid
/dev/md/raid:
        Version : 1.2
  Creation Time : Thu Feb 13 10:56:41 2020
     Raid Level : raid5
     Array Size : 5238784 (5.00 GiB 5.36 GB)
  Used Dev Size : 5238784 (5.00 GiB 5.36 GB)
   Raid Devices : 2
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Thu Feb 13 11:15:04 2020
          State : clean
 Active Devices : 2
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 1

         Layout : left-symmetric
     Chunk Size : 64K

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 18

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3

       2       8        4        -      spare   /dev/sda4

tiene 3 discos , dos activos y uno en spare

5.	Fes els passos necessaris perquè el RAID tingui tres discs actius. Mostrar-ho amb proc i descripció.
# creo un nuevo disco y lo asigno a un loop, despues lo añado al raid 5
[root@i21 ~]# dd if=/dev/zero of=disc04.img bs=1k count=5M
5242880+0 records in
5242880+0 records out
5368709120 bytes (5.4 GB, 5.0 GiB) copied, 7.36486 s, 729 MB/s
[root@i21 ~]# losetup /dev/loop0 disc04.img
[root@i21 ~]# mdadm --grow /dev/md127 --level=5
mdadm: level of /dev/md127 changed to raid5
[root@i21 ~]# mdadm --grow /dev/md127 --raid-devices=3 --add /dev/loop0
mdadm: added /dev/loop0

# provoco un fallo de este disco que estaba activo en el raid
[root@i21 ~]# mdadm /dev/md/raid --fail /dev/loop0
mdadm: set /dev/loop0 faulty in /dev/md/raid
 Active Devices : 2
Working Devices : 3
 Failed Devices : 1
  Spare Devices : 1

         Layout : left-symmetric
     Chunk Size : 64K

 Reshape Status : 97% complete
  Delta Devices : 1, (2->3)

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 45

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3
       3       7        0        2      faulty   /dev/loop0

       2       8        4        -      spare   /dev/sda4

# borro el disco y lo vuelvo añadir
# entonces el de spare ocupa su puesto y se activa y el nuevo disco se queda como spare
[root@i21 ~]# mdadm /dev/md/raid --remove /dev/loop0
mdadm: hot removed /dev/loop0 from /dev/md/raid
[root@i21 ~]# mdadm /dev/md/raid --add /dev/loop0
mdadm: added /dev/loop0
[root@i21 ~]# cat /proc/mdstat
Personalities : [raid1] [raid6] [raid5] [raid4]
md127 : active raid5 loop0[3](S) sda4[2] sda3[1] sda2[0]
      10477568 blocks super 1.2 level 5, 64k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: <none>
[root@i21 ~]# mdadm --detail /dev/md127
/dev/md127:
        Version : 1.2
  Creation Time : Thu Feb 13 10:56:41 2020
     Raid Level : raid5
     Array Size : 10477568 (9.99 GiB 10.73 GB)
  Used Dev Size : 5238784 (5.00 GiB 5.36 GB)
   Raid Devices : 3
  Total Devices : 4
    Persistence : Superblock is persistent

    Update Time : Thu Feb 13 11:33:48 2020
          State : clean
 Active Devices : 3
Working Devices : 4
 Failed Devices : 0
  Spare Devices : 1

         Layout : left-symmetric
     Chunk Size : 64K

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 67

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3
       2       8        4        2      active sync   /dev/sda4

       3       7        0        -      spare   /dev/loop0

# provocamos fallo del loop y lo borramos
[root@i21 ~]# mdadm /dev/md/raid --fail /dev/loop0
mdadm: set /dev/loop0 faulty in /dev/md/raid
[root@i21 ~]# mdadm /dev/md/raid --remove /dev/loop0
mdadm: hot removed /dev/loop0 from /dev/md/raid

#mostramos resultados
[root@i21 ~]# mdadm --detail /dev/md127
/dev/md127:
        Version : 1.2
  Creation Time : Thu Feb 13 10:56:41 2020
     Raid Level : raid5
     Array Size : 10477568 (9.99 GiB 10.73 GB)
  Used Dev Size : 5238784 (5.00 GiB 5.36 GB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Thu Feb 13 11:37:36 2020
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 64K

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 69

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3
       2       8        4        2      active sync   /dev/sda4

[root@i21 ~]# cat /proc/mdstat
Personalities : [raid1] [raid6] [raid5] [raid4]
md127 : active raid5 sda4[2] sda3[1] sda2[0]
      10477568 blocks super 1.2 level 5, 64k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: <none>


6.	Contesta i mostra: l’espai de disc disponible al raid, l’espai actual i l’ocupació de disc que mostra df del raid muntat.
# espacio deisponible del raid
[root@i21 ~]# mdadm --detail /dev/md127
/dev/md127:
        Version : 1.2
  Creation Time : Thu Feb 13 10:56:41 2020
     Raid Level : raid5
     Array Size : 10477568 (9.99 GiB 10.73 GB)
  Used Dev Size : 5238784 (5.00 GiB 5.36 GB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Thu Feb 13 11:37:36 2020
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 64K

           Name : i21:raid  (local to host i21)
           UUID : 25fba14f:434b6e31:97a0d544:0dd4bfd1
         Events : 69

    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8        3        1      active sync   /dev/sda3
       2       8        4        2      active sync   /dev/sda4
# ocupacion en disco montado
[root@i21 ~]# df -h -t ext4 /dev/md127
Filesystem      Size  Used Avail Use% Mounted on
/dev/md127      4.9G  3.8G  829M  83% /mnt/raid












7.	Fes els canvis pertinents per tal de que el sistema de fitxers ocupi totalment l’espai disponible al raid, i mostra-ho.
#hacemos un resize2fs para que ocupe todo el espacio
[root@i21 ~]# resize2fs /dev/md/raid
resize2fs 1.43.5 (04-Aug-2017)
Filesystem at /dev/md/raid is mounted on /mnt/raid; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 2
The filesystem on /dev/md/raid is now 2619392 (4k) blocks long.

[root@i21 ~]# df -h -t ext4 /dev/md127
Filesystem      Size  Used Avail Use% Mounted on
/dev/md127      9.8G  3.8G  5.6G  41% /mnt/raid


8.	Descriu les ordres a fer per tal de poder reiniciar el sistema i disposar del raid actiu. Cal fer-ho (reboot) i  mostrar amb df l’ocupació.

# copiamos en el fichero etc/mdadm.conf la configuracion del raid
[root@i21 ~]# mdadm --examine -scan > /etc/mdadm.conf
[root@i21 ~]# cat /etc/mdadm.conf
ARRAY /dev/md/raid  metadata=1.2 UUID=25fba14f:434b6e31:97a0d544:0dd4bfd1 name=i21:raid
   spares=1

# en el fstab ponemos el montaje para que al reiniciarlo salga
/dev/md/raid    /mnt/raid       ext4    defaults        0       0

# hacemos el reboot
[root@i21 ~]# df -h -t ext4 /dev/md/raid
Filesystem      Size  Used Avail Use% Mounted on
/dev/md127      9.8G  3.8G  5.6G  41% /mnt/raid

Un cop fet desmunta /mnt/raid i elimina el muntatge automatittzat del fstab.
# Eliminariamos la linea introducida en el fstab
[root@i21 ~]# vim /etc/fstab
# Desmontariamos /mnt/raid
[root@i21 ~]# umount /mnt/raid
# parariamos el raid
mdadm –stop /dev/md/raid
# eliminariamos el fichero de conf /etc/mdadm.conf
# hariamos un mdadm –zero-superblock a cada particion


### LVM  

LVM
Partint 	del RAID creat a l'apartat anterior (Raid 1 dels discs sda2, sda3 i sda4) fer:
1.	Crear dins dos Volums Lògics anomenats hdsystem (1024 MBytes) i hddata (500 Mbytes). Mostrar clarament tots els passos per fer-ho.
# creamos el pv
[root@i21 ~]# pvcreate /dev/md/raid
WARNING: ext4 signature detected on /dev/md/raid at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/md/raid.
  Physical volume "/dev/md/raid" successfully created.
[root@i21 ~]# vgcreate mydisc /dev/md/raid

# creamos el vg
  Volume group "mydisc" successfully created

# creamos las lv
[root@i21 ~]# lvcreate -L 1024M -n hdsystem /dev/mydisc
  Logical volume "hdsystem" created.
[root@i21 ~]# lvcreate -L 500M -n hddata /dev/mydisc
  Logical volume "hddata" created.


2.	Mostrar la informació del volum físic, el grup de volum i els volums lògics.
# info del pv
[root@i21 ~]# pvdisplay /dev/md/raid
  --- Physical volume ---
  PV Name               /dev/md127
  VG Name               mydisc
  PV Size               9.99 GiB / not usable 4.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              2557
  Free PE               2176
  Allocated PE          381
  PV UUID               1pbO3s-krID-iQhm-xNzZ-ocAi-N9vw-uXdicW

# info del vg
[root@i21 ~]# vgdisplay /dev/mydisc
  --- Volume group ---
  VG Name               mydisc
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <9.99 GiB
  PE Size               4.00 MiB
  Total PE              2557
  Alloc PE / Size       381 / <1.49 GiB
  Free  PE / Size       2176 / 8.50 GiB
  VG UUID               IzpMcC-71Ri-1Xwc-8Vsw-Pwj1-N6lY-R33fKC





#info dels lv
[root@i21 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/mydisc/hdsystem
  LV Name                hdsystem
  VG Name                mydisc
  LV UUID                2jSCBs-01XX-P5pn-4lp2-OsKc-H5Jc-FsJD9h
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:57:49 +0100
  LV Status              available
  # open                 0
  LV Size                1.00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/mydisc/hddata
  LV Name                hddata
  VG Name                mydisc
  LV UUID                nL29my-hgY5-8uDr-BlYq-uxYI-yUye-1ZQnxA
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:58:07 +0100
  LV Status              available
  # open                 0
  LV Size                500.00 MiB
  Current LE             125
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:1


3.	Muntar a /mnt/hdsystem i a /mnt/hddata els corresponents Volums Lògics. Copiar-hi a hdsystem tot el contingut de /usr/share/man i a hddata de /usr/share/doc. Mostrar amb df -h l'ocupació.
# damos formato a las lv
[root@i21 ~]# mkfs -t ext4 /dev/mydisc/hdsystem
mke2fs 1.43.5 (04-Aug-2017)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 16687c70-a2f0-477b-b7cd-7cb1c4a172da
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

[root@i21 ~]# mkfs -t ext4 /dev/mydisc/hddata
mke2fs 1.43.5 (04-Aug-2017)
Creating filesystem with 512000 1k blocks and 128016 inodes
Filesystem UUID: 002310bc-b92e-491c-bcb0-52f104a69323
Superblock backups stored on blocks:
	8193, 24577, 40961, 57345, 73729, 204801, 221185, 401409

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

# creamos los directorios hdsystem y hddata a mnt
[root@i21 ~]# mkdir /mnt/hddata
[root@i21 ~]# mkdir /mnt/hdsystem

#montamos los lv
[root@i21 ~]# mount /dev/mydisc/hdsystem /mnt/hdsystem
[root@i21 ~]# mount /dev/mydisc/hddata /mnt/hddata



# copiamos los ficheros indicados en su directorio correspondiente y vemos que se haya copiado:
[root@i21 ~]# cp -r /usr/share/man /mnt/hdsystem/.
[root@i21 ~]# cp -r /usr/share/doc /mnt/hddata/.
[root@i21 ~]# ll /mnt/hddata/
total 48
drwxr-xr-x. 1113 root root 34816 Feb 13 12:07 doc
drwx------.    2 root root 12288 Feb 13 12:04 lost+found
[root@i21 ~]# ll /mnt/hdsystem/
total 20
drwx------.  2 root root 16384 Feb 13 12:04 lost+found
drwxr-xr-x. 47 root root  4096 Feb 13 12:06 man

# vemos la ocupacion
[root@i21 ~]# df -h -t ext4 /dev/mydisc/*
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydisc-hddata    477M  109M  339M  25% /mnt/hddata
/dev/mapper/mydisc-hdsystem  976M   47M  863M   6% /mnt/hdsystem


4.	Fer que aquests canvis siguin permanents en reiniciar el sistema. Mostra que es fa un reinici i els sistemes de fitxers estan muntats i quina és la seva ocupació.
# copiamos las siguientes lineas en el fstab
[root@i21 ~]# vim /etc/fstab
/dev/mydisc/hddata      /mnt/hddata     ext4    defaults        0       0
/dev/mydisc/hdsystem    /mnt/hdsystem     ext4    defaults        0       0

#reboot
[root@i21 ~]# reboot

# ocupacion
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydisc-hddata    477M  109M  339M  25% /mnt/hddata
/dev/mapper/mydisc-hdsystem  976M   47M  863M   6% /mnt/hdsystem

5.	Aprofitant que encara hi ha espai disponible assignar un 50% de l'espai lliure al Volum Lògic hdsystem. Mostrar clarament aquests canvi en el Volum Lògic.
# extendemos la lv hdsystem
[root@i21 ~]# lvextend -l +50%FREE /dev/mydisc/hdsystem
  Size of logical volume mydisc/hdsystem changed from 1.00 GiB (256 extents) to 5.25 GiB (1344 extents).
  Logical volume mydisc/hdsystem successfully resized.
[root@i21 ~]# df -h -t ext4 /dev/mydisc/*
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydisc-hddata    477M  109M  339M  25% /mnt/hddata
/dev/mapper/mydisc-hdsystem  976M   47M  863M   6% /mnt/hdsystem

# como vemos que el file sysrem sigue igual , hacemos un resize2fs para que ocupe todo el espacio
[root@i21 ~]# resize2fs /dev/mydisc/hdsystem
resize2fs 1.43.5 (04-Aug-2017)
Filesystem at /dev/mydisc/hdsystem is mounted on /mnt/system; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/mydisc/hdsystem is now 1376256 (4k) blocks long.

[root@i21 ~]# df -h -t ext4 /dev/mydisc/*
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydisc-hddata    477M  109M  339M  25% /mnt/hddata
/dev/mapper/mydisc-hdsystem  5.2G   48M  4.9G   1% /mnt/hdsystem

# vemos que ha cambiado la lv
[root@i21 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/mydisc/hdsystem
  LV Name                hdsystem
  VG Name                mydisc
  LV UUID                2jSCBs-01XX-P5pn-4lp2-OsKc-H5Jc-FsJD9h
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:57:49 +0100
  LV Status              available
  # open                 1
  LV Size                1.00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/mydisc/hddata
  LV Name                hddata
  VG Name                mydisc
  LV UUID                nL29my-hgY5-8uDr-BlYq-uxYI-yUye-1ZQnxA
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:58:07 +0100
  LV Status              available
  # open                 1
  LV Size                500.00 MiB
  Current LE             125
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:1

[root@i21 ~]# lvdisplay /dev/mydisc/hdsystem
  --- Logical volume ---
  LV Path                /dev/mydisc/hdsystem
  LV Name                hdsystem
  VG Name                mydisc
  LV UUID                2jSCBs-01XX-P5pn-4lp2-OsKc-H5Jc-FsJD9h
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:57:49 +0100
  LV Status              available
  # open                 1
  LV Size                5.25 GiB
  Current LE             1344
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:0

[root@i21 ~]# lvdisplay /dev/mydisc/hddata
  --- Logical volume ---
  LV Path                /dev/mydisc/hddata
  LV Name                hddata
  VG Name                mydisc
  LV UUID                nL29my-hgY5-8uDr-BlYq-uxYI-yUye-1ZQnxA
  LV Write Access        read/write
  LV Creation host, time i21, 2020-02-13 11:58:07 +0100
  LV Status              available
  # open                 1
  LV Size                500.00 MiB
  Current LE             125
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     512
  Block device           253:1
6.	Usant l’ordre df -h mostrar que el sisteme de fitxers muntat a /mnt/hdsystem s'ha ampliat fins al màxim del seu espai disponible (i si no ho ha fet, fer-ho!).
# como hemos hecho antes un resize2fs, tenemos el maximo del espacio asignado.
[root@i21 ~]# df -h -t ext4 /dev/mydisc/*
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydisc-hddata    477M  109M  339M  25% /mnt/hddata
/dev/mapper/mydisc-hdsystem  5.2G   48M  4.9G   1% /mnt/hdsystem


### EXTRA  

Partint 	del RAID i el LVM creats en els apartats anteriors fer:
1.	Escriu les ordres necessàries per eliminar tota l’automatització de l’arrancada.
# borramos las lineas del fstab
[root@i21 ~]# vim /etc/fstab

# borramos el file de conf de raid para que no detecte nada
[root@i21 ~]# rm -rf /etc/mdadm.conf

# desmontamos los directorios
[root@i21 ~]# umount /mnt/raid
[root@i21 ~]# umount /mnt/hdsystem
[root@i21 ~]# umount /mnt/hddata


2.	Escriu les ordres necessàries per eliminar els LVM.
# borramos las lv
[root@i21 ~]# lvremove /dev/mydisc/hdsystem
Do you really want to remove active logical volume mydisc/hdsystem? [y/n]: y
  Logical volume "hdsystem" successfully removed
[root@i21 ~]# lvremove /dev/mydisc/hddata
Do you really want to remove active logical volume mydisc/hddata? [y/n]: y
  Logical volume "hddata" successfully removed

# borramos las vg
[root@i21 ~]# vgremove /dev/mydisc
  Volume group "mydisc" successfully removed

#booramos las pv
[root@i21 ~]# pvremove /dev/md/raid
  Labels on physical volume "/dev/md/raid" successfully wiped.


3.	Escriu les ordres necessàries per eliminar el raid.
# paramos el raid
[root@i21 ~]# mdadm --stop /dev/md/raid
mdadm: stopped /dev/md/raid

4.	Verifica que les particions sda2, sda3 i sda4 no tenen cap marca especial.
# hacemos un zero-superblock para eliminar todo tipo de marca
[root@i21 ~]# mdadm --zero-superblock /dev/sda2
[root@i21 ~]# mdadm --zero-superblock /dev/sda3
[root@i21 ~]# mdadm --zero-superblock /dev/sda4

# verificamos
[root@i21 ~]# mdadm --examine /dev/sda2
mdadm: No md superblock detected on /dev/sda2.
[root@i21 ~]# mdadm --examine /dev/sda3
mdadm: No md superblock detected on /dev/sda3.
[root@i21 ~]# mdadm --examine /dev/sda4
mdadm: No md superblock detected on /dev/sda4.


