lime y volability herramientas forense linux
pfsense para controlar los firewalls y hacer de enrutador en una pyme.


Entornos de pruebas virtuales con Docker + Jenkins + Selenoid
 
DOCKER
Tras instalarlo descargando el binario o a través de repositorio vía Linux, podemos comenzar descargando la última release estable de Jenkins y lanzando un contenedor:

$ sudo docker pull jenkins/jenkins:lts
$ sudo docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
detallando el comando:

-p : : puerto, se indica que el contenedor tendrá expuesto su puerto 8080 (derecha) y se enlazará con el puerto 8080 (izquierda). Es decir, sabiendo que vamos a levantar la aplicación Jenkins dentro del contenedor,

-v :: volumen, se indica que para el contenedor que estamos levantando, vamos a realizar la persistencia de la ruta indicada mediante el volumen cuyo nombre indicamos precedido de los dos puntos (:). Por tanto, todo lo que este contenedor albergue dentro de la ruta “/var/jenkins_home”, será almacenado en el volumen de docker con nombre “jenkins_home“.

Para consultar la lista de volúmenes que tenemos activos podemos realizarlo mediante el comando:

$ sudo docker volume ls
jenkins/jenkins:lts : Nombre de la imagen de la que queremos construir un contenedor.

Tras ejecutar el comando, se construirá un contenedor de Jenkins y veremos el log de la ejecución en todo momento. Habiendo realizado esta acción desde un terminal, éste se quedará mostrando el log de todo lo que sucede en el contenedor. En el momento en el que apaguemos dicho contenedor, el terminal cerrará la tarea y nos devolverá al prompt.

Durante la construcción de Jenkins se mostrará una clave codificada que habremos de utilizar para identificarnos en Jenkins y comenzar a instalar plugins y preparar el servicio.

Entre otras cosas podemos crear el usuario administrador (recomendable si no queremos tener que escribir esa contraseña cifrada cada vez que queramos acceder).

Podemos acceder a Jenkins introduciendo en el navegador la dirección “http://localhost:8080“

A partir de este momento, tras introducir la clave cifrada y configurar el usuario, podemos trabajar con Jenkins.

 
JENKINS
Se trata de un servidor de integración continúa. No sólo eso, también nos permite llevar a cabo distintas tareas de manera automatizada.

Estas labores pueden llevarse a cabo de manera “simple” mediante un proyecto de tipo “Freestyle” o multiproyecto, o lo que, durante los últimos años ha cogido más fuerza, pipelines.

El objetivo de este curso trata de generar un entorno simple de pruebas para que, a partir de este conocimiento, podáis construir en base a algo más de investigación, proyectos más robustos y complejos. Por lo que crearemos un proyecto de tipo Freestyle.

Este tipo de proyectos se puede asemejar a un “Lego”. En base a las piezas que podemos ir seleccionando (los distintos plugins que Jenkins incorpora), podemos importar código desde un repositorio; lanzar un proyecto maven con ciertos parámetros; enviar un correo electrónico con los resultados al finalizar la ejecución… Todo ello de manera simple, introduciendo los parámetros pertinentes de acuerdo a la tarea que se desea llevar a cabo.

 
SELENOID
Escrito en Golang, nos permite desplegar un sistema de contenedores con Selenium en su interior. En sí mismo es un sistema donde podemos ejecutar nuestros proyectos de manera simple. Sólo indicando las características del navegador que deseamos lanzar y enrutando correctamenta la dirección de ejecución, podemos ver a tiempo real como se ejecutan las instancias de los navegadores con nuestras pruebas. Esto incluye grabación de vídeo según lo configuremos.

Para ello, debemos partir de un fichero de configuración.

En este caso implementaremos el siguiente fichero:

browsers.json
{
    "chrome": {
        "default": "89.0",
        "versions": {
            "89.0": {
                "image": "selenoid/vnc:chrome_89.0",
                "port": "4444",
                "path": "/"
            }
        }
    }
}
En su contenido estamos indicando a Selenoid que debe integrar un controlador (y navegador) de Chrome, versión 89. Selenoid descargar el contenedor indicado desde DockerHub y lo pondrá en marcha cuando sea oportuno. Como se puede ver, la imagen correspondiente a este navegador es:

selenoid/vnc:chrome_89.0

 
DOCKER-COMPOSE
Para poder trabajar con múltiples contenedores de manera simultánea, lo más cómodo es utilizar docker-compose.

Esta herramienta nos permite definir un esquema de contenedores describiéndolos en un fichero con formato YAML.

Existen varios “versionados” de ficheros docker-compose.yml , en este caso vamos a usar la 3.3.

Para más información podéis consultar la documentación oficial de docker:

https://docs.docker.com/compose/compose-file/compose-versioning/

A continuación, el fichero que nosotros utilizaremos durante el taller:

docker-compose.yml
version: "3.3"

volumes:
        jenkins_home:
                external:
                        name: jenkins_home  
services:
        jenkins:
                build: ./jenkins_dockerfile/
                network_mode: bridge
                links:
                  - selenoid
                volumes:
                  - "jenkins_home:/var/jenkins_home"
                ports: ["9000:8080","50000:50000"]
        selenoid:
                image: "aerokube/selenoid:latest-release"
                network_mode: bridge
                volumes:
                  - "/var/run/docker.sock:/var/run/docker.sock"
                  - "./config/:/etc/selenoid/:ro"
                ports: ["4444:4444"]
        selenoid-ui:
                image: "aerokube/selenoid-ui"
                network_mode: bridge
                links:
                  - selenoid
                ports: ["8080:8080"]
                command: ["--selenoid-uri", "http://selenoid:4444"]
Explicación:

Muy importante mantener el indentado correctamente.

En los campos raíz (sin indentar): “volumes“ y “services”, se señala a qué corresponde el árbol de datos que contienen cada uno.

En primer lugar encontramos “volumes”, donde se indica el nombre del volumen de persistencia de datos que vamos a utilizar junto a los contenedores descritos más abajo. En este caso: jenkins_home

En segundo lugar encontramos “services”, esta sección hacer referencia a los contenedores que vamos a desplegar al ejecutar este fichero de docker-compose.

En este caso, serán “jenkins“, “selenoid“ y “selenoid-ui”.

Los nombres son irrelevantes. Sólo sirven para detectarse unos a otros (de ahí las referencias que podemos ver en los campos “links“ y “command“. Donde, en este último, se utiliza el nombre para hacer referencia el dominio en la URL, sin necesidad de conocer la IP que dispondrá el contenedor a la hora de ser desplegado.

El apartado “image“ hace referencia a la imagen que tomará el contenedor como base para ser desplegado. Aunque esto es obvio.

El campo “network_mode“ indica la red que va a utilizar el contenedor en cuestión. En docker podemos crear redes de comunicación entre los distintos contenedores. Tantas como necesitemos, y hacer que los contenedores sean visibles unos entre o otros (o no). Para el objetivo de este taller no necesitamos crear ningún tipo de red. Indicando el modo “bridge“ hacemos referencia a que todos van a estar dentro de la red por defecto.

En el campo “volumes“ informamos al contenedor que debe usar la persistencia de los contenedores que se indican. En el caso de jenkins se informa que debe usar el volumen “jenkins_home“ (que declaramos al inicio del documento), mientras que en selenoid indicamos las rutas que se van a compartir entre el host (nuestra máquina local) y el propio contenedor. Esto quiere decir, por ejemplo, que aquellos ficheros que situásemos en nuestra ruta “./config/“ serían visibles DENTRO del contenedor de selenoid, en la ruta “/etc/selenoid/“. Indicar también que el “:ro“ en el caso de selenoid hace referencia a que el path “./config/“ se toma como path de SÓLO LECTURA (Read Only).

Al igual vimos antes al correr el contenedor de Jenkins, “ports“ expondrá el puerto del contenedor (derecha) con el puerto local (izquierda). Es decir, si escribimos “9000:8080”, quiere decir que nuestro puerto 9000 en la máquina local, será el mismo que el 8080 del contenedor. En el caso, de Jenkins, si estamos exponiendo “9000:8080”, el ir en nuestro navegador a “http://localhost:9000“ sería exactamente lo mismo que, estando DENTRO el contenedor, ir al “http://localhost:8080“.

Finalmente, el campo “build” que podemos encontrar en el servicio de Jenkins hace que, en lugar de partir de una imagen base (como hacemos en los casos de selenoid), partimos de un fichero “Dockerfile” (literalmente, un fichero sin extensión y con nombre “Dockerfile”), desde el que se construirá el contenedor en cuestión de Jenkins.

Este último apartado lo veremos con mayor profunidad a continuación.

 
DOCKERFILE
Este fichero se utiliza para construir contenedores con algún tipo de configuración “extra” que no se dispone de serie.

En nuestro caso, el objetivo es instalar Python y su herramienta Pip para poder utilizarlas a la hora de ejecutar el proyecto.

Para ello, a continuación lo describimos:

Dockerfile

FROM jenkins/jenkins:lts
USER root
RUN apt-get update && \
    apt-get -y install python3 && \
    apt-get -y install python3-pip && \
    pip3 install --no-cache-dir selenium && \
Explicación:

Aunque es fácil de interpretar, en este caso es un fichero muy sencillo con una serie de comandos muy básica.

A la hora de construir el contenedor, a docker se le dice:

FROM (a partir de la imagen) jenkins/jenkins:lts

y

USER (utilizando el usuario) root

entonces

RUN (ejecuta los siguientes comandos)

apt-get update

apt-get -y install python3

apt-get -y install python3-pip

pip3 install --no-cache-dir selenium
Estos serán ejecutados durante la creación del contenedor.

Dada la imagen de la que partimos, cuando termine de construir el contenedor pondrá en marcha el servicio de Jenkins igual que si lo hubiéramos hecho sin el uso del Dockerfile.

 
EJECUCIÓN
.
├── config
│   └── browsers.json
├── docker-compose.yml
└── jenkins_dockerfile
    └── Dockerfile
Una vez tenemos listo todo, y todo ubicado en las carpetas correspondientes (fijaos en las rutas que se indican dentro del fichero docker-compose.yml), procedemos a levantar los contenedores mediante el comando:

$ sudo docker-compose up
Si deseasemos ignorar el log, podríamos utilizar el parámetro “-d” (detached)

y la consola volvería a estar disponible tras ejecutar el comando.

Ya teniendo en marcha los contenedores, al haber ejecutado al inicio el contenedor de Jenkins de manera independiente Y habiendo persistido los datos mediante el volumen “jenkins_home”, podremos ver que en esta ocasión jenkins ya no nos pregunta por la configuración. En su lugar ya aparece configurado y únicamente debemos ponernos manos a la obra para crear el proyecto de pruebas.

Como dijimos, utilizaremos el Freestyle como tipo de proyecto.

Una vez creado, agregaremos la opción de “importar código fuente” mediante Git, e indicaremos el repositorio donde hemos situado el archivo de pruebas (en nuestro caso un fichero python con una prueba básica de navegación).

En mi caso pedirá las credenciales por ser un repositorio privado. En el vuestro podéis usar el que mejor os venga.

Una vez indicado el repositorio, el siguiente paso será la ejecución.

Nuestro fichero es un script en python, por lo que únicamente necesitaríamos ejecutar un comando en la shell:

python3 PRUEBAselenium.py
Una vez introducido, guardamos el proyecto y, antes de ejecutarlo, abrimos una nueva ventana del navegador.

En este caso nos dirigiremos a “http://localhost:8080“, donde dispondremos de la interfaz de Selenoid, como ya vimos al principio del taller.

Una vez en el dashboard, podemos darle a “Ejecutar proyecto en Jenkins” y volver de nuevo a Selenoid para ver como se genera una nueva sesión de Chrome, en vivo. Y, finalmente, tras acabar la prueba, recibir el resultado dentro del proyecto de Jenkins.

 
COMENTARIOS FINALES
El objetivo de este taller ha sido dar una pincelada superficial a las distintas herramientas disponibles y la gran versatilidad que ofrece con ahondar sólo un poco más.

Los más experimentados sabrán que no es la forma correcta de construir una arquitectura de sistema donde ejecutar las pruebas de un proyecto, pero estarán de acuerdo conmigo en que tiene un objetivo meramente didáctico.

Ahora os toca a vosotros investigar sobre contenedores docker, acoplamiento de esclavos Jenkins sobre los que ejecutar las Build. Agregar más navegadores a Selenoid, incluso activando el sistema de grabación de vídeo que este ofrece.

Sólo espero que os haya servido de guía para empezar en todo este tema.

Un saludo y gracias por vuestra atención ;)

 
PRUEBA SELENIUM UTILIZADA DURANTE LA PRUEBA:
PRUEBAselenium.py

import unittest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

import time

class prueba_selenium(unittest.TestCase):

    def setUp(self):        
        print("me ejecuto antes de cada test")
        #self.driver = webdriver.Chrome()
        self.driver = webdriver.Remote(command_executor="http://selenoid:4444/wd/hub",
                           desired_capabilities={'browserName': 'chrome', 'browserVersion':'89.0', 'selenoid:options':{'enableVNC':True}})

    def test_a(self):        
        print("me ejecuto en cada test A")

        #GOOGLE
        self.driver.get("https://www.google.com")
        time.sleep(5)
        #aceptamos cookies
        self.driver.find_element(By.XPATH, "//div[text()='Acepto']/ancestor::button").click()
        time.sleep(5)
        #buscamos texto wikipedia                
        search_bar = self.driver.find_element(By.NAME, "q")
        time.sleep(5)
        search_bar.send_keys("wikipedia")
        time.sleep(5)
        search_bar.send_keys(Keys.ENTER)

        #BUSQUEDA DE GOOGLE
        self.driver.find_element(By.XPATH, "(//div[@id='search']/descendant::div[@class='g'])[1]/descendant::a[1]").click()

        #WIKIPEDIA
        title = self.driver.title

        self.assertEqual("Wikipe, la enciclopedia libre", title)
        pass

    def tearDown(self):
        print("me ejecuto después de cada test")
        self.driver.quit()
        pass


if __name__ == "__main__":
    unittest.main()