# DOCKER

![docker](./images/docker.png)

## Instalación:  
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

## Resumen Comandos  
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