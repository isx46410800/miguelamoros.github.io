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

### Dockerfile  
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
    - EXPOSE: puertos por donde escucha y puedes indicar qué puertos va funcionar mi contenedor.
    - CMD: comando por el cual se ejecuta el container, normalmente un servicio detached `CMD ["apachectl", "-DFOREGORUND"]`.  

```
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




