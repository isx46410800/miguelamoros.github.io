# SERVIDORES  

## CONEXIONES SSH A SERVIDORES

+ [SSH](https://www.profesionalreview.com/2018/11/30/ssh-windows-10/)  

+ [SSH CLOUD](https://ayuda-cloud-servers.donweb.com/preguntas-frecuentes/como-conectarme-por-acceso-remoto-via-ssh-desde-mi-pc-windows)  

## HARDWARE SERVIDORES  

### NAS SERVER  

+ NAS SERVER: Un servidor NAS es un dispositivo de almacenamiento conectado a la red. Su función es la de hacer copias de seguridad de los archivos que tú le indiques en la configuración, tanto los de tu ordenador personal como los de cualquier otro dispositivo móvil, aunque también tiene muchas otras funcionalidades. Lo único que necesitarás es utilizar las diferentes aplicaciones que tiene cada fabricante.  

+ Un NAS es un ordenador con su propio sistema operativo y que está adaptado para estar todo el día funcionando. En ellos puedes distinguir dos conjuntos de componentes, estando por una parte lo que es el NAS en sí con su RAM, su procesador y toda su circuitería, y por otra parte los discos duros que puedes añadir a sus ranuras. Dependiendo del modelo o el fabricante, estos discos duros pueden venir incluidos cuando los compres o tendrás que comprarlos aparte.  

+ A las ranuras que tiene el NAS para los discos duros se les llama bahías, y también es importante decidir cuántas quieres tener.  

+ Por lo general, cada fabricante cuenta con su propio sistema operativo para sus NAS, por lo que la interfaz y tu experiencia a la hora de moverte por sus menús y configuraciones dependerá en gran medida de la marca por la que apuestes. 

+ De nada sirve tener el NAS más potente del mercado si los discos que tiene en su interior no rinden a la medida. Algunos NAS vienen directamente con discos duros en el interior, pero en la mayoría de ocasiones deberás ser tú quien los compre por separado. Es básico fijarse no tanto en la capacidad como en la velocidad de lectura y escritura. En el mercado actual quien mejores discos duros ofrece para dispositivos NAS es Western Digital con su gama Red. Son discos duros preparados para rendir correctamente en dispositivos NAS y seguramente la mejor elección. Pero aún así, es recomendable informarte sobre los diferentes modelos de cada fabricante.  

+ [CONFIGURAR/INSTALAR NAS](https://www.xataka.com/basics/configurar-nas-synology-paso-a-paso-configuracion-inicial)  
+ Hay una ip publica y otra interna. Desde el exterior tenemos que hacer un [redireccionamiento de puertos](https://www.youtube.com/watch?v=PxfgkNzc0PA&ab_channel=Qloudea) para poder conectarnos. Sabiendo la ip podemos conectarnos por el navegador, por putty por ssh y establecemos la [conexion](https://www.ionos.es/digitalguide/servidores/configuracion/como-conectar-un-nas-a-internet/)  



## VPN SERVIDORES  

+ Una VPN (o Virtual Private Network, «Red Privada Virtual») es una forma de conectarse a una red local a través de Internet. Por ejemplo, suponga que quiere conectarse a la red local de su trabajo mientras está en un viaje de negocios. Tendrá que buscar una conexión a Internet en alguna parte (como en un hotel) y luego conectarse a la VPN de su trabajo. Sería como si se estuviera conectando directamente a la red de su trabajo, pero la conexión de red real sería a través de la conexión a Internet del hotel. Las conexiones VPN normalmente van cifradas para evitar que la gente pueda acceder a la red local a la que está conectándose sin autenticarse.

+ Hay diferentes tipos de VPN. Puede que tenga que instalar algún software adicional dependiendo de a qué tipo de VPN se conecta. Busque los detalles de conexión de quien está a cargo de la VPN y consulte qué cliente VPN tiene que usar. Luego, vaya al instalador de software y busque el paquete NetworkManager que funciona con su VPN (si lo hay) e instálelo.  

+ Para configurar una conexión VPN:  
```
Abra la vista de Actividades y empiece a escribir Red.
Pulse en Red para abrir el panel.
En la parte inferior de la lista, a la izquierda, pulse el botón + para añadir una conexión nueva.
Elija VPN en la lista de interfaces.
Seleccione qué tipo de conexión VPN tiene.
Rellene los detalles de la conexión VPN y pulse Añadir cuando haya terminado.
Cuando termine de configurar la VPN, abra el menú del sistema en la parte derecha de la barra superior, pulse en VPN apagada y seleccione Conectar. Es posible que deba introducir una contraseña antes de establecer la conexión. Cuando la conexión se establezca, verá un icono de un candado en la barra superior.
Si todo va bien, se conectará correctamente a la VPN. De lo contrario, puede que necesite volver a comprobar la configuración VPN que introdujo. Puede hacerlo pulsando desde el panel de Red que usó para crear la conexión, seleccionando la conexión VPN en la lista y pulsando el botón configuración para revisar la configuración.
Para desconectarse de la VPN, pulse en el menú del sistema en la barra superior y pulse Apagar bajo el nombre de su conexión VPN.
```  

+ [CONEXION VPN WINDOWS](https://support.microsoft.com/es-es/windows/conectar-a-una-vpn-en-windows-3d29aeb1-f497-f6b7-7633-115722c1009c)  
+ [CONFIGURACION VPN WINDOWS](https://www.profesionalreview.com/2018/11/23/crear-vpn-windows-10/)  
+ [CONEXION VPN](https://www.youtube.com/watch?v=5wyR4iBZyCA&ab_channel=ACADENAS)  

+ [CONEXION VPN LINUX](https://www.hostinger.es/tutoriales/como-configurar-vpn-linux-con-openvpn)  
+ [CONFIGURACION VPN LINUX](https://vidatecno.net/como-conectarse-a-una-vpn-automaticamente-en-linux/)  

+ [CREAR VPN DOMESTICA](https://www.xataka.com/basics/como-crear-paso-a-paso-tu-propio-vpn-gratis-windows-necesidad-ningun-programa)  

+ [SOFTWARES DE VPN](https://www.xataka.com/basics/vpn-gratis-mejores-que-conectarte-ocultando-tu-ip-otro-pais)  

+ [COMPARTIR ARCHIVOS EN CASA SERVER DLNA](https://www.xataka.com/basics/que-servidor-dlna-como-configurar-uno-tu-pc-para-ver-tus-archivos-otros-dispositivos)  


## MANTENIMIENTO SERVIDORES

## CLONACION IMAGENES  

+ El software de creación de imágenes de espejo crea un único archivo comprimido que se puede utilizar con fines de copia de seguridad y recuperación ante desastres. Si algo le sucede a su sistema, use el software para restaurar su sistema usando ese archivo de imagen. Puede programar actualizaciones periódicas para ese archivo para asegurarse de capturar nuevos datos o cambios de archivo, y puede almacenar varios archivos de copia de seguridad de imágenes en una sola unidad.

+ Clonar un disco simplemente copia el contenido de un disco duro a otro disco duro. Como resultado, tanto el disco de origen como el de destino tienen exactamente los mismos datos. Esta función le permite transferir toda la información (incluido el sistema operativo y los programas instalados) de un disco duro pequeño a uno grande sin tener que reinstalar y reconfigurar todo su software. Pero solo puede crear un clon en un disco, y no capturará cambios en sus datos, a menos que haga un nuevo clon.

+ [SOFTWARES CLONACION IMAGENES](https://es.phhsnews.com/5-free-disk-imaging-cloning-utilities)  

+ [SYSPREP PARA CLONAR](https://www.iperiusbackup.net/es/sysprep-clonacion-y-despliegue-de-instalaciones-de-windows/). Para [cambiar el ssid](https://www.sysadmit.com/2014/11/windows-sysprep-sid.html).
```
Ejecutas sysprep en modo OOBE y seleccionas la opción generalizar, y también que se apague cuando termine.
Luego de que termine y se apague, creas la imagen y la despliegas a todos los equipos que necesites, con esto windows iniciará en modo bienvenida y generará un SID nuevo.
```

+ [Ejemplo clonacion sysprep](https://eltallerdelbit.com/sysprep-windows/)  

+ [CLONAR](https://www.youtube.com/watch?v=z0tOAOXpMPE&ab_channel=AdministradoresIT) Y [CAMBIAR SSID](https://www.youtube.com/watch?v=ujDbpij2x40&ab_channel=pantallazos.es)

+ [DESCARGA CLONEZILLA](https://drbl.org/download/download-sf.php?branch=stable)  
+ [TUTORIAL CLONEZILLA](https://blog.redigit.es/clonezilla-clonar-varios-ordenadores-multicast-tutorial-parte-3/)  

+ [Ejemplo clonacion con clonezilla](https://docplayer.es/94823707-Practica-clonacion-en-red-con-clonezilla-server.html) y otro [ejemplo 2](https://weblinus.com/clonacion-de-equipos-por-multicasting-drbl-y-clonezilla/)  

+ PRACTICA CLONEZILLA: [VIDEO](https://www.youtube.com/watch?v=huVhDH7nE1M&ab_channel=DIEGOLITTLELION): [1](https://peredafp.files.wordpress.com/2010/10/sesion1czserver.pdf), [2](https://peredafp.files.wordpress.com/2010/10/sesion1czlocal.pdf) Y [3](https://peredafp.files.wordpress.com/2010/10/sesion2czlocal.pdf) Y EN [WINDOWS](https://www.youtube.com/watch?v=ZbHJUVuv10Y&ab_channel=Inform%C3%A1ticoForever)  

+ [CLONACION DE DISCOS](https://www.drive-image.com/es/clonacion-de-disco.html)  

+ [PRACTICA DE CLONADO AULA](https://riunet.upv.es/bitstream/handle/10251/127678/Olmeda%20-%20Despliegue%20de%20im%C3%A1genes%20del%20sistema%20operativo%20en%20aulas%20departamentales%20con%20OpenGnsys%20y%20posterior%20automatizaci%C3%B3n%20de%20procesos%20mediante%20Powershell.pdf?sequence=1)


## WINDOWS SERVER

## MONITORIZAR SERVIDORES  

+ [curso nagios](https://www.udemy.com/course/monitoreo-con-nagios/?referralCode=1484E2700013380FDEE9)  

+ [NAGIOS](https://www.youtube.com/playlist?list=PLf0g2cV4iCkE9vRbsSoe5f428zMNlasKd)  

+ INSTALACION:  
    - cambiar a disabled o permissive el selinux
    - abrir puertos `firewall-cmd --permanent --add-port=80-443/tcp`  
    - firewall-cmd --reload  
    - instalar paquetes como gcc glibc glibc-common openssl openssl-devel perl make
    - useradd nagios // usermod -aG nagios apache  
    - wget https://github.com/NagiosEnterprises/nagioscore/releases/download/nagios-4.4.6/nagios-4.4.6.tar.gz en root home.  
    - tar -xvf nagios.tar.gz // cd nagios
    - ./configure // make all // make install // make install-init // make install-commandmode // make install-config // make install-webconf  
    - systemctl enable nagios 
    - htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin y ponemos passwd  
    - systemctl start nagios  


+ DEPENDENCIAS Y PLUGINS:  
    - En root home wget https://github.com/nagios-plugins/nagios-plugins/releases/download/release-2.4.0/nagios-plugins-2.4.0.tar.gz  
    - tar -xvf plugins_nagios.tar.gz // cd plugins  
    - ./configure // make install
    - systemctl restart nagios  
    - Se instalan en /usr/local/nagios/libexec  
    - plugin help te dice como se usa  


+ NRPE(Nagios Remote Plugin Executor) es un módulo que permite realizar una monitorización activa empleando un agente instalado en los recursos.:
    - wget https://github.com/NagiosEnterprises/nrpe/releases/tag/nrpe-4.0.3 en root home  
    - tar -xvf nrpe.tar.gz // cd nrpe  
    - ./configure // nos da el puerto 5666 que tiene que ser abierto en los clientes  
    - make check_nrpe // make install-plugin
    - creamos un comando para que nagios use nrpe ->/usr/local/nagios/etc/objects/
    - vim commands.cfg // abajo del todo:  
    ```
    define command {
        command_name check_nrpe
        command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$
    }
    ```
    > user1 = /usr/local/nagios/libexec  
    - cd .. / cat resource.cfg nos especifica los parametros de varibles  

+ PREPARAR HOST LINUX:  
    - EN EL CLIENTE
    - cambiar a disabled o permissive el selinux
    - abrir puertos `firewall-cmd --permanent --add-port=80-443/tcp`  
    - firewall-cmd --reload  
    - instalar paquetes como gcc glibc glibc-common openssl openssl-devel perl make
    - useradd nagios
    - En root user home wget https://github.com/nagios-plugins/nagios-plugins/releases/download/release-2.4.0/nagios-plugins-2.4.0.tar.gz  
    - tar -xvf plugins_nagios.tar.gz // cd plugins  
    - ./configure // make install
    - wget https://github.com/NagiosEnterprises/nrpe/releases/tag/nrpe-4.0.3 en root home  
    - tar -xvf nrpe.tar.gz // cd nrpe  
    - ./configure // nos da el puerto 5666 que tiene que ser abierto en los clientes  
    - make all // make install // make install-config // make install-init  
    - vim /usr/local/nagios/etc/nrpe.cfg -> allowed_host:,ip del server  
    - systemctl enabled nrpe // systemctl start nrpe  
    - ahora estará listo para poder ser monitorizado  
    - en el server /usr/local/nagios/libexec ./check_nrpe -H ipcliente y si devuelve la version es que sí hay comunicacion.  

+ AGREGAR HOST LINUX:  
    - En el server /usr/local/nagios/etc/objects/ añadimos un fichero parecido a las plantillas(template.cfg) donde vamos a poner los linux que queremos agregar a nagios `linux.cfg`  
    ```
    define host{
        use     linux-server #en las plantillas veremos lo que viene de este nombre
        host_name cliente_linux
        alias cliente_linux
        check_interval 1 # lo podemos poner en nagios grafico y ahorramos esta linea
        address 10.20.30.201
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Hard disk
        check_command check_nrpe!check_sda1
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Uptime
        check_command check_nrpe!check_uptime
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Current load
        check_command check_nrpe!check_load
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Swap
        check_command check_nrpe!check_swap
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Ping
        check_command check_ping!500.0,20%!800.0,60% #warning-critical
    }
    define service{
        use  generic-service
        host_name cliente_linux
        service_description Usuarios activos
        check_command check_nrpe!check_users
    }
    ```  
    - En /urs/local/nagios/etc/nagios.cfg editamos y añadimos este fichero para que lo cargue en el servidor nagios `cfg_file=/usr/local/nagios/etc/objects/linux.cfg`
    - Para verificar este fichero `/usr/local/nagios/bin/nagios -v /urs/local/nagios/etc/nagios.cfg`  
    - .bash_rc creamos dos alias: alias nagioscheck=`/usr/local/nagios/bin/nagios -v /urs/local/nagios/etc/nagios.cfg` y alias nagiosrestart=`systemctl restart nagios`
    - si hay plugins que no detecta nagios, en el cliente editamos el fichero /usr/local/nagios/etc/nrpe.cfg y añadimos la linea de command[check_swap]=/usr/local/nagios/libexec/check_swap -w 20% -c 10% y despues systemctl restart nrpe
    - Podemos hacer consultas por consola en el server como por ejemplo en libexec `./check_nrpe -H ip cliente -c check_swap`  

+ AGREGAR HOST WINDOWS:  
    - En windows cliente se instala el `nsclient` para poder comunicar nagios con un windows y poder monitorizar. Descargamos en [nsclient](https://nsclient.org/download/)  
    - Instalamos - complete - direccion ip del server - sin pass, insecure y todo enabled
    - vamos a firewall de windows y que esté permitido el nsclient
    - vamos a services.msc y buscamos nsclient monitoring y esté ejcutandose y luego en inicar sesion permitimos el escritorio  
    - en el server nagios en el template de windows.cfg podemos ver como comunicar con windows y los distintos plugins. Luego lo agregamos a nagios.cfg  
    - creamos una nueva norma en firewall de entrada de ping por si falla la conexion.  
    - editamos el fichero de archivos de programa -nsclient si da errores.
    




## INVENTARIO  

+ El inventario de hardware usará el sistema clásico de inventarios, los diferentes campos de la tabla de Excel incluirá:  
```
Id del producto. Cada producto estará marcado o señalizado para poder identificarlo. También sirve para que los empleados conozcan la procedencia del aparato.
Tipo de producto: Ordenador de sobre mesa, portátil, teclado, ratón, tableta gráfica, móvil de empresa, pantalla o videowall, impresora ....
Marca: Marca del producto
Garantía: Fecha de validez de la garantía
Características: Descripción de cómo es el producto
Estado: en servicio, reparada, fuera de servicio
Ubicación: Departamento RRHH, Producción, Dirección...
Cantidad de productos. Número de productos que se poseen con esas características
```  

+ Algunos programas de inventario:  
```
1. PartKeepr
2. Inventory Manager de Suru Tech Perfect Inventory Manager
3. Magento Inventory Project (MSI)
II. Sistemas de gestión de inventarios unidos a un ERP
4. OpenBoxes Plan Self-Hosted
5. ERPNext Self Hosted
6. Odoo ERP Community Edition
```  

+ [MAGENTO INVENTORY](https://github.com/magento/inventory) y su [CONFIGURACION](https://www.hiberus.com/crecemos-contigo/como-controlar-gestionar-stock-magento/)  

+ [INVENTARIOS](https://negokai.com/los-mejores-software-de-gestion-de-inventarios.html?utm_source=justexw.com&utm_medium=content&utm_campaign=seo_sp&utm_term=plantilla_498)  

+ [SOFTWARES DE INVENTARIO DE RED](https://www.network-inventory-advisor.com/es/best-hardware-inventory-software.html)  

+ [SOFTWARE GRATIS INVENTARIO RED](https://es.ccm.net/aplicaciones-e-internet/profesional/1388-los-mejores-programas-gratuitos-de-inventario-de-equipos-informaticos/)  

+ [ASSET TOOL INVENTARIO](https://www.it-asset-tool.com/es/descarga-rapida/it-asset-tool)  