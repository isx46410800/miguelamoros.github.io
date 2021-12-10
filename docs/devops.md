# CURSO DEVOPS INTEGRADO  

+ [QUÉ ES DEVOPS](https://azure.microsoft.com/es-es/overview/devops-tutorial/#documentation)  

![](./images/devops.png)  

+ La idea es una aplicación hecha en angular con base de datos postgres y servidor web nginx. La cuestión es tener 5 nodos: uno de test, dos de preproducción y dos de producción. De manera más robusta sería tener en cada nodo la app, el server web y la bbdd pero actualmente solo necesitamos tener en cada nodo Docker Engine instalado para una mayor facilidad y rapidez.  

## INSTALACIÓN  

![](./images/devops1.png)  
![](./images/devops2.png)  
> Instalamos Docker engine y docker compose en nuestra máquina. Comprovamos con --version.  

__*NOTA WINDOWS+__  
> Podemos instalar docker engine en [windows](https://docs.docker.com/desktop/windows/install/). Instalamos [docker desktop](https://www.docker.com/products/docker-desktop). Activamos virtualización en la bios y activamos WSL2 con `Enable-WindowsOptionalFeature -Online -FeatureName $("VirtualMachinePlatform", "Microsoft-Windows-Subsystem-Linux")`  

+ Cogemos del repositorio de [dockerhub](https://hub.docker.com/r/sotobotero/udemy-devops/tags) del profe la imagen a descargar que contiene la app de facturación en angular a utilizar en este curso:  
`docker pull sotobotero/udemy-devops:0.0.1`  

+ Iniciamos el contenedor de la app mapeando puertos web y para puerto gráfico:  
`docker run -p 80:80 -p 8080:8080 --name billingapp sotobotero/udemy-devops:0.0.1`  
> Comprobamos que funcionan en www.localhost:80 y www.localhost:8080/swagger-ui/index.html.  

## DOCKER-COMPOSE  

### V1 SCRATCH  

+ Creamos un docker-compose de prueba con dos nombres diferentes para ver como arranca de manera directa o con un nombre diferente:  
```
version: '3.1'

services:

  db:
    container_name: postgres
    image: postgres
    restart: always
    environment:
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: qwerty
      POSTGRES_DB: postgres    

  adminer:
    container_name: adminer
    image: adminer
    restart: always
    depends_on: 
      - db
    ports:
       - 9090:8080
```  
> Descargamos con `docker-compose pull // docker-compose -f fichero.yml pull` e iniciamos con `docker-compose -f fichero.yml up -d`  
> Ahora podemos entrar de manera grafica a postgres con admirer en www.localhost:9090 e introducimos las credenciales y funcionando.  

+ Ahora vamos a crear from scratch un Dockerfile para crear la app de facturación:  
```
#Creamos nuestra app de facturación

#Partimos de imagen base
FROM nginx:alpine

#Instalamos java
RUN apk -U add openjdk8 && rm -rf /var/cache/apk/*;
RUN apk add ttf-dejavu

#Instalamos java microservicios y variables
ENV JAVA_OPTS=""
ARG JAR_FILE
ADD ${JAR_FILE} app.jar

#Instalamos la app en nginx server y creamos un volumen para info y conf
VOLUME /tmp
RUN rm -rf /usr/share/nginx/html/*
COPY nginx.conf /etc/nginx/nginx.conf
COPY dist/billingApp /usr/share/nginx/html
COPY appshell.sh appshell.sh

#mapeamos puertos
EXPOSE 80 8080
ENTRYPOINT ["sh", "/appshell.sh"]
```  
> Lo construimos con `docker build -t facturacionapp:prod --no-cache --build-arg JAR_FILE=target/*.jar .`  
> Lo iniciamos con los puertos EXPOSE `docker run -p 80:80 -p 8080:8080 --name billingapp facturacionapp:prod`  
> Luego podemos subir nuestra version a Dockerhub con `docker tag facturacionapp:prod isx46410800/facturacionapp:1.0` y la subimos`docker push isx46410800/facturacionapp:1.0`

### V2 SERVICES  

+ Siempre es `local-host:container`.  

+ Ahora creamos nuestra app de facturación pero servicio a servicio separado en el docker-compose. Por un lado nos descargamos una imagen de nginx, por otro lado java y despues construimos manualmente con Dockerfile nuestra app angular y nuestra bbdd de postgresql:  
```
version: '3.1'

services:
#database engine service
  postgres_db:
    container_name: postgres
    image: postgres:latest
    restart: always
    environment:
    ports:
      - 5432:5432
    volumes:
        #allow *.sql, *.sql.gz, or *.sh and is execute only if data directory is empty
      - ./dbfiles:/docker-entrypoint-initdb.d
      - /var/lib/postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: qwerty
      POSTGRES_DB: postgres
#database admin service
  adminer:
    container_name: adminer
    image: adminer
    restart: always
    depends_on:
      - postgres_db
    ports:
       - 9090:8080
#Billin app backend service
  billingapp-back:
    build:
      context: ./java
      args:
        - JAR_FILE=*.jar
    container_name: billingApp-back
    environment:
       - JAVA_OPTS=
         -Xms256M
         -Xmx256M
    depends_on:
      - postgres_db
    ports:
      - 8080:8080
#Billin app frontend service
  billingapp-front:
    build:
      context: ./angular
    container_name: billingApp-front
    depends_on:
      - billingapp-back
    ports:
      - 80:80
```  
> Construir las imagenes definidas en la orquestación: `docker-compose -f stack-billing.yml build`  
> Inicializar los contenedores de los servicios de la orquestación: `docker-compose -f stack-billing.yml up -d`    
> No se ha especificado, pero todo esto se crea en una misma red virtual para que se puedan comunicar entre los diferentes servicios.  

+ Resumen de comandos en DOCKER-COMPOSE:  
```
Eliminar todos los contenedores detenidos: `docker system prune` 
Eliminar todas las imágenes: docker rmi $(docker images -a -q)
Listar los volumenes: docker volume ls 
Eliminar todos los volumenes: docker volume prune
Construir las imagenes definidas en la orquestación: docker-compose -f stack-billing.yml build
Inicializar los contenedores de los servicios de la orquestación: docker-compose -f stack-billing.yml up -d
Detener todos los servicios de la orquestación: docker-compose -f stack-billing.yml stop
Escalar un servicio al iniciar la orquestación: docker-compose -f stack-billing.yml up --scale  billingapp-front=3 -d --force-recreate
Detener todos los contenedores: docker stop $(docker ps -a -q)
Listar las redes virtuales: docker network ls
Eliminar las redes virtuales: docker network prune
Reconstruir las imagenes: docker-compose -f stack-billing.yml build --no-cache
Reconstruir los contenedores d ela orquestación: docker-compose -f stack-billing.yml up -d --force-recreate
```  

+ Al crear los volumenes veremos que en las rutas indicadas tendremos los registros de la bbdd que vamos rellenando aunque detengamos y borremos nuestro docker-compose.  

+ Podemos escalar servicios con este comando:  
`docker-compose -f stack-billing.yml up --scale billingapp-front=3 -d --force-recreated`  
> Tener cuidado que no este puesto el container name y poner un rango de puertos(80-83:80) en este caso en el front que escalamos.  

+ La otra forma de escalar seria añadir directamente en el docker-compose.yaml:  
```
#Billin app frontend service
  billingapp-front:
    build:
      context: ./angular
    deploy:
        replicas: 3
        resources:
            limits:
                cpus: "0.10"
                memory: 250M
            reservations:
                cpus: "0.1"
                memory: 120M
    #container_name: billingApp-front
    depends_on:
      - billingapp-back
    ports:
      - 80-83:80
```
> Con `docker stats` vemos las estadisticas que por ejemplo delimitamos en el fichero docker-compose.yml.  


### V3 SERVICES TEST/PROD  

+ Ahora veremos como montar de nuevo nuestra infraestructura de app, bbdd, server web y java, primero deploy en test y luego en producción:  
```
version: '3.1'

services:
#database engine service
  postgres_db_prod:
    container_name: postgres_prod
    image: postgres:latest
    restart: always
    networks:
      - env_prod      
    environment:
    ports:
      - 5432:5432
    volumes:
        #allow *.sql, *.sql.gz, or *.sh and is execute only if data directory is empty
      - ./dbfiles:/docker-entrypoint-initdb.d
      - /var/lib/postgres_data_prod:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: qwerty
      POSTGRES_DB: postgres  
#database engine service
  postgres_db_prep:
    container_name: postgres_prep
    image: postgres:latest
    restart: always
    networks:     
      - env_prep
    environment:
    ports:
      - 4432:5432
    volumes:
        #allow *.sql, *.sql.gz, or *.sh and is execute only if data directory is empty
      - ./dbfiles:/docker-entrypoint-initdb.d
      - /var/lib/postgres_data_prep:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: qwerty
      POSTGRES_DB: postgres    
#database admin service
#Use for All enviroments
  adminer:
    container_name: adminer
    image: adminer
    restart: always
    networks:
      - env_prod
      - env_prep
    depends_on: 
      - postgres_db_prod
      - postgres_db_prep   
    ports:
       - 9090:8080

#ENV_PROD
#Billin app backend service
  billingapp-back-prod:
    build:
      context: ./java
      args:
        - JAR_FILE=billing-0.0.3-SNAPSHOT.jar
    networks:
      - env_prod     
    container_name: billingApp-back-prod     
    environment:
       - JAVA_OPTS=
         -Xms256M 
         -Xmx256M         
    depends_on:     
      - postgres_db_prod
    ports:
      - 8080:8080 
#Billin app frontend service
  billingapp-front_prod:
    build:
      context: ./angular 
    networks:
      - env_prod      
    deploy:
        replicas: 2
        resources:
           limits: 
              cpus: "0.15"
              memory: 250M
#recusos dedicados, mantiene los recursos disponibles del host para el contenedor
           reservations:
              cpus: 0.1
              memory: 128M
    #container_name: billingApp-front
    depends_on:     
      - billingapp-back-prod
#rango de puertos para escalar    
    ports:
      - 8081-8082:80


#ENV_PREP
#Billin app backend service
  billingapp-back-prep:
    build:
      context: ./java
      args:
        - JAR_FILE=billing-0.0.2-SNAPSHOT.jar
    networks:    
      - env_prep
    container_name: billingApp-back-prep      
    environment:
       - JAVA_OPTS=
         -Xms256M 
         -Xmx256M         
    depends_on:     
      - postgres_db_prep
    ports:
      - 7080:7080 


#Billin app frontend service
  billingapp-front-prep:
    build:
      context: ./angular 
    networks:     
      - env_prep
    deploy:
        replicas: 2
        resources:
           limits: 
              cpus: "0.15"
              memory: 250M
#recusos dedicados, mantiene los recursos disponibles del host para el contenedor
           reservations:
              cpus: 0.1
              memory: 128M
    #container_name: billingApp-front
    depends_on:     
      - billingapp-back-prep
#rango de puertos para escalar    
    ports:
      - 7081-7082:81


networks:
  env_prod:
    driver: bridge  
    #activate ipv6
    driver_opts: 
            com.docker.network.enable_ipv6: "true"
    #IP Adress Manager
    ipam: 
        driver: default
        config:
        - 
          subnet: 172.16.232.0/24
          gateway: 172.16.232.1
        - 
          subnet: "2001:3974:3979::/64"
          gateway: "2001:3974:3979::1"
  env_prep:   
    driver: bridge  
    #activate ipv6
    driver_opts: 
            com.docker.network.enable_ipv6: "true"
    #IP Adress Manager
    ipam:
        driver: default
        config:
        - 
          subnet: 172.16.235.0/24
          gateway: 172.16.235.1
        - 
          subnet: "2001:3984:3989::/64"
          gateway: "2001:3984:3989::1"
```  
> Se especificas las networks que creamos y copiamos un clon de servicio de prod y pre teniendo en cuenta cambiar el path de la bbdd para que no se conflicten.  
> `docker-compose -f stack-billing.yml build --no-cache`  
> `docker-compose -f stack-billing.yml up -d --force-recreate`  



## KUBERNETTES  

### INSTALACIÓN  

+ La arquitectura consta de un cluster donde hay nodos que trabajan(master y workers), donde dentro estan los pods. En este cluster tenemos Control manager, etcd(bbdd) y scheduler. Tenemos un Apiserver que es quien nos provee la interaccion con el cluster a traves de los comandos __kubectl__ o manera grafica con el __kubernetes dashboard__.  

+ Normalmente se usa la infraestructura de amazon, azure o google.  

+ Minikube simula la infraestrura de un cluster de kubernetes y contiene todos los componentes necesarios en un solo nodo.  

+ Vemos si nuestro Linux tiene virtualización:  
`grep -E --color 'vmx|svm' /proc/cpuinfo`  

+ Descargar kubectl y hacerlo ejecutable:  
`curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.20.0/bin/linux/amd64/kubectl && chmod +x kubectl`  

+ Crear el directorio para kubectl:  
`sudo mv ./kubectl /usr/local/bin/kubectl`

+ Verificar version:  
`kubectl version --client`

+ Descargar minnukube y hacerlo ejecutable:  
`curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube`  

+ Crear el directorio para minikube:  
`sudo mkdir -p /usr/local/bin/`  

+ Lanzar el ejecutable:  
`sudo install minikube /usr/local/bin/`  

+ Comandos para operar minikube, lo lanzamos con `minikube start`:  
```
minikube start 
minikube status
minikube stop
```  
> Por defecto cuando hacemos start se ha de indicar cual es el hipervisor a trabajar, por defecto coge docker, pero se le puede poner hyperV, virtualbox,etc.  
> Un hipervisor o monitor de máquina virtual ​ es una capa de software para realizar una virtualización de hardware que permite utilizar, al mismo tiempo, diferentes sistemas operativos en una misma computadora.  

+ Comprobamos que está encendido y el contenedor docker que se crea:  
```
[miguel@fedora Downloads]$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

[miguel@fedora Downloads]$ docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED              STATUS              PORTS                                                                                                                                  NAMES
82f6891e5f44   gcr.io/k8s-minikube/kicbase:v0.0.28   "/usr/local/bin/entr…"   About a minute ago   Up About a minute   127.0.0.1:49157->22/tcp, 127.0.0.1:49156->2376/tcp, 127.0.0.1:49155->5000/tcp, 127.0.0.1:49154->8443/tcp, 127.0.0.1:49153->32443/tcp   minikube
```  

+ Lanzar el dashboard grafico donde nos manda a una url para manejarlo:  
`minikube dashboard`  

+ Eliminar el cluster de minikube:  
`minikube delete`  

+ Inicar un nuevo cluster de minikube usando el controlador hypervisor de virtualbox:  
`minikube start --driver=virtualbox`  

### PODS  

+ Vamos a crear dentro de un cluster una app para que el cliente desde fuera pueda acceder desde el navegador. Para ello se crea un POD a través de la imagen de la app, con volumenes persistentes y este pod tiene una IP no visible. Para que sea visible desde fuera, se crea un servicio para poder acceder a el desde una petición de fuera.  

+ Creamos un pod desde nuestra imagen de un repositorio dockerhub:  
`kubectl run kbillingapp --image=sotobotero/udemy-devops:0.0.1 --port=80 80`  

+ Lo vemos con `kubetctl get pods` y `kubectl describe pod name_pod`.  

+ El pod tiene un IP no visible desde fuera y para que se pueda acceder se ha de exponer esta ip como si hicieramos un servicio:  
`kubectl expose pod kbillingapp --type=LoadBalancer --port=8080 --target-port=80`  
```
[miguel@fedora Downloads]$ kubectl get services
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kbillingapp   LoadBalancer   10.105.34.39   <pending>     8080:30424/TCP   9s
kubernetes    ClusterIP      10.96.0.1      <none>        443/TCP          25m
```  
> Al ser minikube y no tener mas nodos trabajando (vida real no pasaria) tenemos que pasar otro comando para obtener esa ip externa y acceder:  
```
[miguel@fedora Downloads]$ minikube service kbillingapp
|-----------|-------------|-------------|---------------------------|
| NAMESPACE |    NAME     | TARGET PORT |            URL            |
|-----------|-------------|-------------|---------------------------|
| default   | kbillingapp |        8080 | http://192.168.49.2:30424 |
|-----------|-------------|-------------|---------------------------|
```  

### DEPLOYS, VARIABLES Y SECRETS  

+ En este caso vamos a tener dos pods: uno con una bbdd postgres que contiene volumenes persistentes y otro el pgadmin grafico. Esto tendrá un servicio para poder acceder desde fuera de manera grafica.  

+ Para ello, vamos a crear en un [SECRET](https://kubernetes.io/docs/concepts/configuration/secret/) fichero yaml, las credenciales de entrada para postgresql para que esten encriptadas y no sean visibles, lo hacemos en base64 con el comando `echo -n "palabra" | base64` y para descodificarlo `echo "xxxx" | base64 -d`:  
```
#object that store enviroments variables that could be have sensitive data like a password
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
  labels:
    app: postgres
    #meant that we can use arbitrary key pair values
type: Opaque
data:
  POSTGRES_DB: cG9zdGdyZXM=
  POSTGRES_USER: cG9zdGdyZXM=
  POSTGRES_PASSWORD: cXdlcnR5
```  
> Fichero secret-dev.yaml  

+ Creamos otro secret para el PGADMIN grafico:  
```
#object that store enviroments variables that could be have sensitive data like a password
apiVersion: v1
kind: Secret
metadata:
  name: pgadmin-secret
  labels:
    app: postgres
    #meant that we can use arbitrary key pair values
type: Opaque
data:
  PGADMIN_DEFAULT_EMAIL: YWRtaW5AYWRtaW4uY29t
  PGADMIN_DEFAULT_PASSWORD: cXdlcnR5
  PGADMIN_PORT: ODA=
```  
> Fichero secret-pgadmin.yaml  






