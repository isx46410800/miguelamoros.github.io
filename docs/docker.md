# DOCKER

![docker](./images/docker.png)

## INSTALACIÓN  
+ Instalar Docker:  
```
$ sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
$ sudo dnf -y install dnf-plugins-core
$ sudo dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo   
$ sudo dnf install docker-ce docker-ce-cli containerd.io
$ sudo systemctl start docker
$ sudo docker run hello-world
```

## COMANDOS  
+ Crear un container Docker:  
```
docker run --rm -it fedora:27//isx46410800/netcat:latest /bin/bash
docker run --rm --name ldap -h ldap -d imagen
```
+ Descagar una imagen:  
`docker pull fedora:27/imagen`
+ Ver imagenes de mi sistema:  
`docker images`
+ Iniciar un container:  
`docker start container`
+ Entrar dentro de un container en otra terminal:  
`docker exec -it nomcontainer /bin/bash`
+ Entrar dentro de un container en detached:  
`docker attach container`
+ Procesos de docker:  
`docker ps -a`  
`docker top container`
+ Iniciar un container:  
`docker start/stop IDcontainer`
+ Cambiar nombre container:  
`docker rename IDcontainer NuevoNombre`
+ Docker version:  
`docker version`
+ Info de un docker:  
`docker info`
+ Lista de containers:  
`docker container ls -a`
+ Borrar una imagen:  
`docker rmi imagen`
+ Borrar un container:  
`docker rm container`
+ Cambiar etiqueta de un container:  
`docker tag imagen nombreNuevo:tag`  
+ Borrar imagenes none:  
`docker images -f dangling=true | xargs docker rmi`  
+ Crear y subir una imagen a DockerHub:  
```
docker login
docker tag imagen nuevoimagen:tag
docker push nuevoimagen:tag
```
+ Copiar un fichero a fuera del docker o dentro:  
```
docker cp file container:/opt/docker/.
docker cp container:/opt/docker/. file
```
+ Docker con puerto mapeado para el exterior:  
`docker run --rm --name ldap -h ldap -p 389:389 -p 80:80 -it isx/ldap /bin/bash`
> -p puertoMiMaquina:puertoContenedor  
> -x dirActivo dentro del container  


### Redes en Docker:  
```
docker network create NameRed
docker network rm NameRed
docker network inspect NameRed/container
docker network create --subnet 172.19.0.0/16 NameRed
```

### Volumes en Docker:  
```
docker volume create NOMBREVOLUMEN
docker volume ls
docker volume inspect NOMVOLUMEN
ls /var/lib/docker/volumes
--privileged
-v volumen:contenido
```
`docker run --rm --name ldap -h ldap -v NOMVOLUMEN:/var/lib/sambaloQueGuarda --privileged -it isx/ldap /bin/bash`

### Docker Compose:  
```
docker-compose up #enciende todos los dockers del file compose.yml
docker-compose -f fileCompose.yml up (-d) #elegimos que fichero encendemos del compose
docker-compose down #apaga todo
docker-compose ps
docker-compose images
docker-compose top nom_servei
docker-compose port ldap 389 #servicio y puerto elegido
docker-compose push/pull #subir o bajar images
docker-compose logs ldap #logs del servicio elegido
docker-compose pause/unpause ldap #pausar el servicio
docker-compose start/stop ldap #iniciar servicio
docker-compose scale ldap=2 #dos container ldap
```  

### Docker SWARM:  
```
docker swarm init #inicia el docker swarm
docker node ls # lista de nodos del swarm
docker swarm join-tocken manager/worker #une workers o manager
docker stack deploy -c docker_compose.yml nombreAPP #hace deploy
docker stack ps NombreAPP #procesos
docker stack ls #listado
docker stack services nombreAPP #servicios
docker stack rm NombreAPP #parar
docker service ls
docker service ps nombreservicio
docker service inspect nomservicio
docker service scale nomservicio=3
docker swarm leave --force #se desune del swarm
docker swarm init --advertise-addr IP
docker node update --label-add tipus=valor nomNode
docker node inspect nomNode
docker node update --availability active/drain/pause nomNode
```  

## ARQUITECTURA  

+ Docker Host es el servidor físico/real donde se encuentra instalado Docker.  

+ Docker servicio:  
  - Docker Client.  
  - Rest API: es el intermediario encargado de comunicar al Docker client con el Docker server.  
  - Docker Server.  

![](./images/docker0)  

+ Arquitectura Imagen docker (Dockerfile):  
    1. Capa 1 - From: Sistema operativo minimo a elegir.  
    2. Capa 2 - Run: lo que se quiera instalar, ejemplo apache.  
    3. Capa 3 - CMD: lo que se tiene que poner para que cuando se arranque la imagen empiece con ese comando. Normalmente la activación de un servicio en detached.  
> SON CAPAS DE SOLO LECTURA Y NO SE PUEDE MODIFICAR NI BORRAR 

```
FROM centos:7
RUN yum install -y httpd
CMD["apachectl","-DFOREGROUND"]
```  

![](./images/docker2)  

+ Contenedor es una capa addicional en tiempo real de ejecución, el empaquetado de todo el dockerfile. CAPA DE ESCRITURA. Recuerda que la capa del contenedor es temporal y que al eliminar el contenedor, todo lo que haya dentro de ella desaparecerá.    

![](./images/docker3)  
![](./images/docker4)  

+ Se diferencia de una máquina virtual es que un contenedor es como un proceso más del sistema mientras que una MV hay que bajarse una ISO, instalar y agregar RAM, CPU y HD de nuestra propia máquina real.  


## DOCKER IMAGES  
+ Poniendo `docker + SistemaOperativo` podemos adquirir [imágenes oficiales](https://hub.docker.com/) de los propios creadores para poder descargar del repositorio de __DockerHub__ para nuestros contenedores.  

+ Por defecto, sino podemos un tag a la distribución, nos cogerá el `tag latest` sino tendremos que poner la versión concreta como `docker pull mongo:3.6-jessie`. Se actualiza el tag si te bajas una imagen pero está recientemente actualizada y la antigua se queda en _none_.  

+ Vemos las imágenes con:  
`docker images`  

### DOCKERFILE  
+ El fichero para crear nuestra imagen Docker se llama `Dockerfile`.  

+ Para construir la imagen es `docker build -t/--tag imagen:tag . ó -f /rutaDockerfile` .:  
`docker build -t isx46410800/centos:inicial .`  

+ Si modificamos algo del Dockerfile, hay que volver hacer el comando anterior.  
`docker build -t isx46410800/centos:detached images/centos/.`  

+ Ver el historial de construcción de capas de mi imagen:  
`docker history -H imagen:tag`  

+ Borrar una imagen:  
`docker rmi idImagen`  

+ Borrar un contenedor:  
`docker rm contenedorName`  

+ Ver los contenedores:  
`docker ps / docker ps -a`  

+ COMANDOS DOCKERFILE:  
    - FROM: desde donde se baja la imagen de SO.  
    - RUN: para instalar paquetes.  
    - COPY: copia ficheros de fuera hacia el container, ponemos ruta absoluta o del directorio actual.  
    - ADD: lo mismo que copy pero se puede pasar URLs y copiaría la info de la url a donde indiquemos.  
    - ENV: crea variable de entorno.  
    - WORKDIR: directorio activo al entrar.  
    - LABEL: es una etiqueta que puede ir en cualquier sitio, son informativas, es metadata.  
    - USER: quien ejecuta la tarea, por defecto es root.  
    - EXPOSE: puertos por donde escucha y puedes indicar qué puertos va funcionar mi contenedor.  
    - VOLUME: indica donde metemos la data cuando el container se muere.  
    - CMD: comando por el cual se ejecuta el container, normalmente un servicio detached `CMD ["apachectl", "-DFOREGORUND"]`.  

+ Ejemplo Dockerfile:  

```
# De que sistema operativo partimos
FROM centos:7
# Labels de metadata extra
LABEL author="Miguel Amorós"
LABEL description="Mi primer container con Dockerfile"
# Que paquetes a instalar
RUN yum install -y httpd
# Creamos variables de entorno
ENV saludo "Hola Miguel"
# Directorio activo
WORKDIR /var/www/html
# Copiamos un fichero de fuera
COPY ./listaCompra.txt ~/listaCompra.txt
# Prueba de la variable
RUN echo "$saludo" > ~/saludo.txt
# Usuario que ejecuta la tarea
RUN echo "$(whoami)" > ~/user1.txt
RUN useradd miguel 
RUN useradd miguelito
RUN echo "miguel" | passwd --stdin miguel
RUN echo "miguelito" | passwd --stdin miguelito
RUN chown miguel /var/www/html
USER miguel
RUN echo "$(whoami)" > ~/user2.txt
USER root
# Volumen para meter la chicha de cuando se muere el container
VOLUME /tmp/
# Como arrancar el container
CMD ["apachectl", "-DFOREGROUND"]
```  

+ Podemos usar un fichero `.dockerignore` para ignorar ficheros que no queremos que copiemos en el container.  

+ Para ver cualquier CMD para dejar por ejemplo un servicio encendido en detached se usa el comando:  
`docker history -h SO / en docker hub`  

+ Buenas prácticas, cuantas menos lineas de codigo, menos capas se utilizan al construir la imagen:  
```
RUN \
  useradd miguel && \
  useradd miguelito
```  

#### CMD VS ENTRYPOINT  
+ __CMD__: Este comando se encarga de pasar valores por defecto a un contenedor. Entre estos valores se pueden pasar ejecutables. Este comando tiene tres posibles formas de pasar los parámetros:  

`CMD [“parametro1”, “parametro2”, ….]`  
`CMD ["apachectl", "-DFOREGORUND"]`  

+ __ENTRYPOINT__: Este comando se ejecuta cuando se quiere ejecutar un ejecutable en el contenedor en su arranque. Los ejemplos tipo de su uso, son cuando se quiere levantar un servidor web, una base de datos, etc ….  

`ENTRYPOINT comando parametro1 parametro2`  
`ENTRYPOINT cal 2020`  
`ENTRYPOINT cal` # Y pasar por comando los parámetros  

+ Como se ha comentado anteriormente el comando CMD se puede utilizar para pasar parámetros al comando ENRYPOINT. Una posible forma de realizarlo es:  

`ENTRYPOINT ["cal"]`  
`CMD ["2020"]`  

#### CENTOS-PHP-SSL  

+ Crear unaas llaves para certificado SSL:  
`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout dockerssl.key -out dockerssl.crt`  
> Ponemos de commom name localhost

+ Dockerfile:  
```
# SO
FROM centos:7
# paquetes de apache php y ssl 
RUN \
  yum -y install httpd php php-cli php-commom mod_ssl openssl
# dir creado
RUN mkdir /opt/docker
# indice de comprobacion de php
RUN echo "<?php phpinfo(); ?>" > /var/www/html/hola.php
# web de prueba
COPY startbootstrap /var/www/html
# conf del ssl en el fichero de apache de conf
COPY ssl.conf /etc/httpd/conf.d/default.conf
# copia de certificados y startup
COPY dockerssl.crt /opt/docker/dockerssl.crt
COPY dockerssl.key /opt/docker/dockerssl.key
COPY startup.sh /opt/docker/startup.sh
# permisos del startup
RUN chmod +x /opt/docker/startup.sh
# escuchar puerto 443
EXPOSE 443
# arranque
CMD ["/opt/docker/startup.sh"]
```  

+ Podemos eliminar imagenes none con el comando:  
`docker images -f dangling=true | xargs docker rmi`  


#### NGINX-PHP  

+ Dockerfile:  
```
# SO
FROM centos:7
# copiar el repo de nginx
COPY nginx.repo /etc/yum.repos.d/nginx.repo
# instalar paquetes
RUN \
  yum -y install nginx --enablerepo=nginx                         && \
  yum -y install https://repo.ius.io/ius-release-el7.rpm          && \
  yum -y install                                              \
    php71u-fpm \
    php71u-mysqlnd \
    php71u-soap \
    php71u-xml \
    php71u-zip \
    php71u-jason \
    php71u-mcrypt \
    php71u-mbstring \
    php71u-zip \
    php71u-gd \
      --enablerepo=ius-archive && yum clean all
# dir
RUN mkdir /opt/docker
# puertos escuchando
EXPOSE 80 443
# volumenes
VOLUME /var/www/html /var/log/nginx /var/log/php-fpm /var/lib/php-fpm
# copiamos files de conf
COPY index.php /var/www/html/index.php
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY startup.sh  /opt/docker/startup.sh
RUN chmod +x /opt/docker/startup.sh
# arranque
CMD /opt/docker/startup.sh
```  

#### MULTI-STAGE-BUILD  

+ Ejemplo de instalar varias capas de sistemas operativos:  

```
# SO
FROM maven:3.5-alpine as builder
# copiamos la carpeta dentro
COPY app /app
# entramos y empaquetamos
RUN cd /app && mvn package
# desde java 
FROM openjdk:8-alpine
# copiamos desde maven y lanzamos la app
COPY --from=builder /app/target/my-app-1.0-SNAPSHOT.jar /opt/app.jar
# ejecutamos la app
CMD java -jar /opt/app.jar
```  

```
[isx46410800@miguel multi]$ docker build -t isx46410800/java:app .
```  

```
[isx46410800@miguel multi]$ docker run -d isx46410800/java:app
```  

```
[isx46410800@miguel multi]$ docker logs trusting_galois
Hello World!
```  

+ Otro ejemplo:  

```
FROM centos as test
RUN fallocate -l 10M /opt/file1
RUN fallocate -l 20M /opt/file2
RUN fallocate -l 30M /opt/file3
FROM alpine
COPY --from=test /opt/file2 /opt/myfile
```  
> El centos con los 3 files serian 260M pero solo coge de alpine que son 4 y coge el file que le interesa. El total de la imagen es 24M y no la suma de todo.  

#### PRUEBA REAL  

+ La idea de este articulo es que le des solución al siguiente problema utilizando lo que has aprendido.

+ En donde trabajas, solicitan una imagen Docker base para ser reutilizada. Tu tarea es crear un Dockerfile con las siguientes especificaciones y entregarlo a tu jefe:

+ Sistema Operativo Base: CentOs o Debian (A tu elección):

+ Herramientas a instalar:  
  - Apache (Última versión)
  - PHP 7.0

+ Debes usar buenas prácticas.

+ Deberás comprobar su funcionamiento creando un index.php con la función de phpinfo.

+ Dockerfile:  
```
# SO
FROM centos:7
# Instalar apache
RUN yum install -y httpd
# Añadir repo de php para centos7 e instalamos version 7.0
RUN yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum update -y                                                         && \
    yum install -y yum-utils                                              && \
    yum install -y php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo 
# Test de pagina index de php
RUN echo "<?php phpinfo(); ?>" > /var/www/html/index.php
# Volumenes
VOLUME /var/www/html /var/log/php-fpm /var/lib/php-fpm
# copia del startup y permisos
COPY startup.sh opt/docker/startup.sh
RUN chmod +x opt/docker/startup.sh
# Arrancamos el servicio apache en segundo plano
CMD ["opt/docker/startup.sh"]
```  

+ Startup.sh:  
```
#!/bin/bash
# Iniciar contenedor
echo "iniciando container..."
# Encendiendo servicio apache
apachectl -DFOREGROUND
```  

+ Imagen:  
`docker build -t isx46410800/apache:php .`  

+ Contenedor:  
`docker run --name apache_php -p 80:80 -d isx46410800/apache:php`  

+ Funcionamiento:  
```
docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                NAMES
abda827fb9f5        isx46410800/apache:php   "opt/docker/startup.…"   3 seconds ago       Up 1 second         0.0.0.0:80->80/tcp   apache_php
```  

+ Entramos a `localhost:80` y nos saldrá la web `index.php`  

## DOCKER CONTAINERS  

* Son una instancia de ejecución de una imagen  
* Son temporales  
* Capa de lectura y escritura  
* Podemos crear varios partiendo de una misma imagen  

### LISTAR/MAPEO PUERTOS  

+ `docker ps / docker ps -a / docker ps -q(ids)`  

+ PuertoLocal-PuertoContainer:  
`docker run --name jenkins -p 8080:8080 -d jenkins`  
> 0.0.0.0:8080 todas las interfaces de nuestra máquina están mapeadas al puerto 8080. Si mapeamos la misma imagen con otros puertos, tenemos varias imagenes en diferentes puertos.  

+ `docker run --name jenkins -p :8080 -d jenkins`  
> Cualquier primer puerto libre que coja mi maquina se mapea al 8080.  

### INICIAR/DETENE/PAUSAR  

+ Renombrar un contenedor:  
`docker rename nombre_viejo nombre_nuevo`  

+ Parar contenedor:  
`docker stop nombre/id`  

+ Iniciar contenedor:  
`docker start nombre/id`  

+ Reiniciar contenedor:  
`docker restart nombre/id`  

+ Entrar con una terminal al contenedor:  
`docker exec -it nombre /bin/bash`  
`docker exec -u root/user -it nombre /bin/bash`  
> jenkins@bh45fdiu ---> user@id  


### VARIABLES DE ENTORNO  

+ En Dockerfile:  
`ENV variable valor`  

+ En la linea de construir container:  
`docker run --name jenkins -e "varible=valor" -p :8080 -d jenkins`  

### MYSQL  

+ Se ha de instalar el mysql client en las versiones que descargamos de dockerhub, ya que nos falta eso para poder usarlo:  
`yum install -y mysql / apt-get install mysql-client / dnf install mysql-community-server`  

+ [AYUDA MYSQL](https://hub.docker.com/_/mysql)  

+ Creamos contenedor MYSQL siguiendo las instrucciones:  
`docker run --name mysql-db --rm -e "MYSQL_ROOT_PASSWORD=jupiter" -d mysql:5.7`  

```
docker run --name mysql-db --rm -e "MYSQL_ROOT_PASSWORD=jupiter" -d mysql:5.7
fc84bdb48a389c9e7183fd633c0edfb03a7867104e1e867ef321a223f044fe87
docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                 NAMES
fc84bdb48a38        mysql:5.7                "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        3306/tcp, 33060/tcp   mysql-db
```  

+ Para que arranque con todo lo necesario el container:  
`docker logs -f mysql-db`  
> Mensaje final de ready for connections por tal puerto. 

+ Para conectarnos tendríamos que haber mapeado el puerto, no obstante podemos conectarnos sabiendo la IP de nuestro container y añadirsela al comando de mysql de conexion con `docker inspect mysql-db`:  

`[isx46410800@miguel mysql]$ mysql -u root -h 172.17.0.3 -pjupiter`  

![](./images/mysql_1.png)  


+ Mapeando puerto(el de mysql del log) para también poder usarlo mi maquina local, con nuevas variables de entorno siguiendo la guía, creando una db con usuario y passwd:  

`docker run --name mysql-db2 --rm -e "MYSQL_ROOT_PASSWORD=jupiter" -e "MYSQL_DATABASE=docker-db" -e "MYSQL_USER=docker" -e "MYSQL_PASSWORD=docker" -p 3333:3306 -d mysql:5.7`  

```
[isx46410800@miguel mysql]$ docker run --name mysql-db2 --rm -e "MYSQL_ROOT_PASSWORD=jupiter" -e "MYSQL_DATABASE=docker-db" -e "MYSQL_USER=docker" -e "MYSQL_PASSWORD=docker" -p 3333:3306 -d mysql:5.7
b24dff85293ef892f2f9033c231e7a594a1261e9b5924e2a955691cc403eee11
[isx46410800@miguel mysql]$ docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                               NAMES
b24dff85293e        mysql:5.7                "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        33060/tcp, 0.0.0.0:3333->3306/tcp   mysql-db2
b3254fe3706b        mysql:5.7                "docker-entrypoint.s…"   3 minutes ago       Up 3 minutes        3306/tcp, 33060/tcp                 mysql-db
```  

+ Para que arranque con todo lo necesario el container:  
`docker logs -f mysql-db2`  

+ Comprobamos por localhost:  
`[isx46410800@miguel mysql]$ mysql -u root -h 127.0.0.1 -pjupiter --port=3333`  

![](./images/mysql_2.png)  


## DOCKER VOLUMES  

+ Los volúmenes permiten almacenar data persistente del contenedor:  
   * Host  
   * Anonymous  
   * Named Volumes  


### VOLUMES HOST  

+ Son los que se han de crear una carpeta antes y mapear a la carpeta del contenedor el cual queremos guardar la xixa:  

```
mkdir mysql
docker run --name mysql-db -v mysql:/var/lib/sql -e "MYSQL_ROOT_PASSWORD=jupiter" -p 3306:3306 -d mysql:5-7
```  

### VOLUMES ANONYMOYS  

+ Son los que no ponemos ningún volumen de host y se nos añade a cualquier directorio al azar:  

```
docker run --name mysql-db -v /var/lib/sql -e "MYSQL_ROOT_PASSWORD=jupiter" -p 3306:3306 -d mysql:5-7
```  

+ Lo podemos descubrir(Normalmente en `/var/lib/docker/volumes // /user/home/docker/volumes`):  

`docker inspect container | grep mount`  

`docker info | grep -i root`  


### VOLUMES NAMED VOLUMES  

+ Son los que creamos directamente con las ordenes:  

`docker volume create my-vol`  

+ Lo vemos con:  

`docker volume ls`  

+ Y se guardan en:  

`/var/lib/docker/volumes // /user/home/docker/volumes`  

```
docker run --name mysql-db -v my-vol:/var/lib/sql -e "MYSQL_ROOT_PASSWORD=jupiter" -p 3306:3306 -d mysql:5-7
```  

+ Lo podemos descubrir(Normalmente en `/user/home/docker/volumes`):  

`docker volume inspect volumenName`  

`docker inspect container | grep mount`  

`docker info | grep -i root`  


### PRUEBA REAL  

+ Dockerfile:  

```
# SO
FROM centos:7
# Instalar apache
RUN yum install -y httpd
# Añadir repo de php para centos7 e instalamos version 7.0
RUN yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum update -y                                                         && \
    yum install -y yum-utils                                              && \
    yum install -y php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo 
# Test de pagina index de php
RUN echo "<?php phpinfo(); ?>" > /var/www/html/index.php
# copia del startup y permisos
COPY startup.sh /opt/docker/startup.sh
RUN chmod +x /opt/docker/startup.sh
# Arrancamos el servicio apache en segundo plano
CMD ["/opt/docker/startup.sh"]
```  

+ Startup.sh:  

```
[isx46410800@miguel prueba2]$ cat startup.sh 
#!/bin/bash
# Iniciar contenedor
echo "iniciando container..."
# Encendiendo servicio apache
apachectl -DFOREGROUND
```  

+ Creación volumen:  
`[isx46410800@miguel prueba2]$ mkdir data_apache`  

+ Imagen:  

`Sending build context to Docker daemon  4.096kB`  

`docker build -t apache_volume .`  

+ Contenedor:  

`docker run --rm --name apache_volume -v $PWD/data_apache:/var/www/html/ -e "ENV=dev" -e "VIRTUALIZATION=docker" -p 80:80 -d apache_volume`  

+ Resultados:  

`set`  

```
VIRTUALIZATION=docker
ENV=dev
```  

![](./images/volume.png)  
![](./images/volume2.png)  
![](./images/volume3.png)  
![](./images/volume4.png)  



























## DOCKER REGISTRY  

+ Sería la misma función que crear una cuenta en `Dockerhub` y después hacer:  
- `docker login`  
- `docker tag nombre isx4610800/nombre:tag`
- `docker commit isx4610800/nombre:tag`  
- `docker push isx4610800/nombre:tag`  

+ [Documentación Docker Registry](https://docs.docker.com/registry/)  

+ Lo creamos:  
`docker run --name registry -v $PWD/data:/var/lib/registry -p 5000:5000 registry:2`  
> Tenemos que crear un diretorio data donde estemos y podemos ponerle cualquier puerto.  

```
[isx46410800@miguel registry]$ ls
data
[isx46410800@miguel registry]$ docker run --name registry -v $PWD/data:/var/lib/registry -p 5000:5000 -d registry:2
a52169f2861d43450071e5bedeb01380fc2a26fe9030975b127b4a2452e5f62e
[isx46410800@miguel registry]$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
a52169f2861d        registry:2          "/entrypoint.sh /etc…"   3 seconds ago       Up 1 second         0.0.0.0:5000->5000/tcp   registry
```  

+ Subimos una imagen:  
```
[isx46410800@miguel registry]$ docker pull hello-world
Using default tag: latest
latest: Pulling from library/hello-world
Digest: sha256:4cf9c47f86df71d48364001ede3a4fcd85ae80ce02ebad74156906caff5378bc
Status: Image is up to date for hello-world:latest
[isx46410800@miguel registry]$ docker tag hello-world:latest localhost:5000/hello:registry
[isx46410800@miguel registry]$ docker push localhost:5000/hello:registry
The push refers to repository [localhost:5000/hello]
9c27e219663c: Pushed 
registry: digest: sha256:90659bf80b44ce6be8234e6ff90a1ac34acbeb826903b02cfa0da11c82cbc042 size: 525
[isx46410800@miguel registry]$ ls data/docker/registry/v2/repositories/hello/
_layers  _manifests  _uploads
```  

+ Bajar la imagen del registry:  
`docker pull localhost:5000/hello:registry`  

+ Subir/Bajar una imagen desde nuestra IP o hacía nuestra IP:

`[isx46410800@miguel registry]$ sudo vi /lib/systemd/system/docker.service`  

`ExecStart=/usr/bin/dockerd -H unix:// --insecure-registry 192.168.1.144:5000`  

`systemctl daemon-reload`  

`[isx46410800@miguel registry]$ docker push 192.168.1.144:5000/hello:registry`  

+ Ya podremos hacer pull/push a esta IP o por ejemplo a una IP de AWS donde tuvieramos el registry.  


