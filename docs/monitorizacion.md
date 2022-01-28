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

# PROMETHEUS