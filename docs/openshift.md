# OPENSHIFT  

+ Ejemplo muy resumen de montaje de insfraestructura:  
![](./images/openshift.png)  

+ Minishift es una máquina virtual que te crea un cluster de un solo nodo en local para aprendizaje y desarrollo. Solo disponible en versión 3, por ahora. Es de OKD (Opensource).

+ La herramienta para openshift 4 es usar CRC, RedHat CodeReady Containers gestionada por red hat.

+ Hay otra herramienta online de Openshift Online pero ahora solo está con versión gratis de 30 dias (https://manage.openshift.com/)  

## MINISHIFT  

> Minishift solo funciona en estos momentos para versión 3 de Openshift. Por lo tanto no la vamos a usar durante el curso.  
> Sin embargo y dado que a nivel de línea de comandos es prácticamente idéntica a la versión 4, puede ser muy útil si en vuestro trabajo tenéis esta versión. Por eso me he animado a incluir unos vídeos donde indico como instalarla y usarla.  
> La diferencia radica sobre todo en la parte de la consola WEB que ha variado de forma notable con respecto a la 4.  

+ Para instalar minishift (https://www.okd.io/) ponemos /minishift y también los pasos de las diferentes releases (https://github.com/minishift/minishift/releases) y (https://github.com/minishift/minishift).  

### Instalación  

+ Instalacion virtualbox(https://computingforgeeks.com/how-to-install-virtualbox-on-fedora-linux/). Problemas virtualización (https://docs.fedoraproject.org/en-US/quick-docs/getting-started-with-virtualization/)

```
[isx46410800@miguel minishift]$ ll
total 26312
-rw-r--r--. 1 isx46410800 isx46410800    11374 Sep 26  2020 LICENSE
-rwxrwxr-x. 1 isx46410800 isx46410800 26927104 Sep 26  2020 minishift
-rw-r--r--. 1 isx46410800 isx46410800     3508 Sep 26  2020 README.adoc
[isx46410800@miguel minishift]$ ./minishift config set vm-driver virtualbox
No Minishift instance exists. New 'vm-driver' setting will be applied on next 'minishift start'
[isx46410800@miguel minishift]$ ./minishift start --vm-driver virtualbox
```  

+ Despues arrancado te da una serie de claves, ip(web grafica) y el OC que será como el comando para utilizar el OPENSHIFT CLIENT


## RH CODEREADY CONTAINERS  

### Instalación  

+ PASOS(https://www.itsimplenow.com/instalando-un-cluster-local-de-openshift-con-coderady-containers/)  

+ Esta es la versión similar a minishift pero utilizando la 4 pero instalado sobre un redhat. Utilizado mejor para 30 dias ya que luego se resetea la info.

+ Cogemos la descarga de (https://developers.redhat.com/products/codeready-containers/overview).

+ Una vez descargado antes seguimos con una serie de comandos:
```
yum install NetworkManager
yum install libvirt
```  

+ Descomprimimos el archivo, renonbramos crc al archivo, añadimos el usuario normal a trabajar a sudo. Como root en el /etc/sudoers:  
```
## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL
isx46410800 ALL=(ALL)       ALL
```

+ Despues __./crc setup__ para empezar a preparar el entorno de virtualización de crc.  

+ Despues __./crc start__ para arrancar la maquina. Nos pide un secret que está en los archivos del directorio crc bajado de la descarga, pegamos y enter. 

+ Luego podemos entrar en modo web o modo comando(copiar info en algun fichero que será como podemos entrar en modo kubeadmin y modo developer).  

+ En los directorios vemos que tenemos creados ahora un .crc(configuración y componentes de nuestro cluster) y un .kube(configuración de kubernetes). Dentro de .crc están en bin el driver de virtualización y oc que es la herramienta para gestionar todas las ordenes de nuestro cluster en openshift.

+ Para añadir en el path directamente el binario de la herramienta crc y no tener que escribir siempre ./crc, vamos al home editamos el .bashrc y ponemos __export PATH=$PATH:/home/openshift/crc__ es decir, ponemos la ruta de donde está la herramienta para poder usarlo.  
```
Copiamos el ejecutable en el $PATH de usuario:
cd crc
sudo cp crc /usr/local/bin
```

### Configuración  

+ __crc oc-env__ hace que para este entorno se pueda utilizar la herramienta oc, parecido a lo de .bashrc con lo de crc.  

+ Copiamos el __oc login -u developer -p developer https://api.crc.testing:6443__, info sacada de la instalación, y nos logueamos por comando. Si despues ponemos __crc ip__ vemos la ip de nuestra maquina virtual creada. 

+ Si luego copiamos el otro de __oc login -u kubeadmin -p xxxxxxxxx__ podemos loguearnos y usar ordenes de superadmin del cluster, como por ejemplo __oc get nodes__

+ Podemos usar __oc login__ para loguearnos de la manera que queramos. Si no nos acordamos podemos usar __crc console --credentials__ para recordar de que manera podemos entrar.  

+ Para acceder por una navegador a la consola podemos usar __crc console o crc console --url__ y nos da la dirección o entra directamente. Entramos con el usuario developer o kubeadmin para poder administrar todo lo que veremos en el curso.  

+ Una vez dentro vemos la interfaz web, a la izquierda el menu con las opciones de proyectos, pods, la red, monitoring, storage... También podemos ver la VIEW de como seria de admin y como developer y lo que puede manejar uno u otro.  

+ El __copy login command__ del menu, sirve para dar un ticket, un tocken para conectarme en modo remoto desde otro sitio.  


## OPENSHIFT ONLINE  

+ https://www.openshift.com/products/online/

+ https://cloud.redhat.com/openshift/create/local

+ Nuestra plataforma online es: https://console-openshift-console.apps.sandbox.x8i5.p1.openshiftapps.com/topology/ns/miguel-ito7-dev?view=graph  

+ Para conectarme tenemos que ir a la herramienta de ayuda -> command line tools y nos descargamos la de nuestro sistema operativo.  

+ [isx46410800@miguel openshift]$ tar xvf oc.tar 

+ [isx46410800@miguel openshift]$ ./oc 

+ Despues vamos a nuestro perfil -> copy login command y nos dará un token para conectarnos remotamente a nuestro server openshift.  

```
Your API token is
sha256~b1c3cvoqItQKQvR3AgF6hqPDiGoUP3FnIo4H5TjjBgg
Log in with this token
oc login --token=sha256~b1c3cvoqItQKQvR3AgF6hqPDiGoUP3FnIo4H5TjjBgg --server=https://api.sandbox.x8i5.p1.openshiftapps.com:6443
Use this token directly against the API
curl -H "Authorization: Bearer sha256~b1c3cvoqItQKQvR3AgF6hqPDiGoUP3FnIo4H5TjjBgg" "https://api.sandbox.x8i5.p1.openshiftapps.com:6443/apis/user.openshift.io/v1/users/~"
```

+ Copiamos el token de oc en la terminal y ya nos conectamos.  

+ Para un cloud de prueba: https://cloud.redhat.com/openshift/

## PROYECTOS 

+ oc get ns  

+ oc project nombre_proyecto  

+ oc login

+ Cuando creamos un proyecto se crea automaticamente un namespace `oc new-project nombre_proyecto`  

+ oc get projects  

+ oc get project nombre_proyecto  

+ oc get project miguel-ito7-dev -o yaml

+ oc describe project nombre_proyecto

### Crear un proyecto  

+ Por comando: `oc new-project nombre_proyecto`  

+ Por fichero:  
```
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  annotations:
    openshift.io/description: Esto es la descripcion del proyecto
    openshift.io/display-name: Ejemplo de creacion de una proyecto Openshift
    openshift.io/requester: developer
    documentacion: Ejemplo para crear un proyecto en openshift
  name: desa2
  labels:
     tipo: desa
spec:
  finalizers:
  - kubernetes
```  

+ Desplegamos el fichero .yaml con `oc apply -f proyecto.yaml`  

+ Borrar: `oc delete project nombre_proyecto`  

+ Cuando creamos un namespace se crea un proyecto nuevo `oc create namespace nombre_ns`  

+ Y si lo borramos, tambien se borra el proyecto `oc delete ns nombre_ns`  


## CREAR OBJETOS KUBERNETES  

### NOTA  

+ IMPORTANTE: Configurar permisos para poder ejecutar algunos contenedores
Hola, algunas imágenes que usaremos durante el curso  requiere privilegios de acceso como ROOT o bien necesitan ciertos permisos para acceder a volúmenes o puertos.

+ Por ejemplo postgres, redis, Apache, etc.

+ Aunque este curso no es de Administración, necesitamos dar ciertos permisos al usuario para que pueda trabajar.

+ Es necesario ejecutar el siguiente comando en cada uno de los proyectos que creemos durante el curso

+ Hay que modificar "default" por el nombre del proyecto....

`oc adm policy add-scc-to-user anyuid -z default`  

+ De esa forma podremos crear objetos y contenedores sin problemas.

### PODS  

+ Creamos un pod por comando: `oc run --generator=run-pod/v1 nginx(name_pod) --image=nginx`  

+ oc get pods / oc get pods -o wide/yaml 

+ oc describe pod name_pod  

+ oc logs name_pod  

+ oc delete pod name_pod  

+ En la consola web dentro del pod-> terminal, podemos entrar dentro del container.  

+ Creamos por manifiesto yaml:  
```
creamos un dockerfile personalizado y lo subimos a dockerhub
##Descargamos una versión concreta de UBUNTU, a través del tag
FROM ubuntu:18.04
MAINTAINER Apasoft Formacion "apasoft.formacion@gmail.com"
##Actualizamos el sistema
RUN apt-get update
##Instalamos nginx
RUN apt-get install -y nginx
##Creamos un fichero index.html en el directorio por defecto de nginx
RUN echo 'Ejemplo de POD para el curso de OPENSHIFT de Apasoft Formacion' > /usr/share/nginx/www/index.html
##Arrancamos NGINX a través de ENTRYPOINT para que no pueda ser modificado en la creación del contenedor
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
##Exponemos el Puerto 80
EXPOSE 80
```  

```
el manifiesto pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx1
  labels:
    zone: prod
    version: v1
spec:
  containers:
   - name: nginx   
     image: apasoft/nginx:v1
``` 

### DEPLOYMENT

+ Ejemplo:  
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example
  namespace: desa1
spec:
  selector:
    matchLabels:
      app: ejemplo-deploy
  replicas: 3
  template:
    metadata:
      labels:
        app: ejemplo-deploy
    spec:
      containers:
        - name: hello-openshift
          image: openshift/hello-openshift
          ports:
            - containerPort: 8080
```  

+ Deploy con oc apply -f crear_deploy.yaml.  

+ oc get deploy  

+ oc describe deploy nombre 


### SERVICIO

+ Ejemplo:  
```
apiVersion: v1
kind: Service
metadata:
  name: servicio
  labels:
     app: ejemplo-deploy
spec:
  type: NodePort
  ports:
  - port: 8080
    nodePort: 30005
    protocol: TCP
  selector:
     app: ejemplo-deploy
```  

+ oc get svc  

+ oc describe svc nombre  

+ oc get all  

+ crc ip  

+ oc expose svc_nombre  

+ oc get route  


## DESPLIEGUE APLICACIONES  

+ Esquema de un despliegue:  

![](./images/openshift2.png)  

+ Seguridad:  

```
Estimado alumno, desde la versión 4.5 de Openshift (que corresponde con CRC 1.13 o superior), el comando "new-app" que utilizamos durante parte del curso ha cambiado y ahora genera un Deployment en vez de un DeploymentConfig.
#
Dado que en este curso estudiamos sobre todo  DeploymentConfig (que es un recurso propio de Openshift, mientras que un Deployment es un recurso de Kubernetes) es necesario indicar la siguiente opción al ejecutar el comando.
        --as-deployment-config   (con 2 guiones por delante)
```  

+ Para crear una aplicación, le damos una imagen, primero buscará en local sino en las nubes o repos:  
`oc new-app nombre_imagen`  

+ Vemos que crea un imagenstream: `oc get is`. Esto es como un puntero que señala a las imagenes de verdad, como si fuese una librería a las imagenes a usar. `oc get is nombre`

+ También crea un deploymentconfig: `oc get dc` y `oc describe dc nombre`  

+ En openshift se utiliza replication controller, no replicasets como kubernetes `oc get rc`  

+ Cuando desplega el pod, primero despliega como un pod deploy como si fuese un test y luego lo completa el real.  

+ Al ponerle un label de blog podemos ver todo lo que se crea `oc get all -o name -l app=blog`:  
```
[isx46410800@miguel OC]$ oc get all -o name -l app=blog
pod/blog-1-kpq8w
replicationcontroller/blog-1
service/blog
deploymentconfig.apps.openshift.io/blog
imagestream.image.openshift.io/blog
```  

+ Podemos exponer el servicio:  
```
[isx46410800@miguel OC]$ oc expose svc blog
route.route.openshift.io/blog exposed
[isx46410800@miguel OC]$ oc get route blog
NAME      HOST/PORT                                                     PATH      SERVICES   PORT       TERMINATION   WILDCARD
blog      blog-miguel-ito7-dev.apps.sandbox.x8i5.p1.openshiftapps.com             blog       8080-tcp                 None
```  

+ Podemos escalar los deploy con `oc scale --replicas=3 dc name`  
```
[isx46410800@miguel OC]$ oc scale --replicas=3 dc blog
deploymentconfig.apps.openshift.io/blog scaled
[isx46410800@miguel OC]$ oc get pods
NAME            READY     STATUS      RESTARTS   AGE
blog-1-deploy   0/1       Completed   0          6m23s
blog-1-k4sch    1/1       Running     0          22s
blog-1-kpq8w    1/1       Running     0          6m19s
blog-1-nn9bf    1/1       Running     0          22s
[isx46410800@miguel OC]$ oc get pod -l app=blog
NAME           READY     STATUS    RESTARTS   AGE
blog-1-k4sch   1/1       Running   0          73s
blog-1-kpq8w   1/1       Running   0          7m10s
blog-1-nn9bf   1/1       Running   0          73s
```  

+ Podemos borrar todo lo realizado con `oc delete all -o name -l app=blog`  

+ Desde consola podemos crear esto en TOPOLOGY - CONTAINER IMAGE Y PONERLE LA IMAGEN - DEPLOYMENTCONFIG, ETC. para crear el servicio se va a NETWORKING - CREATE ROUTE - SE PONE SERVICIO  y ya está.

### Ejemplo wordpress  

+ Crear un proyecto  
`oc new-project wordpress`
+ Recordemos activar los permisos necesarios para poder desplegar imágenes que trabajen como ROOT o que activen determinados puertos  
`oc adm policy add-scc-to-user anyuid -z default`
+ Crear una nueva aplicación con Mysql  
`oc new-app mysql:5.7 --name=mysql1 -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_USER=usu1 -e MYSQL_PASSWORD=secret MYSQL_DATABASE=wordpress`
+ Crear una nueva aplicación de tipo Wordpress y enlazarla con la anterior
`oc new-app wordpress --name=wordpress1 -e WORDPRESS_DB_HOST=mysql1 -e WORDPRESS_DB_USER=usu1 -e WORDPRESS_DB_PASSWORD=secret -e WORDPRESS_DB_NAME=wordpress`
+ Crear un route para poder acceder a Wordpress  
`oc expose svc wordpress1`
+ Comprobamos que tenemos todos los componentes  
`oc get dc`
`oc get rc`
`oc get pod`
`oc get svc`
`oc get is`
`oc get route`
+ Luego podemos probar la aplicación a través del router  

### Maneras de construir una imagen  

![](./images/openshift3.png)  

![](./images/openshift4.png)  

+ Desde el codigo fuente de un repo:  
![](./images/openshift5.png)  

+ Lo generemos con oc new-app builder~linkrepo:  
`oc new-app (python:3.5)~https://github.com/apasofttraining/blog.git (--name nombre_queremos)`  

+ Esto te creará un imagestream un builder. un deploymentconfig y un servicio. En los pods crea uno de build, uno de deploy y el real.  

+ oc get buildconfig(bc) nombre_app  

+ oc get build

+ Por consola web desde topology - from git y ponemos el repo, detecta rapido cual es el builder, sino ponemos python.  

+ Ahora desde un dockerfile sería:  
`oc new-app --name blog3 --strategy=docker https://github.com/apasofttraining/blog.git`  
> esto hará que automaticamente construye los objetos desde el dockerfile  

+ oc logs pod_build  

+ Desde la consola web vamos a topology - from dockerfile - ponemos el repo  

+ Desde consola web también podemos crear app desde catalogo y usar las plantillas para construir la app. Las versiones ephemeral es porque los datos no se almacenan persitentemente.  

+ También desde consola web - desde YAML podemos crear app completa o componentes. Ejemplo de un replication-controller. Estos son los que gobiernan las replicas de los pods:  
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: rep-controller1
spec:
  replicas: 3
  selector:
    app: apasoft-rc
  template:
    metadata:
      name: apasoft-rc
      labels:
        app: apasoft-rc
    spec:
      containers:
      - name: apasoft-rc
        image: apasoft/blog
        ports:
        - containerPort: 80
```

+ APLICATION GROUPS: desde la consola en topology, elegimos el ejemplo de wordpress en el que hay dos apps en un proyecto. Vamos a actions - edit aplication grouping - creamos una app - ponemos nombre y nos crea como una burbuja(en la bbdd). Ahora vamos al worpress y hacemos lo mismo y le asignamos al mismo aplication groups y vemos que las dos apps estan agrupadas como si las dos dependieran d ellas. Esto en labels se ve como añade un label mas. Se puede añadir flechas(conectores) para poner graficamente que relaciona una cosa con otra.  


## VARIABLES  

+ El selector permite localizar los pods. El template define las caracteristicas de los pods.
+ Podemos asignar variables en un yaml de la siguiente manera:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: ejemplo-variables
spec:
  replicas: 2
  selector:
    app: variables
  template:
    metadata:
      labels:
        app: variables
    spec:
      containers:
       - name: variables
         image: gcr.io/google-samples/node-hello:1.0
         env:
         - name: NOMBRE
           value: "CURSO DE OPENSHIFT"
         - name: PROPIETARIO
           value: "Apasoft Training"
```  

+ Nos logamos en un pod para comprobar las variables:  
`oc rsh nombre_pod` y despues dentro `env`  

+ Podemos modificar el fichero yaml en caliente con el `oc edit nombre_dc` en este caso. Miramos que suma una revision y otro pods al ser modificados con `oc get dc y oc get pods`. Esto lo hace automaticamente porque en triggers estan de type: configchange.  

+ podemos listar las variables creadas del pod o dc con:  
`oc set env pod/nombre_pod --list`  

+ Podemos añadir variables despues de hacer el pod o el dc con:  
`oc set env dc/nombre_dc VARIABLE=valor`  

+ Podemos tambien de una variable ya creada, sobreescribir el valor:  
`oc set env dc/nombre_dc --overwrite VARIABLE=valor`  

+ Para quitar la variable:  
`oc set env dc/nombre_dc VARIABLE-`  

+ Desde consola web vamos al deploymentconfig creado y en environment podemos añadir variables o modificarlas.  

### Ejemplo con ODOO y POSTGRES  

+ Creamos un deploymentconfig de postgres con las variables que necesita:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: postgres-db
spec:
  replicas: 1
  selector:
    deploymentconfig: postgres-db
  template:
    metadata:
      labels:
        deploymentconfig: postgres-db
    spec:
      containers:
      - image: postgres:11
        imagePullPolicy: Always
        name: postgres-db
        ports:
        - containerPort: 5432
          protocol: TCP
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-db-volume
        env:
         - name: POSTGRES_PASSWORD
           value: "secret"
         - name: POSTGRES_USER
           value: "odoo"
         - name: POSTGRES_DB
           value: "postgres"
      volumes:
      - emptyDir: {}
        name: postgres-db-volume
  test: false
  triggers:
  - type: ConfigChange
```

+ Creamos el servicio postgres:  
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: postgres-db
  name: postgres-svc
spec:
  ports:
  - name: 5432-tcp
    port: 5432
    protocol: TCP
    targetPort: 5432
  selector:
    deploymentconfig: postgres-db
  type: ClusterIP
```  
> Sino sale, hacemos crc oc-env y luego lo que sale, lo ejecutamos.  

+ Ahora creamos el dc de odoo:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: odoo
spec:
  replicas: 1
  selector:
    deploymentconfig: odoo
  template:
    metadata:
      labels:
        deploymentconfig: odoo
    spec:
      containers:
      - env:
        - name: HOST
          value: postgres-svc
        - name: PASSWORD
          value: secret
        - name: USER
          value: odoo
        image: odoo
        imagePullPolicy: Always
        name: odoo
        ports:
        - containerPort: 8069
          protocol: TCP
        - containerPort: 8071
          protocol: TCP
        - containerPort: 8072
          protocol: TCP
        resources: {}
        volumeMounts:
        - mountPath: /mnt/extra-addons
          name: odoo-volume-1
        - mountPath: /var/lib/odoo
          name: odoo-volume-2
      volumes:
      - emptyDir: {}
        name: odoo-volume-1
      - emptyDir: {}
        name: odoo-volume-2
  test: false
  triggers:
  - type: ConfigChange
```

+ Creamos servicio:  
```
apiVersion: v1
kind: Service
metadata:
  name: odoo-svc
spec:
  ports:
  - name: 8069-tcp
    port: 8069
    protocol: TCP
    targetPort: 8069
  - name: 8071-tcp
    port: 8071
    protocol: TCP
    targetPort: 8071
  - name: 8072-tcp
    port: 8072
    protocol: TCP
    targetPort: 8072
  selector:
    deploymentconfig: odoo
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```

+ Luego exponemos el servicio de odoo y copiamos la url y entramos.

## CONFIGMAPS  

+ Son ficheros que contienen clave-valor de variables, para no tener que poner todas las variables en el yaml y ponerlo solo con el nombre de fichero del configmap.  

+ Creamos con `oc create configmap cf1 --from-literal=VARIABLE=VALOR --from-literal=VARIABLE=VALOR ...`  

+ `oc get cm o oc describe cm cf1`  

+ `oc get cm cf1 -o yaml`  

+ Ejemplo de como usarlo:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: ejemplo-variables
spec:
  replicas: 2
  selector:
    app: variables
  template:
    metadata:
      labels:
        app: variables
    spec:
      containers:
       - name: variables
         image: gcr.io/google-samples/node-hello:1.0
         envFrom:
         - configMapRef:
            name: cf1
```  

+ Ahora modificando el ejercicio practico de ODOO y POSTGRES, creando primero las variables en un fichero en vez de antes ponerlas from literal:  
`create configmap postrges-cm --from-env-file fichero_variables_postgres`  

```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: postgres-db
spec:
  replicas: 1
  selector:
    deploymentconfig: postgres-db
  template:
    metadata:
      labels:
        deploymentconfig: postgres-db
    spec:
      containers:
      - image: postgres:11
        imagePullPolicy: Always
        name: postgres-db
        ports:
        - containerPort: 5432
          protocol: TCP
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-db-volume
        envFrom:
         - configMapRef: 
              name: postgres-cm
      volumes:
      - emptyDir: {}
        name: postgres-db-volume
  test: false
  triggers:
  - type: ConfigChange
``` 

```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: odoo
spec:
  replicas: 1
  selector:
    deploymentconfig: odoo
  template:
    metadata:
      labels:
        deploymentconfig: odoo
    spec:
      containers:
      - env:
        - name: HOST
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: SERVIDOR
        - name: PASSWORD
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: CONTRASENA
        - name: USER
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: USUARIO
        image: odoo
        imagePullPolicy: Always
        name: odoo
        ports:
        - containerPort: 8069
          protocol: TCP
        - containerPort: 8071
          protocol: TCP
        - containerPort: 8072
          protocol: TCP
        resources: {}
        volumeMounts:
        - mountPath: /mnt/extra-addons
          name: odoo-volume-1
        - mountPath: /var/lib/odoo
          name: odoo-volume-2
      volumes:
      - emptyDir: {}
        name: odoo-volume-1
      - emptyDir: {}
        name: odoo-volume-2
  test: false
  triggers:
  - type: ConfigChange
```

+ Comprobamos lo mismo que antes y saldrá igual.  


## SECRETS  

+ Son como configmaps de clave-valor pero el valor está encriptado, no es visible directamente. 

+ Para crear un secret:  
`oc create secret generic secret-cm --from-literal=usuario=usu1 --from-literal=passowrd=secret`  

+ Vemos:  
`oc get secret` y `oc describe secret secret-cm`  

+ Para desplegarlo en un dc y usarlo seria:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: ejemplo-secrets
spec:
  replicas: 1
  selector:
    app: secret
  template:
    metadata:
      labels:
        app: secret
    spec:
      containers:
       - name: secret
         image: gcr.io/google-samples/node-hello:1.0
         envFrom:
         - secretRef:
            name: secret-cm
```  

+ Si entramos al pod con `oc rsh pod_name` cuando hacemos `env` lo vuelve a poner en claro y no encriptado.  

+ Ahora seguimos con el ejemplo practico. Creamos un secret por yaml:  
```
apiVersion: v1
kind: Secret
metadata:
  name: secreto1
type: Opaque
data:
  PASSWORD: c2VjcmV0Cg==
```
> password con `echo secret | base64`  

+ Y ahora en el dc de odoo:  
```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: odoo
spec:
  replicas: 1
  selector:
    deploymentconfig: odoo
  template:
    metadata:
      labels:
        deploymentconfig: odoo
    spec:
      containers:
      - env:
        - name: HOST
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: SERVIDOR
        - name: PASSWORD
          valueFrom:
           secretKeyRef:
             name: secreto1
             key: PASSWORD
        - name: USER
          valueFrom:
           configMapKeyRef:
             name: odoo-cm
             key: USUARIO
        image: odoo
        imagePullPolicy: Always
        name: odoo
        ports:
        - containerPort: 8069
          protocol: TCP
        - containerPort: 8071
          protocol: TCP
        - containerPort: 8072
          protocol: TCP
        resources: {}
        volumeMounts:
        - mountPath: /mnt/extra-addons
          name: odoo-volume-1
        - mountPath: /var/lib/odoo
          name: odoo-volume-2
      volumes:
      - emptyDir: {}
        name: odoo-volume-1
      - emptyDir: {}
        name: odoo-volume-2
  test: false
  triggers:
  - type: ConfigChange
```

+ En CONSOLA WEB vamos al apartado confimaps o secrets y damos a crear. Tambien se puede crear añadiendo un workload ya existente.  

## IMAGESTREAMS  

+ Forma de crearlos:  
![](./images/openshift6.png)  
![](./images/openshift7.png)  

+ Se crea:  
`oc new-app wordpress(imagen) --name=w1` (en este caso no hay fase de build al ser de un repo)  

```
NOTA ERROR: 
c import-image test:latest --from=docker-registry.default.svc:5000/devproject/test:v2
  ! error: Import failed (InternalError): Internal error occurred: Get https://docker-registry.default.svc:5000/v2/: x509: certificate signed by unknown authority
======================================================================================
Es debido a que a partir de la versión 3.10, OpenShift no registra la CA en el trusted store de CA del sistema operativo, por lo que la API maestra no puede acceder al registro interno de docker para importar las imágenes.
Desgraciadamente, no es posible solucionarlo en CRC debido a que no podemos acceder por SSH al servidor
Si estamos en un cluster normal de Openshift, para habilitar este acceso, hay que acceder por ssh al maestro y registrar /etc/origin/master/ca.crt en el almacén de confianza de CA del sistema operativo.
Los pasos para solucionarlo son:
- Acceder al servidor master de openshift
- Añadir lo siguiente  /etc/origin/node/pods/apiserver.yaml
============================================
- mountPath: /etc/pki
      name: certs
- hostPath:
      path: /etc/pki
    name: certs
==============================================
- Ejecutar lo siguiente
oc patch dc docker-registry -p '{"spec":{"template":{"spec":{"containers":[{"name":"registry","volumeMounts":[{"mountPath":"/etc/pki","name":"certs"}]}],"volumes":[{"hostPath":{"path":"/etc/pki","type":"Directory"},"name":"certs"}]}}}}'
- y lo siguiente
oc adm policy add-scc-to-user hostaccess -z registry
```  

+ A través de una imagen externa. Con este comando nos indica de que tipo de imagen tratamos:  
`oc import-image cassandra:latest --from="docker.io/cassandra:latest" (--confirm)`  
> Luego podremos ya con oc new-app cassandra:latest --name=c1 y tardará porque ahora es cuando se descarga la imagen a la que se apuntaba  

+ A través de una imagen interna que la descargamos:  
`oc import-image mariadb:10.2 --confirm`  
> Luego podremos ya con oc new-app mariadb:10.2 --name=maria1 -e MYSQL_ROOT_PASSWORD=secret 

+ Para etiquetas imagenes usamos `oc tad`. Descargamos oc import-image odoo:13.  
`oc tag oddo:13 oddo:latest` y con `oc get is` vemos que tiene dos tags de una misma imagen. Si ahora ponemos `oc tag docker.io/oddo:12 oddo:12` tenemos otro tag pero otra imagen. Luego podemos construir una app como queramos `oc new-app odoo:12 --name=oddoapp`  

+ Crear un imagestream desde YAML:  
```
apiVersion: v1
kind: ImageStream
metadata:
  name: mi-web
spec:
  tags:
  - name: "1.0"
    from:
      kind: DockerImage
      name: apasoft/web
```  

+ Podemos actualizar cambios directamente en las imagenes poniendo `oc import-image apasoft/ejemplo_docker --scheduled=true --confirm`  

+ En las propiedades de `imagePullPolicy`. Always nos permite hacer un pull, If not present, se puede si aun no se ha traido la imagen, Never nunca se pueda hacer pull.

## BUILDS  

![](./images/openshift8.png)  

+ Podemos poner una imagen desde catalog en consola y un imagen to source como por ejemplo de node y ver las caracteristicas del build o build config que crea.  

+ Se puede crear solo la parte de build con `oc new-build nodejs~https://github.com/sclorg/nodejs-ex.git --name=node1`. Solo crea el build y el is, no crea un DC con pod.  

+ `oc get builds` // `oc get bc`  

+ Se puede arrancar, parar otro build de un build con `oc start-build bc nombre_bc` o `oc start-build nombre_build`. Tambien `oc cancel-build nombre_build` o `oc delete bc nombre build`.  

+ Podemos crear un BUILD INLINE:  
```
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: docker-input
spec:
  failedBuildsHistoryLimit: 5
  nodeSelector: null
  output:
    to:
      kind: ImageStreamTag
      name: docker-input:latest
  postCommit: {}
  resources: {}
  runPolicy: Serial
  source:
    dockerfile: "FROM centos:7\nCMD echo 'Hola, estas probando un dockerfile inline' && exec sleep infinity" 
    type: dockerfile
  strategy:
    dockerStrategy:
    type: Docker
  successfulBuildsHistoryLimit: 5
  triggers:
  - imageChange:
    type: ImageChange
  - type: ConfigChange
```  
> Despues hacemos un `oc create imagestream nombre_bc` y luego ya podemos hacer un start-build y un new-app si queremos.  

+ Creamos uno desde un fichero externo:  
```
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: binary
spec:
  failedBuildsHistoryLimit: 5
  nodeSelector: null
  output:
    to:
      kind: ImageStreamTag
      name: binary:latest
  postCommit: {}
  resources: {}
  runPolicy: Serial 
  source:
    git:
      uri: https://github.com/ApasoftTraining/ejemplophp.git
    type: Git
  strategy:
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: php:latest
        namespace: openshift
    type: Source
  successfulBuildsHistoryLimit: 5
  triggers:
  - imageChange:
    type: ImageChange
  - type: ConfigChange
```  

```
apiVersion: v1
kind: ImageStream
metadata:
  labels:
    application: ejemplo-binary
    version: "2"
  name: binary
```  
> Luego vemos que se nos crea automaticamente todo por los triggers(triggers: - imageChange: type: ImageChange- type: ConfigChange) y ya podemos usarlo para un new-app.  

+ Un ejemplo de TRIGGERS:  
```
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: pythonapp
spec:
  failedBuildsHistoryLimit: 5
  nodeSelector: null
  output:
    to:
      kind: ImageStreamTag
      name: pythonapp:latest
  postCommit: {}
  resources: {}
  runPolicy: Serial
  source:
    git:
      uri: https://github.com/ApasoftTraining/python-app 
    type: Git
  strategy:
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: mi-imagen:v1
    type: Source
  successfulBuildsHistoryLimit: 5
  triggers:
  - generic:
      secretReference:
        name: pythonapp-generic-webhook-secret
    type: Generic
  - github:
      secretReference:
        name: pythonapp-github-webhook-secret
    type: GitHub
  - imageChange:
    type: ImageChange
  - type: ConfigChange
```  

```
apiVersion: v1
kind: ImageStream
metadata:
  name: mi-imagen
spec:
  tags:
  - name: "v1"
    from:
      kind: DockerImage
      name:  openshift/python-33-centos7
```
> auto lo hace por el tipo de triggers y sino creamos un `oc create imagestream pythonapp` y lo hace.


## DEPLOYMENTCONFIG Y DEPLOY  

![](./images/openshift9.png)  

+ Partimos de un ejemplo PERP creado por catalog en consola:  
```
kind: DeploymentConfig
apiVersion: apps.openshift.io/v1
metadata:
  annotations:
    app.openshift.io/vcs-ref: ''
    app.openshift.io/vcs-uri: 'https://github.com/sclorg/dancer-ex.git'
    openshift.io/generated-by: OpenShiftWebConsole
  resourceVersion: '244792607'
  name: perl
  uid: 4c5c65cb-0d4e-4f12-ba24-fd1b159c6bc5
  creationTimestamp: '2021-07-26T11:50:22Z'
  generation: 1
  managedFields:
    - manager: Mozilla
      operation: Update
      apiVersion: apps.openshift.io/v1
      time: '2021-07-26T11:50:22Z'
      fieldsType: FieldsV1
      fieldsV1:
        'f:metadata':
          'f:annotations':
            .: {}
            'f:app.openshift.io/vcs-ref': {}
            'f:app.openshift.io/vcs-uri': {}
            'f:openshift.io/generated-by': {}
          'f:labels':
            .: {}
            'f:app': {}
            'f:app.kubernetes.io/component': {}
            'f:app.kubernetes.io/instance': {}
            'f:app.kubernetes.io/name': {}
            'f:app.kubernetes.io/part-of': {}
            'f:app.openshift.io/runtime': {}
            'f:app.openshift.io/runtime-version': {}
        'f:spec':
          'f:replicas': {}
          'f:selector':
            .: {}
            'f:app': {}
            'f:deploymentconfig': {}
          'f:strategy':
            'f:activeDeadlineSeconds': {}
            'f:rollingParams':
              .: {}
              'f:intervalSeconds': {}
              'f:maxSurge': {}
              'f:maxUnavailable': {}
              'f:timeoutSeconds': {}
              'f:updatePeriodSeconds': {}
            'f:type': {}
          'f:template':
            .: {}
            'f:metadata':
              .: {}
              'f:creationTimestamp': {}
              'f:labels':
                .: {}
                'f:app': {}
                'f:deploymentconfig': {}
            'f:spec':
              .: {}
              'f:containers':
                .: {}
                'k:{"name":"perl"}':
                  .: {}
                  'f:image': {}
                  'f:imagePullPolicy': {}
                  'f:name': {}
                  'f:ports':
                    .: {}
                    'k:{"containerPort":8080,"protocol":"TCP"}':
                      .: {}
                      'f:containerPort': {}
                      'f:protocol': {}
                  'f:resources': {}
                  'f:terminationMessagePath': {}
                  'f:terminationMessagePolicy': {}
              'f:dnsPolicy': {}
              'f:restartPolicy': {}
              'f:schedulerName': {}
              'f:securityContext': {}
              'f:terminationGracePeriodSeconds': {}
          'f:triggers': {}
    - manager: openshift-controller-manager
      operation: Update
      apiVersion: apps.openshift.io/v1
      time: '2021-07-26T11:50:22Z'
      fieldsType: FieldsV1
      fieldsV1:
        'f:status':
          'f:conditions':
            .: {}
            'k:{"type":"Available"}':
              .: {}
              'f:lastTransitionTime': {}
              'f:lastUpdateTime': {}
              'f:message': {}
              'f:status': {}
              'f:type': {}
          'f:observedGeneration': {}
  namespace: miguel-ito7-dev
  labels:
    app: perl
    app.kubernetes.io/component: perl
    app.kubernetes.io/instance: perl
    app.kubernetes.io/name: perl
    app.kubernetes.io/part-of: perl
    app.openshift.io/runtime: perl
    app.openshift.io/runtime-version: 5.30-el7
spec:
  strategy:
    type: Rolling
    rollingParams:
      updatePeriodSeconds: 1
      intervalSeconds: 1
      timeoutSeconds: 600
      maxUnavailable: 25%
      maxSurge: 25%
    resources: {}
    activeDeadlineSeconds: 21600
  triggers:
    - type: ImageChange
      imageChangeParams:
        automatic: true
        containerNames:
          - perl
        from:
          kind: ImageStreamTag
          namespace: miguel-ito7-dev
          name: 'perl:latest'
    - type: ConfigChange
  replicas: 1
  revisionHistoryLimit: 10
  test: false
  selector:
    app: perl
    deploymentconfig: perl
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: perl
        deploymentconfig: perl
    spec:
      containers:
        - name: perl
          image: 'perl:latest'
          ports:
            - containerPort: 8080
              protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: Always
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
status:
  latestVersion: 0
  observedGeneration: 1
  replicas: 0
  updatedReplicas: 0
  availableReplicas: 0
  unavailableReplicas: 0
  conditions:
    - type: Available
      status: 'False'
      lastUpdateTime: '2021-07-26T11:50:22Z'
      lastTransitionTime: '2021-07-26T11:50:22Z'
      message: Deployment config does not have minimum availability.
```  

+ Con el `oc rollout latest dc/php1`. Hace volver al dc ultimo registrado.  

+ Con el `oc rollout status dc/php1` vemos el estado del rollout  

+ Con el `oc rollout history dc/php1` vemos el historial del rollout y con el `oc rollout history dc/php1 --revision=2` vemos el estado de uno en concreto.

+ Con el `oc rollout undo dc/php1 --to-revision=2` volvemos al estado del 2

+ Type rolling para poder hacer cambios, escalar etc. Si cambiamos el type por recrate, termina todo, crea uno nuevo en modo deploy y si funciona, despliega todo de golpe.

## PLANTILLAS  

+ Partimos de una plantilla:
```
apiVersion: v1
kind: Template
metadata:
  name: redis-plantilla
  annotations:
    description: "Description"
    iconClass: "icon-redis"
    tags: "database,nosql"
objects:
- apiVersion: v1
  kind: Pod
  metadata:
    name: ${SERVIDOR}
  spec:
    containers:
    - env:
      - name: REDIS_PASSWORD
        value: ${REDIS_PASSWORD}
      image: redis
      name: ${NOMBRE_CONTENEDOR}
      ports:
      - containerPort: 6379
        protocol: TCP
parameters:
- description: Password used for Redis authentication
  from: '[A-Z0-9]{8}'
  generate: expression
  name: REDIS_PASSWORD
- name: SERVIDOR
  from: 'servidor[a-z0-9]{5}'
  generate: expression
- name: NOMBRE_CONTENEDOR
  value: redis-mio
labels:
  redis: master
```
> En los parameters especificamos las variables a utilizar en una plantilla personalizada.  

+ Lo vemos con `oc get templates` y `oc describe templates nombre_template`  

+ El comando `oc process nombre_plantilla -o yaml` te dice lo que generaría de los objetos que hace la plantilla.

+ Para pasar parámetros a una plantilla: `oc process nombre-plantilla --parameters` nos indica que parametros hemos pasado a esa plantilla. Si le quiero añadir `oc process nombre-plantilla -p SERVIDOR=servidor2 | oc apply -f -` y le pasamos a la plantilla lo nuevo. Si le pasamos parametros a través de un fichero con los parametros se usa `oc process nombre-plantilla --param-file=file.txt`  

+ Con este ejemplo de plantilla vemos como construye una imagen a traves de un buildconfig:  
```
kind: Template
apiVersion: v1
metadata:
  name: plantilla-build
  annotations:
        description: Plantilla con BuildConfig para aplicacion PHP
objects:
  - kind: BuildConfig
    apiVersion: v1
    metadata:
      name: "${APLICACION}"
      annotations:
        description: Ejemplo de plantilla con Buildconfig
    spec:
      output:
        to:
          kind: ImageStreamTag
          name: '${APLICACION}:latest'
      source:
        type: Git
        git:
          uri: "${SOURCE_REPOSITORY_URL}" 
      strategy:
         sourceStrategy:
          from:
             kind: ImageStreamTag
             name: php:7.2
             namespace: openshift
         type: Source
      triggers:
        - imageChange: {}
          type: ImageChange
  - kind: ImageStream
    apiVersion: v1
    metadata:
      name: '${APLICACION}'
    spec:
      dockerImageRepository: ''
      tags:
        - name: latest
parameters:
  - name: SOURCE_REPOSITORY_URL 
    displayName: Repositorio GIT de la aplicacion
    description: Donde se encuentra la aplicacion 
    value: https://github.com/ApasoftTraining/cakephp-ex 
    required: true 
  - description: Nombre aplicacion.
    name: APLICACION
    value: aplicacion
message: "... Aplicacion ${APLICACION} creada desde una plantilla."
```  
> ahora podemos crear una nueva apllicacion con `oc new-app aplicacion --name=app1`

+ Las plantillas de dentro de openshift predefinidas se miran en `oc get templates -n openshift`. Y podemos crear una app con `oc new-app --template=nombre_plantilla_predefinida --name=app_predefinida`. Tambien podemos coger el fichero de una y luego modificarlo al gusto `oc get templates nombre_plantilla -n openshift -o yaml > plantilla_custom.yaml`

+ Tambien podemos crear DC desde consola y luego guardar los ficheros en consola y crear una `oc get bc,is,dc,service,route -o yaml > plantilla_web.yaml`  


## ALMACENAMIENTO 

+ Hay volumenes con datos persistentes y no.  

+ Ejemplo de un volumen donde se crean dentros los directorios de montajes que se crean dentro del contenedor y su nombre, y fuera donde esta la info:

```
apiVersion: v1
kind: Pod
metadata:
  name: volumenes
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: /desarrollo
      name: desarrollo
    - mountPath: /git
      name: git
      readOnly: true
    - mountPath: /temp
      name: temp
  volumes:
  - name: desarrollo
    hostPath:
      path: /home/openshift/datos
  - name: git
    gitRepo:
      repository: https://github.com/apasoftTraining/cursoopenshift.git
  - name: temp
    emptyDir: {}
```  

+ `oc get pv` donde vemos los physical volumen creados. Bound significa que estan ligados a un PVClaim. `oc describe pv pv_name` donde el source nos indica a qué está asociado.  

+ Ejemplo de hacer un postgres con un pv y un asociado pvc:  
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  storageClassName: sc-ficheros
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data/pv-volumen"
```  
> el storageclassname es una clase de pv que se crea cuando es para varias cosas en general. Si se crea aqui es solo interno y no sale como clase como objeto general, solo está nivel d este pv. 

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-claim
spec:
  storageClassName: sc-ficheros
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

```
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: postgres-db
spec:
  replicas: 1
  selector:
    deploymentconfig: postgres-db
  template:
    metadata:
      labels:
        deploymentconfig: postgres-db
    spec:
      containers:
      - image: postgres:11
        imagePullPolicy: Always
        name: postgres-db
        ports:
        - containerPort: 5432
          protocol: TCP
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-db-volume
        env:
         - name: POSTGRES_PASSWORD
           value: "secret"
         - name: POSTGRES_USER
           value: "odoo"
         - name: POSTGRES_DB
           value: "postgres"
      volumes:
      - name: postgres-db-volume
        persistentVolumeClaim:
        claimName: pvc-claim 
  test: false
  triggers:
  - type: ConfigChange
``` 

+ Para un class storage generico. Creamos un proyecto, luego un storage class y ponemos el tipo de que sera en el 'provisioner'. Ahora creamos dos PV con el nombre y tamaño que queramos, asociados a este storage. Ahora creamos un PVC asociado al storage, el tamaño. Hace al hacer el apply del yaml veremos como el PVC clama, bound a unos de los PV que mas ajuste al estar asociados.