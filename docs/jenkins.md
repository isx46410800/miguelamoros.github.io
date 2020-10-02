# Jenkins  

## Instalación  
### FEDORA  
+ Actualizar repositorios:
`sudo dnf update -y`  

+ Instalar Java:  
`sudo dnf install -y java`  

+ Agregar repositorios de Jenkins:  
`sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat/jenkins.repo`  
`sudo rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key`  
`sudo rpm --import http://pkg.jenkins.io/redhat-stable/jenkins.io.key`  

+ Instalar Jenkins:  
`sudo dnf install -y jenkins`  

+ Encender el servicio Jenkins:  
`sudo systemctl start jenkins`  
`sudo systemctl status jenkins`  

### UBUNTU/DEBIAN
+ Actualizar repositorios:  
`sudo apt update`  

+ Instalar Java:  
`sudo apt install openjdk-8-jdk`  

+ Agregar repositorios de Jenkins:  
`wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add - sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'`

+ Instalar Jenkins:  
`sudo apt update sudo apt install jenkins`  

+ Encender el servicio Jenkins:  
`sudo systemctl start jenkins sudo systemctl start jenkins`  

### DOCKER
+ En un fichero docker-compose.yml:  
`docker-compose up -d`  
```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "9090:8080" #cambiamos el 9090 de local nuestro 
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home
    networks:
      - net
networks:
  net:

```

#### NOTAS A TENER EN CUENTA  
+ Al instalar se crea el usuario y grupo Jenkins
+ Jenkins trabaja en el directorio /var/lib/jenkins
+ Los archivos de log están en /var/log/jenkins/jenkins.log
+ Los parámetros de configuración se encuentran en /etc/sysconfig/jenkins
+ Por defecto, el puerto donde trabaja Jenkins es el 8080
+ La contraseña de administrar Jenkins se encuentra en /var/jenkins_home/secrets/ini...

## PROYECTO CON PARAMETROS  
Aquí podemos definir en la opción de `this project is parameterized->string parameter` se puede definir variable con valor para utilizarlas en la construcción del job con un build de `execute shell`.  

![](./images/test02.png)  

También con `choice parameter` podemos hacer una variable con diferentes opciones a elegir:  

![](./images/test02b.png)  

También con `boolean parameter` podemos hacer una variable con true/false a elegir:  

![](./images/test02c.png)  

![](./images/test02d.png)  

![](./images/test02e.png)  

![](./images/test02f.png)  

> Le pasamos los argumentos por las variables definidas en los parámetros y en el script.

## SSH  

### Creacion SSH container
Vamos a crear un container con ssh server para poder conectarnos alli y hacer cosas con Jenkins.  

Creamos un `Dockerfile` con Centos e instalamos el ssh, creamos su directorio ssh y creamos unas llaves con `ssh-keygen -f nombre-key` para pasarle la publica al ssh y asi conectarnos directamente sin password.  

![](./images/sshcentos.png)  

Modificamos el `docker-compose.yml` añadiendo el servicio de `ssh` para ello creamos un nuevo servicio con una `image: build: context: ssh` y luego haremos un `docker-compose build` y nos generará una imagen a través del dockerfile de dentro de donde pongamos la ubicacion en `context`:  

```
# Instalamos un container con SO centos
FROM centos:7
# Instalamos el ssh server para poder conectarnos por ssh alli
RUN yum -y install openssh-server
# Creamos un usuario con pass por stdin y creamos su dir ssh y con permisos
RUN useradd remote_user && \
    echo "1234" | passwd remote_user --stdin && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
# Copiamos nuestra clave publica ssh y la copiamos en el authorized(se crea) para conectarnos sin passwd
COPY remotessh-key.pub /home/remote_user/.ssh/authorized_keys
# Cambiamos propetario y grupo a todo lo que haya abajo del home remoteuser y damos permisos
RUN chown remote_user:remote_user   -R /home/remote_user && \
    chmod 600 /home/remote_user/.ssh/authorized_keys
# Para que no de errores por primera vez en un container
RUN /usr/sbin/sshd-keygen > /dev/null 2>&1
# activamos servicio ssh detached
CMD /usr/sbin/sshd -D
```

```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "9090:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home
    networks:
      - net
  remote_host:
    container_name: remote_host
    image: remote_host
    build:
      context: ssh
    networks:
      - net
networks:
  net:
```

Hacemos el Docker-compose
```
[isx46410800@miguel jenkins]$ docker-compose up -d
jenkins is up-to-date
Creating remote_host ... 
Creating remote_host ... done
[isx46410800@miguel jenkins]$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
172b5c2a9f99        remote_host         "/bin/sh -c '/usr/sb…"   6 seconds ago       Up 3 seconds                                            remote_host
4f52a64e7618        jenkins/jenkins     "/sbin/tini -- /usr/…"   5 hours ago         Up 3 hours          50000/tcp, 0.0.0.0:9090->8080/tcp   jenkins
```

### Credenciales  
Para comprobar que el SSH y JENKINS se ven y comunican, hacemos primero un ping desde Jenkins con `ping remote_host` y despues nos conectamos por ssh con el usuario creado alli `ssh remote_user@remote_host` o copiando la llave publica a Jenkins y desde ahi `ssh -i remotessh-key remote_user@remote_host`

+ Configurar en `Credentials -System - Global - credentials` para poner las credenciales de SSH con el usuario creado en dockerfile y la llave privada creada.  

+ Luego vamos a `Manage - system configuration - ssh remote host` y ponemos el nombre del servicio ssh del docker, puerto y las credenciales creadas antes. Damos a check conection y comprobamos que sale successfully.  

![](./images/sshcentos2.png)  

### Ejercicio mandar un job a maquina remota
En este ejercicio lo que hacemos es que desde jenkins, mandamos un job creando un `build - execute shell via ssh remote` con las credenciales creadas de ssh a una máquina remota. El resultado lo veremos dentro de esta maquina remota.  

![](./images/sshcentos3.png)   

![](./images/sshcentos4.png)  

![](./images/sshcentos5.png)  

![](./images/sshcentos6.png)  


## BASE DE DATOS JENKINS  
Modificamos el docker-compose y creamos un servicio que será una bbdd de mysql creando un volumen para que la xixa se guarde ahí al salir. Indicamos un nuevo campo de `environment` para poner el campo de la pass de root `MYSQL_ROOT_PASSWORD=1234`.  

```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "9090:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home
    networks:
      - net
  remote_host:
    container_name: remote_host
    image: remote_host
    build:
      context: ssh
    networks:
      - net
  db_host:
    container_name: db
    image: mysql:5.7
    environment:
      - "MYSQL_ROOT_PASSWORD=1234"
    volumes:
      - $PWD/db_data:/var/lib/mysql
    networks:
      - net
networks:
  net:
```

Nos conectamos al container nuevo y para entrar a la bbdd se pone el comando `mysql -u root -p`.  

![](./images/mysql.png)  

Añadimos lo siguiente en el Dockerfile para poder utilizar mysql por ssh y aws:  
```
# Instalamos mysql para poder conectarnos a la bbdd con mysql como comando
RUN yum -y install mysql
# Instalamos aws cli para amazon que está en un paquete de epel-pip
RUN yum -y install epel-release && yum -y install python-pip && pip install --upgrade pip && yum -y install awscli
```  
Una vez cambiado hacemos un `docker-compose build` para que vuelva construir todo con los cambios nuevos y despues enchegar de nuevo con las nuevas construcciones `docker-compose up -d`.  

Hacemos un ping desde ssh container a db container para comprobar conexion:  
```
[root@e1825be6ec48 /]# ping db_host
PING db_host (172.21.0.4) 56(84) bytes of data.
64 bytes from db.jenkins_net (172.21.0.4): icmp_seq=1 ttl=64 time=0.162 ms
64 bytes from db.jenkins_net (172.21.0.4): icmp_seq=2 ttl=64 time=0.083 ms
```  

Después de esto nos conectamos a la bbdd del container desde ssh con opcion -h de host:  
`[root@e1825be6ec48 /]# mysql -u root -h db_host -p`  

### Creacion bbdd simple  
MySQL [(none)]> show databases  
MySQL [(none)]> create database testdb;  
MySQL [(none)]> use testdb;  
MySQL [testdb]> create table info (name varchar(20), surname varchar(20), age int(2));  
MySQL [testdb]> show tables;  
MySQL [testdb]> desc info;  
MySQL [testdb]> insert into info values('Miguel', 'Amoros', 27);  

![](./images/mysql2.png)  

### Creación Buckets en amazon  
Amazon Simple Storage Service (Amazon S3) es almacenamiento para Internet. Puede usar Amazon S3 para almacenar y recuperar cualquier cantidad de datos en cualquier momento y desde cualquier parte de la Web. Puede realizar estas tareas usando la Consola de administración de AWS, que es una sencilla e intuitiva interfaz web.  

Amazon S3 almacena datos a modo de objetos dentro de buckets. Un objeto es un archivo y cualquier metadato opcional que describe el archivo. Para almacenar un archivo en Amazon S3, lo carga a un bucket. Al cargar un archivo como objeto, puede configurar permisos en el objeto y en cualquier metadato.  

Los buckets son contenedores de objetos. Puede tener uno o más buckets. Puede controlar el acceso de cada bucket, decidiendo quién puede crear, eliminar y enumerar objetos en él. También puede elegir la región geográfica donde Amazon S3 almacenará el bucket y su contenido y ver los registros de acceso para el bucket y sus objetos.  

`AWS - BUCKETS - CREATE BUCKET`  # jenkins-udemy-miguel  
![](./images/mysql3.png)  

Creamos un usuario de autenticación para subir cosas al bucket:  
`AWS-IAM-USERS-CREATE USER`  
![](./images/mysql4.png)  

Opcion `attach - full access - crear - download .csv key`  

![](./images/mysql5.png)  
![](./images/mysql6.png)  

### Dump de la bbdd  
`[root@e1825be6ec48 /]# mysqldump -u root -h db_host -p1234 testdb > /tmp/dbdump.sql`  
```
-- MySQL dump 10.14  Distrib 5.5.65-MariaDB, for Linux (x86_64)
--
-- Host: db_host    Database: testdb
-- ------------------------------------------------------
-- Server version	5.7.31
/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
--
-- Table structure for table `info`
--
DROP TABLE IF EXISTS `info`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `info` (
  `name` varchar(20) DEFAULT NULL,
  `surname` varchar(20) DEFAULT NULL,
  `age` int(2) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;
--
-- Dumping data for table `info`
--
LOCK TABLES `info` WRITE;
/*!40000 ALTER TABLE `info` DISABLE KEYS */;
INSERT INTO `info` VALUES ('Miguel','Amoros',27);
/*!40000 ALTER TABLE `info` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;
/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
-- Dump completed on 2020-09-25 18:19:57
```  
+ Configuramos a través del aws cli de amazon para poder subir el DUMP al bucket de s3 de amazon. A través de las credenciales obtenidas en bucket configuramos las variables de entorno.  

+ Las configuramos en el container de bbdd:  
![](./images/mysql7.png)  
> [ayuda](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)  

+ Ahora con las credenciales podremos copiar el dump al bucket de amazon:  
> [ayuda](http://bigdatums.net/2016/09/17/copy-local-files-to-s3-aws-cli/)  

`[root@e1825be6ec48 /]# aws s3 cp /tmp/dbdump.sql s3://jenkins-udemy-miguel`  
> upload: tmp/dbdump.sql to s3://jenkins-udemy-miguel/dbdump.sql  

![](./images/mysql8.png)  

### DUMP AUTOMATIZADO  
+ Creamos un script dentro del container de nuestra bbdd para poder hacer desde jenkins una conexion a la bbdd remota y subir a amazon el dump al bucket de almacenaje.  
```
#!/bin/bash
# definimos unas variables
DB_HOST=$1
DB_PASSWORD=$2
DB_NAME=$3
DATE=%(date +$H-%M-%S)
AWS_SECRET=$4
BUCKET_NAME=$5
# hacemos el dump de a bbdd diciendo el nombre host servicio, pass y name de la bbdd, exportamos las variables aws para subir al bucket
mysqldump -u root -h $DB_HOST -p$DB_PASSWORD $DB_NAME > /tmp/db-$DATE.sql && \
export AWS_ACCESS_KEY_ID=AKIA5RIFOUI3AQMRXFFQ && \
export AWS_SECRET_ACCESS_KEY=$AWS_SECRET && \
aws s3 cp /tmp/db-$DATE.sql s3://$BUCKET_NAME
```  
+ Configuramos ahora las credenciales de la bbdd en jenkins con una variable de db_name y el passwd de nuestra bbdd que era 1234:  

![](./images/mysql9.png)  

+ Configuramos ahora las credenciales del s3 bucket en jenkins poniendo la passwd secret key:  

![](./images/mysql10.png)  

+ Ahora configuramos en Jenkins las variables parametrizadas del script de bbdd:  

![](./images/mysql11.png)  

+ Después en la opción de entorno de ejecución selecionamos la opción de usar secret text y ponemos las credenciales creadas anteriormente y la variable del script creado en la bbdd.  

![](./images/mysql12.png)  

+ Build por ssh:  

![](./images/mysql13.png)  

+ Automatizamos:  

![](./images/mysql14.png)  
![](./images/mysql15.png)  
![](./images/mysql16.png)  

+ Añadimos en el docker-compose estas lineas para que el script creado en tmp de la bbdd no se borre cuando se elimine, por lo tanto la chicha del script de fuera lo mandamos alli copiado:  
```
volumes:
      - $PWD/dumpremotessh-aws.sh:/tmp/dumpremote.sh
```  

+ Ahora si creamos en mysql otra db y en amazon otro bucket, cambiamos los parametros del job y nos crea lo mismo sin cambiar el script.  

+ Podemos tambien hacerlo manualmente y en vez de llamar al script, lo copiamos dentro y hace lo mismo (opción más fea).  

![](./images/mysql17.png)  
![](./images/mysql18.png)  


## ANSIBLE  
Automatización de tareas hecho en python.  

+ Creamos un nuevo dockerfile:  
```
# sistema basado en jenkins
FROM jenkins/jenkins
# instalamos pip como root
USER root
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py" && python get-pip.py
RUN pip install -U ansible
USER jenkins
```

+ Modificamos el docker-compose:  
```
jenkins:
    container_name: jenkins
    image: jenkins-ansible
    build:
      context: jenkins-ansible
    ports:
      - "9090:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home
    networks:
      - net
```

+ Hacemos docker-compose build y up -d

+ Creamos un fichero `hosts` con lenguaje ansible para crear nuestro primer fichero de inventario.  
```
# ARCHIVO DE INVENTARIO ANSIBLE
# todas las variables se definen asi
[all:vars]
# todas las maquinas se conectaran por ssh
ansible_connection = ssh
[test]
# aque maquina me voy a conectar con el nombre test1 y con que usuario y donde esta la llave privada para conectarme
test1 ansible_host=remote_host ansible_user=remote_user ansible_private_key_file=/var/jenkins_home/ansible/remotessh-key
```  
> Despues lo copiamos dentro de `[isx46410800@miguel jenkins]$ cp hosts jenkins_home/ansible/` para que esté dentro del container jenkins-ansible ya que aqui está el volumen de la xixa del container que se guarda.  

+ Comprobamos conexion de nuestro inventario ansible-jenkins con la máquina ssh remote_host:  
`jenkins@7cafd0984215:~/ansible$ ansible -m ping -i hosts test1`  
> -m de modulo -i fichero y maquina  
```
test1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
```  

### Playbooks  
Los Playbooks describen configuraciones, despliegue, y orquestación en Ansible. ​ El formato del Playbook es YAML. ​ Cada Playbook asocia un grupo de hosts a un conjunto de roles. Cada rol está representado por llamadas a lo que Ansible define como Tareas.  

+ Creamos primer fichero playbook:  
```
cat play.yml 
- hosts: test1
  tasks:

    - shell: echo "Hola Mundo desde Ansible y Jenkins" > /tmp/hola-ansible.txt
```

+ Para comprobar el funcionamiento:  
`jenkins@7cafd0984215:~/ansible$ ansible-playbook -i hosts play.yml`  

![](./images/ansible.png)  
> lo que hace es desde jenkins conectar el playbook a la maquina creada en ansible test1(que es remote_host de ssh container) por ssh.  

+ Instalamos el modulo ansible en jenkins y creamos un job con build de ansible playbook. Ponemos la ruta del playbook y la ruta del file hosts para la conexion.   

![](./images/ansible2.png)  
![](./images/ansible3.png)  
> asi nos ahorramos poner toda la ruta de arriba, lo hacemos automatizado.  

+ Modificamos el fichero play.yml para pasar el texto por parametro:  

![](./images/ansible4.png)  

+ Añadimos los parametros y la variable extra para que en el script coja la variavle MSG con el parametro texto de arriba( seria como añadir la opcion -e "MSG=hola" en hardcode):  
```
- hosts: test1
  tasks:
    - debug:
       var: MSG
```
![](./images/ansible5.png)  
![](./images/ansible6.png)  
![](./images/ansible7.png)  
![](./images/ansible8.png)  

#### TAGS
+ Ponemos tags en nuestro script:  
```
- hosts: test1
  tasks:
    - debug:
       var: MSG
    - debug:
       msg: "Yo no me voy a ejecutar :("
     tags: no-exec
    - debug:
       msg: "Yo sí me voy a ejecutar :)"
     tags: si-exec
```  
> solo se ejecutan las tareas que ponen en RUN de tags en jenkins, el resto no:  

![](./images/ansible9.png)  
![](./images/ansible10.png)  


+ `PLUGIN: ANSICOLOR` para que salga en colo en jenkins el resultado del job activando la opción `color` en configuracion del job.  

### DB MYSQL
+ Creamos en el container db una bbdd de people con registros en la tabla registro.  

+ De un file con 50 nombres, hacemos un script para meterlos todos en la bbdd:  
```
#!/bin/bash
#iniciamos contador
count=0
#mientras sea menos de 50 personas del archivo, coger los campos
while [ $count -lt 50 ]
do
        count=$((count+1))
        nombre=$(nl people.txt | grep -w $count | tr -s '[:blank:]' ',' | cut -d',' -f3)
        apellido=$(nl people.txt | grep -w $count | tr -s '[:blank:]' ',' | cut -d',' -f4)
        edat=$(shuf -i 20-25 -n1)

        mysql -u root -p1234 people -e "insert into registro values($id, '$nombre', '$apellido', $edat)"

        echo "$count, $nombre, $apellido importado"
        sleep 5
done
```

![](./images/ansible11.png)  
> copiamos el script en el container db y lo ejecutamos para que se llene la bbdd creada.  

### NGINX SERVER  
+ Creamos un container con nginx server y php a partir del container con ssh:  
```
# a partir de la imagen de ssh generada ya
FROM remote_host
# añadimos el repo del web server nginx para centos
COPY ./conf/nginx.repo /etc/yum.repos.d/nginx.repo
# instalamos los paquetes necesarios y de php
RUN                                                                          \

  yum -y install nginx-1.12.2 openssl --enablerepo=nginx                  && \

  yum -y install https://repo.ius.io/ius-release-el7.rpm                     \

  https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  && \

  yum -y install                                                             \

    php71u-fpm                                                               \

    php71u-cli                                                               \

    php71u-mysqlnd                                                           \

    php71u-soap                                                              \

    php71u-xml                                                               \

    php71u-zip                                                               \

    php71u-json                                                              \

    php71u-mcrypt                                                            \

    php71u-mbstring                                                          \

    php71u-zip                                                               \

    php71u-gd                                                                \

     --enablerepo=ius-archive && yum clean all
# abrimos los puertos por donde escuchar
EXPOSE 80 443
# nos quedamos con los volumenes
VOLUME /var/www/html /var/log/nginx /var/log/php-fpm /var/lib/php-fpm
# comando para dar permisos al usuario creado d ssh
RUN setfacl -R -m u:remote_user:rwx /var/www/html
# copiamos el fichero de configuracion 
COPY ./conf/nginx.conf /etc/nginx/conf.d/default.conf
# copiamos el fichero de empezar
COPY ./bin/start.sh /start.sh
# damos permisos de ejecucucion
RUN chmod +x /start.sh
# arranca el container con el script
CMD /start.sh
```

+ Modificamos el docker-compose para añadir el nuevo container nginx-php con ssh:  
```
web:
    container_name: web
    image: ansible-web
    build:
      context: jenkins-ansible/web
    ports:
      - "80:80"
    networks:
      - net
```  
> creamos un servicio `web` con el nombre container y la imagen `ansible-web` que se crea a través del dockerfile con la ruta en context.  

+ Hacemos un docker-compose build y up.
> NOTA: desactivo con systemctl stop httpd porque escucha por el puerto 80 del web que queremos crear.  

+ Entramos al container web y añadimos el indice de index.php:  
```
[root@7d0d237e1686 /]# cat /var/www/html/index.php
<?php phpinfo(); ?>
```  
> Hacemos esto solo de prueba para nuestro navegador  

![](./images/ansible12.png)  

+ Creamos una tabla que muestra la información via web:  
`[isx46410800@miguel jenkins-ansible]$ docker cp table.j2 web:/var/www/html/index.php`  
![](./images/ansible13.png)  

+ Para integrar el webserver en nuestro inventario de Ansible modificamos el fichero host de `/jenkins_home/ansible/hosts` y añadimos el nuevo alias y el nombre servicio:  

`web1 ansible_host=web ansible_user=remote_user ansible_private_key_file=/var/jenkins_home/ansible/remotessh-key`  

+ Comprobamos yendo al container jenkins que es donde está instalado Ansible y lo comprobamos como la otra vez:  
```
jenkins@7cafd0984215:~/ansible$ pwd
/var/jenkins_home/ansible
**jenkins@7cafd0984215:~/ansible$ ansible -i hosts -m ping web1
web1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
**jenkins@7cafd0984215:~/ansible$ ansible -i hosts -m ping all
test1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
web1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
```

+ Ahora vamos hacer lo anterior pero de manera automatizada en Jenkins. Para ello creamos un playbook nuevo:  
```
- hosts: web1
  tasks:
    - name: Transfiere el template hacia web1
      template:
        src: table.j2
        dest: /var/www/html/index.php
```  
+ Cambiamos unos datos del fichero table.j2 donde contenia los datos a mostrar en el index.php para poder pasar las cosas por parametros en Jenkins: 
``` 
  $sql = "SELECT id, nombre, apellido, edat FROM registro where edat <= 25 and edat >=20";
  ----> CAMBIOS
  $sql = "SELECT id, nombre, apellido, edat FROM registro {% IF EDAD is defined %} where edat = {{ EDAD }} {% endif %}";----
```  
  > queremos decir que si el parametro que pasamos EDAD está defenido haga la consulta donde la edad sea igual al parametro.  

+ Damos permisos para solucionar un fallo de poner escribir dentro del container web en la carpeta de html y despues dentro del container jenkins, probamos siempre lo del playbook:  
`[root@7d0d237e1686 /]# chown remote_user:remote_user -R /var/www/html/`  
`jenkins@7cafd0984215:~/ansible$ ansible-playbook -i hosts people.yml`  
`jenkins@7cafd0984215:~/ansible$ ansible-playbook -i hosts people.yml -e "EDAD=22"`  
> con y sin parametros, y el cambio lo vemos en el index.php del container web.  

+ Vamos a jenkins y automatizamos la tarea, poniendo una variable de opcion, el path del playbook y del fichero de hosts para conectar con la maquina y despues una extra variable que sera la variable que pasamos como parametro.  

![](./images/ansible14.png)  
![](./images/ansible15.png)  
![](./images/ansible16.png)  
![](./images/ansible17.png)  
![](./images/ansible18.png)  
![](./images/ansible19.png)  


## SECURITY JENKINS  

+ Por defecto no está activado, pero si queremos que cualquier persona se pueda loguear al jenkins via navegador vamos a `Manage Jenkins- Conf global Security` y clicamos en la opcion de desactivar seguridad.  

+ Se puede activar la opción `Allow users to sign up` para permitir a usuarios crearse una cuenta para entrar a Jenkins igual que la otra opción de que tengan permisos especiales los usuarios registrados.  

+ Activamos lo de registrarse, nos ddesconectamos y creamos dos cuentas:   
![](./images/security.png)  
![](./images/security2.png)  

+ Instalamos un potente plugin de seguidad que sirve para gestionar los roles y dar permisos a los usuarios:  
`Role-based Authorization Strategy`  

+ Entramos de nuevo a la conf de seguridad con el uuario admin y le damos a este role de usuarios. Veremos que nos aparece una nueva pestaña de menu para que pueda gestionar los roles:  
![](./images/security3.png)  
![](./images/security4.png)  

### MANAGE USERS
+ Vamos a `manage jenkins-manage users` aqui podremos crear/borrar/modificar usuarios sin tener que hacerlos creando cuentas:  
![](./images/security5.png)  
![](./images/security6.png)  
![](./images/security7.png)  
![](./images/security8.png)  

### MANAGE ROLES
+ Vamos a `manage jenkins-manage and assign roles` y manage roles para gestionar los roles de un usuario:

+ Creamos un nuevo role en `role to add` como por ejemplo que solo sea de lectura el role del usuario, solo podrá ver jobs sin ejecutar ni nada mas:  

![](./images/security9.png)  

+ Ahora asignamos este role creado de `solo-lectura` a uno de los uusuarios. Vamos a `manage jenkins-manage and assign roles` y assign role.  
> Veremos al loguearlos despues que solo puede ver, solo lectura.  

![](./images/security10.png)  
![](./images/security11.png)  

+ Si modificamos el manage role y le ponemos que pueda _read_ los jobs, al loguearse veremos que pueda ver los jobs almenos.  

![](./images/security12.png)  

+ Ahora creamos un role de poder ejecutar y ver los jobs y se lo asignamos:  

![](./images/security13.png)  
![](./images/security14.png)  
![](./images/security15.png)  

+ Ahora lo que queremos hacer es que un usuario en vez de ver todos los jobs, solo veas los que le digamos y pueda hacer build solo a esos. Para ello le quitamos el read the jobs y creamos un item role y le añadimos un patron para ver solo jobs con ese patron.  

![](./images/security16.png)  
![](./images/security17.png)  
![](./images/security18.png)  

## TRIPS AND TICKS  

### Variables de entorno  

+ Lista de variables de entorno propias de Jenkins:  
```
echo "BUILD_NUMBER: $BUILD_NUMBER"
echo "BUILD_ID: $BUILD_ID"
echo "BUILD_URL: $BUILD_URL"
echo "JOB_NAME: $JOB_NAME"
echo "JAVA_HOME: $JAVA_HOME"
echo "JENKINS_URL: $JENKINS_URL"
``` 
> [lista variables](https://wiki.jenkins.io/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-belowJenkinsSetEnvironmentVariables)  

Resultado de un simple job:  
```
Console Output
Started by user admin
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/7-ENV
[7-ENV] $ /bin/sh -xe /tmp/jenkins7847738549255029537.sh
+ echo BUILD_NUMBER: 1
BUILD_NUMBER: 1
+ echo BUILD_ID: 1
BUILD_ID: 1
+ echo BUILD_URL: http://localhost:9090/job/7-ENV/1/
BUILD_URL: http://localhost:9090/job/7-ENV/1/
+ echo JOB_NAME: 7-ENV
JOB_NAME: 7-ENV
+ echo JAVA_HOME: /usr/local/openjdk-8
JAVA_HOME: /usr/local/openjdk-8
+ echo JENKINS_URL: http://localhost:9090/
JENKINS_URL: http://localhost:9090/
Finished: SUCCESS
```  

+ Podemos crear propias en `manage jenkins- conf sistem` y clicamos en la opcion de variables de entorno:  

![](./images/trick.png)  

```
echo "PLATAFORMA: $PLATAFORMA"
echo "PAIS: $PAIS"
```

```
+ echo PLATAFORMA: UDEMY
PLATAFORMA: UDEMY
+ echo PAIS: ESPAÑA
PAIS: ESPAÑA
```

### Cambio URL 

+ Podemos crear propias en `manage jenkins- conf sistem` y clicamos en la opcion de Jenkins Location:  

Cambiamos la url por la de dns (/etc/hosts):  
```
192.168.1.44 host2
127.0.0.1 loopback.jenkins
```

`http://loopback.jenkins:9090/`  

### CRON  
+ Podemos ver una chuleta de [crontab](https://crontab.guru/)  

+ A la hora de construir un job hay que dar en la opción de `Build triggers - execute periodically`  

`5 * * * *`   
> cada 5 minutos  

![](./images/trick2)  
> Podemos poner una H en un * y quiere decir que cogerá cuando pueda de ese momento para que haya menos carga de jobs por si hay otras tareas también y no se sobrecargue.  

### GATILLAR JOBS  
+ Quiere decir que lancemos un job sin necesidad sin entrar a jenkins y construir el job, sino desde un script desde la terminal.  Vamos a `Manage and Assign Roles - Manage Roles` y creamos uno que se llame trigger-jobs.  

+ Creamos un usuario `jenkins` y le asignamos este rol.  
![](./images/trick3.png)  
![](./images/trick4.png)  

+ Va relacionado con la opción `Crumb Issuer` de seguridad global,ya viene por defecto.  

+ Instalamos un plugin para evitar error:  
> Buscando en Internet he visto que el error se produce porque a partir de cierta versión de Jenkins (2.176.x) es necesario que ambas peticiones (para obtener el crumb y para lanzar el job) estén dentro de la misma "sesión web" (ver https://jenkins.io/doc/upgrade-guide/2.176/#upgrading-to-jenkins-lts-2-176-3).  
> Siguiendo la recomendación en esa misma página, instalé el plugin "Strict Crumb Issuer" y lo configuré para que no fuera necesario estar en la misma sesión web:  

![](./images/trick5.png)  

```
[isx46410800@miguel jenkins]$ cat crumb.sh 
# generamos el crum, el usuario que queremos, -s de silencioso el output y la url de jenkins
crumb=$(curl -u "jenkins:1234" -s 'http://127.0.0.1:9090/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
# autenticamos el crumb a traves de variable pasada de crumb
curl -u "jenkins:1234" -H "$crumb" -X POST http://127.0.0.1:9090/job/7-ENV/build?delay=0sec
```  

+ Ahora con parametros:  
```
[isx46410800@miguel jenkins]$ cat crumb.sh 
# generamos el crum, el usuario que queremos, -s de silencioso el output y la url de jenkins
crumb=$(curl -u "jenkins:1234" -s 'http://127.0.0.1:9090/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
#con parametros
curl -u "jenkins:1234" -H "$crumb" -X POST http://127.0.0.1:9090/job/6-db-playbook-ansible-nginx-php/buildWithParameters?EDAD=23
[isx46410800@miguel jenkins]$ bash crumb.sh
```  
> nos sale el index-php solo con los de 22  

## MAIL  

### Configurar envio de notificaciones  

+ Plugin a instalar `Email Extension Plugin`  

+ Vamos a `manage jenkins-conf sistem - E-mail Notification`  

+ Vamos a `Amazon - SES - Stmp settings` y copiamos la direccion del mail 
> email-smtp.eu-west-2.amazonaws.com  

![](./images/trick6.png)  

+ Despues le damos a crear credenciales stmp de amazon y ponemos un usuario `jenkins-user`:  

![](./images/trick7.png)  

```
[isx46410800@miguel jenkins]$ cat credentials.csv 
IAM User Name,Smtp Username,Smtp Password
"jenkins-user",AKIA5RIFOUI3LWLFOOG7,BFW538mmwDzTr4eaMMAzSVlQA57NeH1/Hqvnn3ABJsZ6
```  
![](./images/trick9.png)  


+ Creamos un email de admin en amazon:  

![](./images/trick8.png)  
![](./images/trick10.png)  

+ Probamos el email:  
```
Test e-mail recipient	￼	
Test configuration: miguel14amoros@gmail.com
Email was successfully sent
```  

### Gmail como server de correo  

+ Ponemos nuestro gmail como direccion de correo y luego rellenamos la parte de correo:  

![](./images/trick11.png)  

### Email de error  

+ Cogemos un build e indicamos en la opcion `post-build` nuestro correo para si falla, enviarnos email. Escribimos algo mal y recibimos el email. Lo ponemos correcto y recibimos email de que todo va bien. Si sigue yendo bien, no recibimos email.  

![](./images/trick12.png)  
![](./images/trick13.png)  
![](./images/trick14.png)  
![](./images/trick15.png)  

## MAVEN  

### Instalacion

+ Instalamos el plugin `Maven Integration`  

+ Ejemplo de git maven:  
[maven sample app](https://github.com/jenkins-docs/simple-java-maven-app)  


### Configuracion de un job  
+ Configuracion del job:  
![](./images/maven.png)  

+ Los workspace son las mesas de trabajo donde se deja lo clonado de git y ahi tenemos toda la xixa para trabajar en jenkins.  

+ Configuracion e instalamos maven:  
![](./images/maven_2.png)  

+ Añadimos el paso de construir tarea de maven:  
![](./images/maven_3.png)  
> lo que hace todo el proceso es descargar el codigo fuente de git, instalar la version de maven indicada y despues ejecuta el comando de `-B -DskipTests clean package de maven` que jenkins coja el codigo fuente y lo construya(package) un `.jar` de la app y se ejecuta en un workspaces donde jenkins crea un `pom.xml` que necesita maven.  

+ Despues añadimos que despues de todo esto haga un test:  
![](./images/maven_4.png)  
![](./images/maven_5.png)  
![](./images/maven_6.png)  

+ Añadimos otra opción de desplegar el jar:  
`+ java -jar /var/jenkins_home/workspace/8-MavenJob/target/my-app-1.0-SNAPSHOT.jar`  

![](./images/maven_7.png)  
![](./images/maven_8.png)  

### Registrar los resultados  

+ Añadimos acción para ejecutar despues(post build) con la opcion de publicar los resultados de tests Junit(Publish JUnit test result report)--> `target/surefire-reports`  

+ Vemos que nos sale una grafica y una nueva pestaña de test results:  

![](./images/maven_9.png)  
![](./images/maven_10.png)  
![](./images/maven_11.png)  


### Archivar los jar  

+ Añadimos otra acción post build de archivar los *.jar y vemos que nos aparece una nueva pestaña para descargar el archivo jar:  

![](./images/maven_12.png)  
![](./images/maven_13.png)  

+ Podemos añadir la alerta de email si falla:  
![](./images/maven_14.png)  

## GIT SERVER  

+ Creamos en el docker-compose un git-server siguiendo estas [instruciones](https://docs.gitlab.com/omnibus/docker/)  

+ Cambiamos el puerto local del servicio web para que no se colpasen:  
```
web:
    container_name: web
    image: ansible-web
    build:
      context: jenkins-ansible/web
    ports:
      - "8888:80"
    networks:
      - net
  git:
    container_name: git-server
    hostname: gitlab.example.com
    ports:
      - "443:443"
      - "80:80"
    volumes:
      - "/srv/gitlab/config:/etc/gitlab"
      - "/srv/gitlab/logs:/var/log/gitlab"
      - "/srv/gitlab/data:/var/opt/gitlab"
    image: gitlab/gitlab-ce
    networks:
      - net
```  

+ Ponemos la url en /etc/hosts para asignar la ip al servicio mejor:  
`127.0.0.1 gitlab.example.com`  

+ Entramos, nos registramos con `root` y `12345678` y creamos un grupo llamado `jenkinsci`.  

![](./images/git.png)  

+ Despues creamos un proyecto, lo llamamos `maven`.  

+ Despues vamos a usuarios y creamos un usuario nuevo `miguel` con acceso regular. Luego editamos el usuario y le ponemos una contraseña `12345678`.  

+ Luego vamos al proyecto creado de `jenkinsci/maven` y vamos a `manage settings` y añadimos como usuario developer al user creado.  
> NOTA: lo pondremos en modo mantainer, un nivel superior, para poder hacer el primer push al crear la rama master con git pusg -u origin master.  

![](./images/git2.png)  

+ Despues clonamos el repo de maven con el simple app maven y clonamos el nuevo repo vacio y copiamos los archivos de uno a otro, hacemos un push y ya tenemos todo el contenido.  

### CAMBIO URL MAVEN/GIT/JENKINS  

+ Vemos la url de mi repo git en:  
```
[isx46410800@miguel maven]$ cat .git/config 
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = http://gitlab.example.com/jenkinsci/maven.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```  

+ En jenkins vamos a credenciales y le damos al de la llave naranja y creamos las credenciales del git de dentro del docker:  

![](./images/git3.png)  
![](./images/git4.png)  

+ Una vez hecho esto, vamos a configurar el job que teniamos de maven y cambiamos el SCM por la url de nuestro git creado. Deberiamos poner la url de nuestro git `http://gitlab.example.com/jenkinsci/maven.git` pero como nuestro servicio especificado en docker-compose lo tenemos como git, ponemos `http://git/jenkinsci/maven.git`. Ponemos las credenciales de nuestro git y construimos el build viendo que lo descarga de nuestro gir y funciona.   

![](./images/git5.png)  
![](./images/git6.png)  
![](./images/git7.png)  

+ Vamos al container de git-server dentro donde se esconde el contenido del repo maven: `root@gitlab:/var/opt/gitlab/git-data/repositories/@hashed/6b/86/6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b.git`  

+ Creamos dentro el directorio `mkdir custom_hooks`  y el file `post-receive`  

```
#!/bin/bash
# Get branch name from ref head
if ! [ -t 0 ]; then
  read -a ref
fi
IFS='/' read -ra REF <<< "${ref[2]}"
branch="${REF[2]}"
# preguntamos por el nombre del branch(master)
# si es master hacemos el gatillar con crumb
if [ $branch == "master" ]; then
crumb=$(curl -u "jenkins:1234" -s 'http://jenkins.local:9090/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
curl -u "jenkins:1234" -H "$crumb" -X POST http://jenkins.local:9090/job/8-MavenJob/build?delay=0sec

  if [ $? -eq 0 ] ; then
    echo "*** Ok"
  else
    echo "*** Error"
  fi
fi
```  
> Con esto lo que queremos hacer es que cuando hagamos un push al repo git, como hay cambios, se haga automatico un job en el job de maven.  

+ Despues le damos `chmod +x post-receive` y `chown git:git custom_hooks`  

+ Hacemos un push y se debería hacer automatico el build de maven job.  

## JOB DSL  

+ Instalamos el plugin `Job DSL`  
> nos permite crear jobs codigo  

+ SEED JOB es el job padre que hará ejecutar a los jobs hijos.  

+ Construimos un job y vamos a la opción `build - process job DSLs`  

+ [Documentación de job dsl](https://jenkinsci.github.io/job-dsl-plugin/)  

### SEED JOB  
+ Ejemplo estructura:  
```
job('job_dsl_example') {
}
```

![](./images/dsl.png)  
![](./images/dsl2.png)  
![](./images/dsl3.png)  

### DESCRIPCION  
+ Indicamos la descripcion del job hijo:  
```
job('job_dsl_example') {
    description('This is my awesome Job')
}
```  

![](./images/dsl4.png)  
![](./images/dsl5.png)  
> Con la descripcion te crea un job hijo que te dice la descripcion indicada  

### PARÁMETROS  
+ Para poner parametros en el job:  
```  
job('job_dsl_example') {
    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
}
```  

![](./images/dsl6.png)  
![](./images/dsl7.png)  
> Te crea el job fijo con una descripcion y tres variables parametrizadas.  

### SCM  
+ La administracion del codigo fuente:  
```  
job('job_dsl_example') {
    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
}
```  

![](./images/dsl8.png)  
> Con SCM definimos la url y la branch del codigo fuente git en este caso.  

### TRIGGERS  
+ Cron de tareas:  
```  
job('job_dsl_example') {
    description('This is my awesome Job')  
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
    triggers {
        cron('H 5 * * 7')
    }
}
```  

![](./images/dsl9.png)  
> Definimos un trigger en este caso con un cron.  

### STEPS  
+ Son los pasos que va hacer nuestro job, lo que se va ir ejecutando.  

```
job('job_dsl_example') {

    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
    triggers {
        cron('H 5 * * 7')
    }
    steps {
        shell("echo 'Hello World'")
    }
}
``` 
![](./images/dsl10.png)  
> Paso de hacer un hello world   

### MAILER  
+ Sirve para indicar el aviso de notificaciones por correo:  

```
job('job_dsl_example') {
    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
    triggers {
        cron('H 5 * * 7')
    }
    steps {
        shell("echo 'Hello World'")
        shell("echo 'Hello World2'")
    }
    publishers {
        mailer('me@example.com', true, true)
    }
}
```  

![](./images/dsl11.png)  
> Indicamos el aviso de notificaciones.  

### JOB DE ANSIBLE EN DSL  
+ En este ejemplo vamos a hacer el job número de 6 de ansible con gnix php jenkins en JOBDSL:  

![](./images/dsl12.png)  
![](./images/dsl13.png)  
![](./images/dsl14.png) 
![](./images/dsl15.png)  
> esto es lo que teniamos en el job6 de ansible.  

+ EJEMPLO JOBDSL, LO MÁS UTILIZADO:  
```
job('job_dsl_example') {
    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
    triggers {
        cron('H 5 * * 7')
    }
    steps {
        wrappers {
            colorizeOutput(colorMap = 'xterm')
      }
        ansiblePlaybook('/etc/ansible/plays/i2b-cl/some_playbook.yml') {
            inventoryPath('/etc/ansible/plays/i2b-cl/hosts')
            tags('cool')
            forks(1)
            colorizedOutput(true)
            additionalParameters('--vault-password-file $HOME/pass-vault/i2b-cl.txt')
            extraVars {
                extraVar("whoami", '${param1}', false)
                extraVar("my_pass", 'some_pass', true)
            }
        }
    }
    publishers {
        mailer('me@example.com', true, true)
    }
}
```  

[AYUDA ANSIBLE DSL](https://github.com/jenkinsci/ansible-plugin)  

+ Creamos nuestro archivo jobdsl de ansible.js:  

```
job('ansible-dsl') {
    description('Este es un job de ansible con dsl')
    parameters {
        choiceParam('EDAD', ['20', '21', '22', '23', '24', '25'])
    }
    steps {
        wrappers {
            colorizeOutput(colorMap = 'xterm')
      }
        ansiblePlaybook('/var/jenkins_home/ansible/people.yml') {
            inventoryPath('/var/jenkins_home/ansible/hosts')
            colorizedOutput(true)
            extraVars {
                extraVar("EDAD", '${EDAD}', false)
            }
        }
    }
}
```  
![](./images/dsl16.png)  
![](./images/dsl17.png)  
![](./images/dsl18.png) 
![](./images/dsl19.png)  

+ Nos da un error que ya nos daba en su momento y lo que tenemos que hacer es entrar al contenedor web y cambiar los permisos:  
`chown remote_user:remote_user -R /var/www/html/`

![](./images/dsl20.png)  
![](./images/dsl21.png) 
![](./images/dsl22.png)  


### JOB DE MAVEN EN DSL  
+ Seguimos el job8 de maven pero ahora en DSL:  

```
job('maven_dsl') {
    description('Maven dsl project')
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master', {node -> node / 'extensions' << '' })
    }
    steps {
        maven {
            mavenInstallation('jenkins-maven')
            goals('-B -DskipTests clean package')
        }
        maven {
            mavenInstallation('jenkins-maven')
            goals('test')
        }
        shell('''
            echo "**************************"
            echo "Desplegando el jar"
            echo "**************************"
            java -jar /var/jenkins_home/workspace/8-MavenJob/target/my-app-1.0-SNAPSHOT.jar
        ''')
    }
    publishers {
        archiveArtifacts('target/*.jar')
        archiveJunit('target/surefire-reports/*.xml')
        mailer('miguel14amoros@gmail.com', true, true)
    }
}
```

![](./images/dsl23.png)  
![](./images/dsl24.png) 
![](./images/dsl25.png)  
![](./images/dsl26.png)  
![](./images/dsl27.png) 

### DSL en GIT  
+ Vamos a nuestro git-server `http://gitlab.example.com:443`  

+ Creamos un nuevo proyecto `dsl` y lo clonamos y creamos un fichero copiando todo lo hecho en jobdsl padre:  
```
job('job_dsl_example') {
    description('This is my awesome Job')
    parameters {
        stringParam('Planet', defaultValue = 'world', description = 'This is the world')
	booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
    }
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')
    }
    triggers {
        cron('H 5 * * 7')
    }
    steps {
        shell("echo 'Hello World'")
        shell("echo 'Hello World2'")
    }
    publishers {
        mailer('me@example.com', true, true)
    }
}
job('ansible-dsl') {
    description('Este es un job de ansible con dsl')
    parameters {
        choiceParam('EDAD', ['20', '21', '22', '23', '24', '25'])
    }
    steps {
        wrappers {
            colorizeOutput(colorMap = 'xterm')
      }
        ansiblePlaybook('/var/jenkins_home/ansible/people.yml') {
            inventoryPath('/var/jenkins_home/ansible/hosts')
            colorizedOutput(true)
            extraVars {
                extraVar("EDAD", '${EDAD}', false)
            }
        }
    }
}
job('maven_dsl') {
    description('Maven dsl project')
    scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app', 'master', {node -> node / 'extensions' << '' })
    }
    steps {
        maven {
            mavenInstallation('jenkins-maven')
            goals('-B -DskipTests clean package')
        }
        maven {
            mavenInstallation('jenkins-maven')
            goals('test')
        }
        shell('''
            echo "**************************"
            echo "Desplegando el jar"
            echo "**************************"
            java -jar /var/jenkins_home/workspace/8-MavenJob/target/my-app-1.0-SNAPSHOT.jar
        ''')
    }
    publishers {
        archiveArtifacts('target/*.jar')
        archiveJunit('target/surefire-reports/*.xml')
        mailer('miguel14amoros@gmail.com', true, true)
    }
}
```  

## PIPELINES  
+ Flujo de trabajo por el que tiene que pasar nuestro código para llegar a producción.  

+ Jenkins es, fundamentalmente, un motor de automatización que soporta un número de patrones de automatización. Pipeline añade un poderoso conjunto de herramientas de automatización a Jenkins, soportando casos de uso que van desde la simple integración continua hasta las tuberías completas de CD. Al modelar una serie de tareas relacionadas, los usuarios pueden aprovechar las muchas características de Pipeline:

1. Código: Pipeline se implementa en código y normalmente se comprueba en el control de la fuente, dando a los equipos la capacidad de editar, revisar e iterar en su tubería de entrega.

2. Duradero: Los oleoductos pueden sobrevivir tanto a los reinicios planificados como a los no planificados del maestro Jenkins.

3. Pausable: Los oleoductos pueden opcionalmente detenerse y esperar la entrada o aprobación humana antes de continuar el recorrido del oleoducto.

4. Versátil: Los oleoductos soportan complejos requisitos de CD del mundo real, incluyendo la capacidad de bifurcarse/unirse, hacer bucles y realizar trabajos en paralelo.

5. Extensible: El plugin Pipeline soporta extensiones personalizadas para su nota al pie de página DSL:dsl:[] y múltiples opciones para la integración con otros plugins.

+ Mientras que Jenkins siempre ha permitido formas rudimentarias de encadenar Trabajos de Estilo Libre para realizar tareas secuenciales, [4] Pipeline hace de este concepto un ciudadano de primera clase en Jenkins.

+ Construido sobre el valor central de Jenkins de la extensibilidad, Pipeline es también extensible tanto por los usuarios con las Bibliotecas Compartidas de Pipeline como por los desarrolladores de plugins. [5]

+ El siguiente diagrama de flujo es un ejemplo de un escenario de CD fácilmente modelado en la tubería de Jenkins:


![](./images/pipeline.png)  

+ Plugin `Pipeline`  

### JENKINSFILE  

+ Estructura:  

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```  

+ AGENT: es quien ejecuta el pipeline. ANY quiere decir que cualquiera que esté libre lo ejecute, sino, hay que especificar el agente.  

![](./images/pipeline2.png)  
![](./images/pipeline3.png)  
![](./images/pipeline4.png)  
![](./images/pipeline5.png)  

### MULTIPLE-STEPS  

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Este es mi primer pipeline"'
                sh '''
                    echo "Por cierto, puedo ejecutar más acciones aquí"
                    ls -lah
                '''
            }
        }
    }
}
```  

### POST-ACTIONS  

```
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'echo "Fail!"; exit 1'
            }
        }
    }
    post {
        always {
            echo 'Siempre me voy a ejecutar :D'
        }
        success {
            echo 'Solo me ejecutaré si el build no falla'
        }
        failure {
            echo 'Solo me ejecutaré si el build falla'
        }
        unstable {
            echo 'Solo me ejecutaré si me marco como inestable'
        }
        changed {
            echo 'El pipeline estaba fallando pero ahora está correcto o visceversa'
        }
    }
}
```  

### RETRY  

```
pipeline {
    agent any
    stages {
        stage('Timeout') {
            steps {
                retry(3) {
                    sh 'No voy a funcionar :c'
                }
            }
        }
    }
}
```  

### TIMEOUT  

```
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                retry(3) {
                    sh 'echo hola'
                }

                timeout(time: 3, unit: 'SECONDS') {
                    sh 'sleep 5'
                }
            }
        }
    }
}
#########
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                timeout(time: 2, unit: 'SECONDS') {
                    retry(5) {
                        sh 'sleep 3'
                    }
                }
            }
        }
    }
}
```  

### VARIABLES ENV  

```
pipeline {
    agent any
    environment {
        NOMBRE = 'ricardo'
        APELLIDO    = 'gonzalez'
    }

    stages {
        stage('Build') {
            steps {
                sh 'echo $NOMBRE $APELLIDO'
            }
        }
    }
}
```  

### CREDENCIALES    

```
pipeline {
    agent any
    environment {
        secretito = credentials('TEST')
    }
    stages {
        stage('Example stage 1') {
            steps {
                sh 'echo $secretito'
            }
        }
    }
}
```  

## CI/CD  

### BUILD  
+ Instalamos Docker dentro de un container Jenkins con el dockerfile de la carpeta pipelines y modificamos el Jenkins del docker-compose para poner el de la imagen creada por el dockerfile:  
```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/docker
    build:
      context: pipelines
    ports:
      - "9090:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - net
```  

+ Cambiamos permisos para tener docker dentro con usuario jenkins:  
`[isx46410800@miguel jenkins]$ docker exec -it -u root jenkins /bin/bash`  
`chown jenkins /var/run/docker.sock`  

+ Copiamos la carpeta de maven dentro de la carpeta pipelines:  
`[isx46410800@miguel jenkins]$ cp -r maven/ pipelines/java-app`  

+ Iniciamos un container:  
`docker run --rm -v /root/.m2:/root/.m2 -v $PWD/java-app:/app -w /app maven:3-alpine mvn -B -Dskiptests clean package`  
> lo que hacemos es crear un contenedor con los volumes donde va el contenido de maven, volcamos el contenido de javaapp a app, -w para indicar el directorio activo, la version de maven, el comando hacer para generar un jar y --rm para que se elimine. Tendremos el jar construido en nuestro java-app/target/*.jar  

+ Creamos script automatizado:  
```
#!/bin/bash
echo "*************"
echo "Construyendo jar de mi app java"
echo "*************"
# Con esto construiriamos el container pero no deja la orden directa:
#docker run --rm -v /root/.m2:/root/.m2 -v $PWD/java-app:/app -w /app maven:3-alpine mvn -B -Dskiptests clean package
# Para luego pasarle como argumento la orden
docker run --rm -v /root/.m2:/root/.m2 -v $PWD/java-app:/app -w /app maven:3-alpine "$@"
``` 
> Ejecutamos:  ./jenkins/build/mvn.sh mvn -B -DskipTests clean package  

+ Creamos un dockerfile con solo java y el jar creado en /jenkins/build/.  

+ Lo ejecutamos:  
`[isx46410800@miguel build]$ docker build -f Dockerfile-java -t test .`  

+ Comprobamos lo creado:  
```
[isx46410800@miguel build]$ docker run --rm -it test sh
/ # ls /app
app.jar
/ # 
```  

+ Creamos un docker-compose para automatizar esta creacion de la imagen:  
```
version: '3'
services:
  app:  
    image: "app:$BUILD_TAG"
    build:
      context: .
      dockerfile: Dockerfile-java
```  
+ Comprobamos:  
`[isx46410800@miguel build]$ export BUILD_TAG=12`  
`[isx46410800@miguel build]$ docker-compose -f docker-compose-build.yml build`  

+ Crear un script para automatizar la creación del docker-compose de la imagen:  
```
#!/bin/bash
# Copia el jar
cp -f java-app/target/*.jar jenkins/build/  
echo "######################"
echo "*** Building image ***"
echo "######################"
cd jenkins/build/ && docker-compose -f docker-compose-build.yml build --no-cache
```  

+ Lo comprobamos:  
```
[isx46410800@miguel pipelines]$ bash jenkins/build/build.sh 
######################
*** Building image ***
######################
Building app
Step 1/4 : FROM openjdk:8-jre-alpine
 ---> f7a292bbb70c
Step 2/4 : RUN mkdir /app
 ---> Running in 3997da6947f6
Removing intermediate container 3997da6947f6
 ---> f5f751fbe6ab
Step 3/4 : COPY *.jar  /app/app.jar
 ---> 9dc51ae21e48
Step 4/4 : CMD java -jar /app/app.jar
 ---> Running in dd03ae766c0e
Removing intermediate container dd03ae766c0e
 ---> 48409229a4e8
Successfully built 48409229a4e8
Successfully tagged app:13
```  

+ Lo agregamos al Jenkinsfile:  
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                        ./jenkins/build/mvn.sh mvn -B -DskipTests clean package
                        ./jenkins/build/build.sh
                    '''
            }
        }
        stage('Test') {
            steps {
                sh 'echo test'
            }
        }
        stage('Push') {
            steps {
                sh 'echo push'
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo deploy'
            }
        }
    }
}
```  

### TEST  
+ Para hacer el test de maven de la aplicación se utiliza el `mvn test`:  
`[isx46410800@miguel build]$ docker run --rm -v /root/.m2:/root/.m2 -v $PWD/java-app:/app -w /app maven:3-alpine mvn test`  

+ Vemos los test en java-app/target/surefire-reports:  
```
[isx46410800@miguel pipelines]$ ll java-app/target/surefire-reports/
total 12
-rw-r--r--. 1 root root  270 Sep 30 02:45 com.mycompany.app.AppTest.txt
-rw-r--r--. 1 root root 4764 Sep 30 02:45 TEST-com.mycompany.app.AppTest.xml
```  

+ Ahora queremos automatizar los tests con un script:  
```
[isx46410800@miguel pipelines]$ mkdir jenkins/test
[isx46410800@miguel pipelines]$ vi jenkins/test/test.sh
#!/bin/bash
echo "################"
echo "*** Testing  ***"
echo "################"
docker run --rm -v /root/.m2:/root/.m2 -v /home/ricardo/jenkins/jenkins_home/workspace/pipeline-docker-maven/java-app:/app -w /app maven:3-alpine "$@"
[isx46410800@miguel pipelines]$ chmod +x jenkins/test/test.sh
```  

+ Comprobamos:  
```
[isx46410800@miguel pipelines]$ bash jenkins/test/test.sh mvn test
################
*** Testing  ***
################
```  

+ Agregamos el test al Jenkinsfile:  
```
stage('Test') {
            steps {
                sh './jenkins/test/test.sh mvn test'
	    }
        }
```  

### PUSH A MAQUINA REMOTA AWS  
+ Nos creamos una maquina virtual o maquina en amazon:  
`[isx46410800@miguel .ssh]$ ssh -i mykeypair.pem fedora@18.133.221.84`  

+ Tenemos que tener unas llaves ssh creadas en la maquina remota para poder conectarnos sin contraseña:  
`[fedora@ip-172-31-28-138 ~]$ ssh-keygen -f ssh-aws-jenkins`  

+ Creamos un `DOCKER REGISTRY`:  
`[fedora@ip-172-31-28-138 .ssh]$ docker run -d -p 5000:5000 --name registry registry:2`  
[Ayuda](https://docs.docker.com/registry/)  

+ Vemos que está:  
```
[fedora@ip-172-31-28-138 .ssh]$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
2ebffab5d6d6        registry:2          "/entrypoint.sh /etc…"   50 seconds ago      Up 49 seconds       0.0.0.0:5000->5000/tcp   registry
```  

+ En contenido está en `/var/lib/registry`  

+ Creamos un directorio para meter las cosas en este volumen de registros:  
`[fedora@ip-172-31-28-138 ~]$ mkdir tmp_registry`  
`[fedora@ip-172-31-28-138 ~]$ docker run -d -p 5000:5000 --name registry -v $PWD/tmp_registry:/var/lib/registry registry:2`   

+ Estamos en el AWS en nuestra maquina remota, por lo tanto estamos en local, localhost y queremos ver como bajamos un container y lo subimos a nuestro docker de registros creado anteriormente:  
```
[fedora@ip-172-31-28-138 ~]$ docker pull hello-world
[fedora@ip-172-31-28-138 ~]$ docker tag hello-world localhost:5000/hello-world
[fedora@ip-172-31-28-138 ~]$ docker push localhost:5000/hello-world
[fedora@ip-172-31-28-138 ~]$ ll tmp_registry/
total 4
drwxr-xr-x. 3 root root 4096 Oct  1 18:38 docker
[fedora@ip-172-31-28-138 ~]$ ll tmp_registry/docker/registry/v2/repositories/hello-world/
total 12
drwxr-xr-x. 3 root root 4096 Oct  1 18:38 _layers
drwxr-xr-x. 4 root root 4096 Oct  1 18:38 _manifests
drwxr-xr-x. 2 root root 4096 Oct  1 18:38 _uploads
```  
> Como pusimos que el contenido que vaya al contenedor de registros se guarde en nuestra carpeta creada de tmp_registry, vemos ahi la xixa nueva.  

+ Ahora queremos que desde la maquina de casa se pueda subir cosas a este contenedor de registros de AWS:  
```
[isx46410800@miguel pipelines]$ sudo vim /lib/systemd/system/docker.service 
# Añadimos lo siguiente en la linea de EXECSTART de SERVICE(ip/puerto de aws)
--insecure-registry 18.133.221.84:5000
# añadimos el puerto 5000 en el security group de la maquina para poder verse amazon y mi maquina por ese puerto
# comprobamos la conexion desde mi maquina a AWS con telnet
[isx46410800@miguel pipelines]$ telnet 18.133.221.84 5000
[isx46410800@miguel pipelines]$ sudo systemctl daemon-reload 
[isx46410800@miguel pipelines]$ sudo systemctl restart docker
```  

+ Probamos ahora subirlo desde casa al docker registry de AWS:  
`[isx46410800@miguel pipelines]$ docker pull hello-world`  
`[isx46410800@miguel pipelines]$ docker tag hello-world:latest 18.133.221.84:5000/hello-world-casa`  
`[isx46410800@miguel pipelines]$ docker push 18.133.221.84:5000/hello-world-casa`  

```
[fedora@ip-172-31-28-138 ~]$ ll tmp_registry/docker/registry/v2/repositories/
total 8
drwxr-xr-x. 5 root root 4096 Oct  1 18:38 hello-world
drwxr-xr-x. 5 root root 4096 Oct  1 18:56 hello-world-casa
```  

### CERTIFICADO SSL REGISTRY CON AUTENTICACION  
+ Creamos unos directorios `tmp-jenkins/certs`  
+ Creamos el fichero `nginx.conf`:  
```
server {
  listen	80;
  # reemplaza segun tus registros DNS
  server_name ec2-18-133-221-84.eu-west-2.compute.amazonaws.com;
  location ^~ /.well-known/acme-challenge/ {
    default_type "text/plain";
    root /mnt;
  }
}
```  

+ Arrancamos el contenedor:  
`[fedora@ip-172-31-28-138 certs]$ docker run --rm -v $PWD/nginx.conf:/etc/nginx/conf.d/default.conf -v $PWD/letsencrypt:/etc/letsencrypt -p 80:80 -it nginx:alpine sh`  

+ Instalamos certbot dentro del container que sirve para crear certificados SSL gratuidos durante 3 meses:  
```
/ # nginx
/ # apk add --update certbot
# certbot certonly --email miguel14amoros@gmail.com --agree-tos --non-interactive --webroot -w "/mnt" -
d 18.133.221.84
```  

+ PUSH de imagen con scrip a nuestro registry de amazon o dockerhub. Creamos un directorio en pipelines/jenkins/push:  
```
#!/bin/bash
echo "########################"
echo "*** Preparing to push ***"
echo "########################"
REGISTRY="isx46410800" // "18.133.211.84:5000"
IMAGE="app"
echo "*** Logging in ***"
docker login
echo "*** Tagging image ***"
docker tag $IMAGE:$BUILD_TAG $REGISTRY/$IMAGE:$BUILD_TAG
echo "*** Pushing image ***"
docker push $REGISTRY/$IMAGE:$BUILD_TAG
```  
> Tenemos ya bajada una imagen llamada APP y un export BUILD_TAG=13  

+ Probamos primero y lo agregamos al Jenkinsfile:  
`[isx46410800@miguel pipelines]$ bash jenkins/push/push.sh`    

![](./images/push.png)  

```
pipeline {
    agent any
    stages {

        stage('Build') {
            steps {
                sh '''
                        ./jenkins/build/mvn.sh mvn -B -DskipTests clean package
                        ./jenkins/build/build.sh
                    '''
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/test/test.sh mvn test'
            }
        }
        stage('Push') {
            steps {
                sh './jenkins/push/push.sh'
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo deploy'
            }
        }
    }
}
```  

### DEPLOY  
+ En deploy/deploy.sh  
```
#!/bin/bash
# Transferimos variables
echo app > /tmp/.auth
echo $BUILD_TAG >> /tmp/.auth
# Copiamos el fichero a AWS
scp -i ~/.ssh/mykeypair.pem /tmp/.auth fedora@18.133.221.84:/tmp/.auth
```  

+ Lo copiamos a nuestra AWS:  
`scp -i mykeypair.pem /tmp/.auth fedora@18.133.221.84:/tmp/.auth`  

+ Creamos en AWS un docker-compose:  
```
version: '3'
services:
  app:
    image: "$REGISTRY/$IMAGE:$TAG"
    container_name: app
```  

+ Exportamos las variables:  
```
[fedora@ip-172-31-28-138 jenkins]$ export REGISTRY="isx46410800"
[fedora@ip-172-31-28-138 jenkins]$ export IMAGE=$(sed -n '1p' /tmp/.auth)
[fedora@ip-172-31-28-138 jenkins]$ export TAG=$(sed -n '2p' /tmp/.auth)
```  

+ Comprobamos que descarga la imagen:  
`[fedora@ip-172-31-28-138 jenkins]$ docker-compose up -d`  

+ Creamos otro fichero publish para pasar las cosas a la remota:  
```
[isx46410800@miguel jenkins]$ cat deploy/publish.sh 
#!/bin/bash
export REGISTRY="isx46410800"
export IMAGE=$(sed -n '1p' /tmp/.auth)
export TAG=$(sed -n '2p' /tmp/.auth)
docker login
cd ~/jenkins && docker-compose up -d
```  

+ Añadimos en deploy/deploy.sh:  
```
# Transferimos variables
echo "app" > /tmp/.auth
echo $BUILD_TAG >> /tmp/.auth
# Copiamos el fichero a AWS
scp -i ~/.ssh/mykeypair.pem /tmp/.auth fedora@18.133.221.84:/tmp/.auth
scp -i ~/.ssh/mykeypair.pem ./jenkins/deploy/publish.sh fedora@18.133.221.84:/tmp/publish.sh
```  
`[isx46410800@miguel pipelines]$ bash jenkins/deploy/deploy.sh`  

+ En AWS ejecutamos el /tmp/publish.sh y se arranca el docker-compose creado en ~/jenkins.  

+ Ahora hacemos que se ejecute directamente todo esto desde el deploy.sh en la maquina remota:  
```
#!/bin/bash
# Transferimos variables
echo "app" > /tmp/.auth
echo $BUILD_TAG >> /tmp/.auth
# Copiamos el fichero a AWS
scp -i ~/.ssh/mykeypair.pem /tmp/.auth fedora@18.133.221.84:/tmp/.auth
scp -i ~/.ssh/mykeypair.pem ./jenkins/deploy/publish.sh fedora@18.133.221.84:/tmp/publish.sh
ssh -i ~/.ssh/mykeypair.pem fedora@18.133.221.84 /tmp/publish.sh
```  

![](./images/deploy.png)  

+ Añadimos al Jenkinsfile la parte del deploy:  
```
stage('Deploy') {
            steps {
		        sh './jenkins/deploy/deploy.sh'
            }
        }
```  

### CI/CD  
+ Creamos un proyecto de `pipeline-maven` en nuestro git-server y seguimos los pasos que nos indica el repositorio vacio para poder meter todo el contenido de pipelines en nuestro git.  
```
[isx46410800@miguel pipelines]$ git init
Initialized empty Git repository in /home/isx46410800/Documents/jenkins/pipelines/.git/
[isx46410800@miguel pipelines]$ git remote add origin http://gitlab.example.com/jenkinsci/pipeline-maven.git
[isx46410800@miguel pipelines]$ rm -rf java-app/.git/
[isx46410800@miguel pipelines]$ git add Jenkinsfile java-app/ jenkins/
[isx46410800@miguel pipelines]$ git commit -m "contenido jenkins ci/cd pipeline"; git push -u origin master
```  

+ Cambiamos la ruta del deploy.sh por /opt y lo copiamos al container de jenkins para que use la llave ssh:  
`[isx46410800@miguel pipelines]$ docker cp jenkins/deploy/deploy.sh jenkins:/opt/.`  
`jenkins@ee5ab67daa7d:/$ chmod +x /opt/deploy.sh`  

+ Creamos un proyecto de tipo pipeline `pipeline-docker-maven`  

+ Configuramos el pipeline con SCM de git:  

![](./images/cicd.png)  

+ Modificamos de los ficheros test.sh y deploy.sh la ruta absoluta:  
```
test.sh
#!/bin/bash
echo "################"
echo "*** Testing  ***"
echo "################"
PROJECT="/home/isx46410800/Documents/jenkins/jenkins_home/workspace/pipeline-docker-maven"
docker run --rm -v /root/.m2:/root/.m2 -v $PROJECT/java-app:/app -w /app maven:3-alpine "$@"
+++++++++++++++++++++
mvn.sh
#!/bin/bash
echo "*************"
echo "Construyendo jar de mi app java"
echo "*************"
# Con esto construiriamos el container pero no deja la orden directa:
#docker run --rm -v /root/.m2:/root/.m2 -v $PWD/java-app:/app -w /app maven:3-alpine mvn -B -Dskiptests clean package
# Para luego pasarle como argumento la orden
PROJECT="/home/isx46410800/Documents/jenkins/jenkins_home/workspace/pipeline-docker-maven"
docker run --rm -v /root/.m2:/root/.m2 -v $PROJECT/java-app:/app -w /app maven:3-alpine "$@"
```  

+ Despues entramos al container jenkins para hacer la conexion ssh manual para que no nos pida lo de autenticar conexion en los cripts:  
`ssh -i /opt/mykeypair.pem fedora@18.133.221.84`  
> Hemos copiado mi llave ssh de amazon a opt dentro de jenkins y la ruta de la llave del deploy.sh tambien.  

`[isx46410800@miguel .ssh]$ docker cp mykeypair.pem jenkins:/opt/.`  

+ Añadimos unos post-actions al Jenkisfile para nos de siempre un test de resultados y tambien por si va bien el build de maven guarde el jar:  
```
stage('Build') {
            steps {
                sh '''
                        ./jenkins/build/mvn.sh mvn -B -DskipTests clean package
                        ./jenkins/build/build.sh
                    '''
            }
            post {
                success {
                  archiveArtifacts artifacts 'java-app/target/*.jar', fingerprint: true
                }
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/test/test.sh mvn test'
            }
            post {
                always {
                  junit 'java-app/target/surefire-reports/*.xml'
                }
            }
        }
```  

+ Resultados finales: bajamos el codigo fuente de la app maven, la compilamos, subimos la imagen a dockerhub y mandamos los archivos a AWS para hacer el deploy alli.  

![](./images/cicd2.png)  
![](./images/cicd3.png)  
![](./images/cicd4.png)  
![](./images/cicd5.png)  
![](./images/cicd6.png)  
![](./images/cicd7.png)  














































































































































