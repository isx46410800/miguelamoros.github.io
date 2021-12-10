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




