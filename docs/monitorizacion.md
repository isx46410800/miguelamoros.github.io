# PANDORA FMS  

+ Pandora FMS es una de las herramientas de monitorización de sistemas más flexibles y completas del mercado. Con una única herramienta on-premise puede monitorizar todos los elementos de su negocio (dispositivos, infraestructura, aplicaciones, servicios y procesos de negocio) y obtener la información relevante que precise de ellos para gestionarla.

## INSTALACIÓN  

+ Instalamos en la web de [descargas](https://pandorafms.com/es/descargas-pandora-fms/)  

+ También podemos seguir los pasos de descarga e instalación en [github](https://github.com/pandorafms/pandorafms)  

+ Entramos con la ip + /pandora_console.  

## CONFIGURACION

- IPAM: gestor de direcciones ip tanto ipv4 como ipv6. Para utilizarlo se tiene que ir a Extensiones - IPAM y escanear la red que queramos


links

pandora
https://www.youtube.com/watch?v=ormUhtEsWdw&list=PL6mKJc2MIsOUnMGhfK3sC-VsjPtvIXkdN&index=6

guias
https://pandorafms.com/manual/es/documentation/02_installation/01_installing

https://pandorafms.com/manual/es/quickguides/general_quick_guide

https://pandorafms.com/downloads/guias_rapidas.pdf

https://pandorafms.com/manual/es/quickguides/general_quick_guide_cloud






















# ZABBIX  

+ Zabbix es un software de monitorización de código abierto para redes y aplicaciones. Ofrece monitorización en tiempo real de miles de métricas recogidas de servidores, equipos virtuales, dispositivos de red y aplicaciones web. Estas métricas pueden ayudarle a determinar el estado actual de su infraestructura TI y a detectar problemas con los componentes de hardware o software antes de que los clientes se quejen. La información útil se almacena en una base de datos para que pueda analizar datos a lo largo del tiempo y mejorar la calidad de los servicios proporcionados o planificar mejoras de su equipo.  

+ [INSTALACION PASO A PASO](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-zabbix-to-securely-monitor-remote-servers-on-ubuntu-20-04-es)  

+ Zabbix usa varias opciones para recoger métricas, incluyendo la monitorización sin agente de los servicios del usuario y de la arquitectura cliente-servidor. Para recoger métricas del servidor, utiliza un agente pequeño en el cliente monitorizado para recopilar datos y enviarlos al servidor Zabbix. Zabbix admite la comunicación cifrada entre el servidor y los clientes conectados, de forma que sus datos están protegidos mientras recorren redes inseguras.

+ El servidor Zabbix almacena sus datos en una base de datos relacional alimentada con MySQL o PostgreSQL. También puede almacenar datos históricos en bases de datos NoSQL como Elasticsearch y TimescaleDB. Zabbix ofrece una interfaz web para que pueda ver los datos y configurar los ajustes del sistema.  

+ En este tutorial, configurará Zabbix en dos equipos Ubuntu 20.04. Uno será configurado como el servidor Zabbix y el otro como un cliente que monitorizará. El servidor Zabbix usará una base de datos MySQL para registrar los datos de monitorización y utilizará Nginx para presentar la interfaz web.  

# NAGGIOS  

# GRAFANA  

+ Grafana es una herramienta de visualización y monitoreo de datos de código abierto que se integra con datos complejos de fuentes como Prometheus, InfluxDB, Graphite y ElasticSearch. Grafana le permite crear alertas, notificaciones y filtros ad-hoc para sus datos, a la vez que facilita la colaboración con los compañeros de equipo mediante características de uso compartido integradas.  

+ [INSTALACION](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-grafana-on-ubuntu-20-04-es)  

+ En este tutorial, instalará Grafana y la protegerá con un certificado SSL y un proxy inverso de Nginx. Una vez que haya configurado Grafana, tendrá la opción de configurar la autenticación del usuario a través de GitHub, lo que le permitirá organizar mejor los permisos de su equipo.  

+ Podemos instalar plugins en grafana. Un caso muy usado es instalar Telegraf que es un motor que envia datos de monitorizacion a una base de datos InfluxDB para poder mostrar los datos de esta bbdd en grafana.  

+ Se pueden crear paneles y alertas.  


# PROMETHEUS

+ [Prometheus](https://prometheus.io/) es otra herramienta de metricas y alertas en tiempo real.  

+ Se suele conectar con grafana para una mejor visualizacion y mejor grafica.  


# cADVISOR  

+ Sirve para monitoreo de estadisticas.  
+ Ejemplo en docker:  
```
sudo docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:latest
```  
> Por defecto puerto 8080  

