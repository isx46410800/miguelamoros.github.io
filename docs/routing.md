# ROUTING  

## DATOS REDES  

+ Switch: permite conectar muchos PCs en una misma red Local.  

+ Router: permite conectar a otras redes

+ Patch Panel: ayuda al montaje de los switch

+ LAN: Local Area Network, red individual o local

+ WAN: Wide Area Network, conecta dos o más LAN con TSP

+ Internet: interconexion de redes por ISP

+ Modelo TCP/IP: capa acceso a la red(MAC), capa internet(IP), capa transporte(puertos O y D - TCP/UDP) y capa de aplicación(-/HTTP/FTP)

+ ip a / ip r

+ ifconfig

+ /etc/resolv.conf // nslookup ip/web

+ netstat -putano

+ nc -l 2000 // nc ip/host 2000

+ traceroute ip/dominio

+ Entre router cables serials y entre router y pc fast ethernet.



## IPS PUBLICAS/PRIVADAS

+ Estas direcciones IP privadas son:   

`10.0.0.0 – 10.255.255.255`  

`172.16.0.0 – 172.31.255.255`  

`192.168.0.0 – 192.168.255.255`  

`169.254.0.0 – 169.254.255.255`  

+ Máscaras: 255.0.0.0 / 255.255.0.0 / 255.255.255.0  

+ Cuando una máquina con una dirección IP privada quiere conectarse a Internet, deberá sustituir esa dirección IP privada en una IP pública. Este proceso es conocido como NAT (Network Address Translation). Si tenemos una red con muchos dispositivos con direcciones IP privadas, los routers o los firewalls se encargan de hacer salir a todos esos dispositivos que lo requieran por la misma dirección IP pública (a veces puede ser un pool). Cuando el tráfico vuelve, estos son capaces de deshacer el cambio de manera que pueden mantenerse todas las comunicaciones.  

+ Cuando un dispositivo de red quiere conectarse a través de Internet normalmente tendrá un router para salir a Internet. Este router tiene una dirección IP y a esta dirección IP del router (o firewall) se le denomina «Default Gateway» (Puerta de enlace Predeterminada).  


## CALCULO DE IP DE RED, BITS DE HOSTS Y BITS DE RED  

+ Cálculo es IP / MASCARA = RED

+ Se corta el bit para la red hasta que es diferente el bit de mascara y de la ip. Se pone 1 cuando hay 1 arriba y abajo, 0 si no coindicen

+ Ejemplo:  
```
# red 10.20.192.7/19
10 . 20.110|00000.00000111
255.255.111|00000.00000000
--------------------------
10.255.11000000.00000000
10.255.192.0 / 19
---------------------------
primer host 10.255.192.1 /19    10.255.110|00000.00000001 /19
ultimo host 10.255.223.254 /19  10.255.110|11111.11111110 /19
red 10.255.192.0/19
broadcast 10.255.223.255/19
```

## SUBNETTING  

+ Numero de dispositivos por red: 2**bitsHosts - 2  
+ Numero de bits para redes: 2**x = numero de subredes  

+ Ejemplo:  
```
# red 192.168.100.32/20 se quiere 3 subredes(2**2=4)
# se necesitaran 2 bits mas de redes para hacer las subredes
192.168.0110|01 00.00100000 /20
192.168.0110|00|00.00000000 /22 >> 192.168.96.0  /22
192.168.0110|01|00.00000000 /22 >> 192.168.100.0 /22
192.168.0110|10|00.00000000 /22 >> 192.168.104.0 /22
192.168.0110|11|00.00000000 /22 >> 192.168.108.0 /22
```


## VLSM - VLANS  

+ Mascaras de subred de tamaño variable. Division de subredes con diferentes dispositivos por cada subred.  

+ A partir de una red madre, se va dividiendo seguidamente  

+ Ejemplo:  
```
# red 192.168.224.0 /20 para dispositivos de 700,200,200,50,2,2
700 >> 2**10-2
200 >> 2**8-2
200 >> 2**8-2
50  >> 2**6-1
2   >> 2**2-2
2   >> 2**2-2
--700--
192.168.1110|00 00.00000000 /20
192.168.1110 00|00.00000000 /22 >> 192.168.224.0 /22 red
192.168.1110 00|11.11111111 /22 >> 192.168.227.255 /22 broadcast
--200--
192.168.11100100.|00000000  /24 >> 192.168.228.0 /24 red
192.168.11100100.|11111111  /24 >> 192.168.228.255 /24 broadcast
--200--
192.168.11100101.|00000000  /24 >> 192.168.229.0 /24 red
192.168.11100101.|11111111  /24 >> 192.168.229.255 /24 broadcast
--50--
192.168.11100110.00|000000  /26 >> 192.168.230.0 /26 red
192.168.11100110.00|111111  /26 >> 192.168.230.63 /26 broadcast
--2--
192.168.230.010000|00       /30 >> 192.168.230.64 /30 red
192.168.230.010000|11       /30 >> 192.168.230.67 /30 broadcast
--2--
192.168.230.010001|00       /30 >> 192.168.230.68 /30 red
192.168.230.010001|11       /30 >> 192.168.230.71 /30 broadcast
```

## CONF ROUTER  

+ De una red, ponemos la .1 para el router y la .2 para el primer PC.  

+ De una red entre 2 routers, la .1 para uno la .2 para otro.

+ Para configurar la ruta de un router a otro, se indica la red y mascara de la red de destino y la ip del siguiente router por el que tiene que pasar en su entrada de este router.


```
CONFIGURACION ROUTER
**CONFIGURACION DE LAS INTERFACES**
#
//PUERTO SERIAL ENTRE ROUTERS\\
Router>enable
Router#configure terminal
Router(config)#interface serial 0/1/0 (1/0 tambien)
Router(config-if)#ip address 192.168.1.2 255.255.255.0
Router(config-if)#no shutdown
#
#
//PUERTO FAST ETHERNET ROUTERS-PCS\\
Router>enable
Router#configure terminal
Router(config)#interface fa0/0
Router(config-if)#ip address 192.168.1.2 255.255.255.0
Router(config-if)#no shutdown
#
#
##PARA MANTENER GRABADA LA INFO DEL ROUTER##
Router#copy running-config startup-config
#
#
**CONFIGURACION DHCP PARA IP PCS**
Router>enable
Router#configure terminal
Router(config)#ip dhcp excluded-address 172.16.3.1
Router(config)#ip dhcp pool xarxa1
Router(dhcp-config)#network 172.16.3.0 255.255.255.0
Router(dhcp-config)#default-router 172.16.3.1
Router#copy running-config startup-config
#
#
**VER TABLAS DE ENRUTAMIENTO DEL ROUTER**
Router#show ip route
**CREAR LAS TABLAS DE ENRUTAMIENTO**
Router(config)#ip route 172.16.3.0 255.255.255.0 172.16.2.1
(RED DE DESTINO, MASCARA DESTINO, ROUTER POR EL QUE PASAR)
#
#
**COMPROBACION CONEXIONES**
ping + ip destino, desde pc origen
**VER INTERFACES CONFIGURADAS**
router1#show interfaces fastEthernet 0/0
router1#show interfaces serial 0/0/0
```

+ Pautas:  
```
-VLSM(NUMERO HOSTS 2N-2 O SUBXARXES) Y RUTES RESUM
- CONFIGURACION DE SWITCHOS CERRANDO PUERTOS Y HABILITANDO LOS QUE SE USAN PARA PC/ROUTER
  A TRAVES DE LAS MACS CREANDO VLAN POR CADA PUERTO EN CONCRETO
- CONECTAMOS TODOS LOS CABLES SIGUIENDO EL ESQUEMA DE LOS SWITCHOS
- PONEMOS LAS IPS DE CADA INTERFAZ Y DHCP EN LOS PCS, NO SERVIDOR.
- TABLAS DE ENRUTAMIENTO ESTATICAS( TODAS LAS REDES POR ROUTER Y POR DEFECTO)
  DINAMICAS(RIP/RIP2) PARA CADA LO QUE TIENE CONECTADA A CADA EXTREMO
```

## ROUTER / PC / DHCP  

+ Ejemplo:  
```
ROUTER 7:
PC0:
Router>enable
Router#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#interface fa1/0
Router(config-if)#ip address 192.168.12.129 255.255.255.192
Router(config-if)#no shutdown
router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up
#
PC1:
Router(config)#interface fa6/0
%Invalid interface type and number
Router(config)#interface ethernet 6/0
Router(config-if)#ip address 192.168.0.1 255.255.248.0
Router(config-if)#no shutdown
Router(config-if)#
%LINK-5-CHANGED: Interface Ethernet6/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet6/0, changed state to up
#
WAN2:
Router#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.	
Router(config)#interface fa0/0
Router(config-if)#ip address 172.16.240.5 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/0, changed state to up
#
WAN3:
Router(config-if)#interface serial 2/0
Router(config-if)#ip address 172.16.240.9 255.255.255.252
Router(config-if)#no shutdown
%LINK-5-CHANGED: Interface Serial2/0, changed state to down
#
DHCP:
DEP.CF
Router(config)#ip dhcp excluded-address 192.168.12.129
Router(config)#ip dhcp pool depcf
Router(dhcp-config)#network 192.168.12.128 255.255.255.192
Router(dhcp-config)#default-router 192.168.12.129
Router(dhcp-config)#exit
Router(config)#exit
Router#
%SYS-5-CONFIG_I: Configured from console by console
Router#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
#
AULESCF
Router(config)#ip dhcp excluded-address 192.168.0.1
Router(config)#ip dhcp pool aulescf
Router(dhcp-config)#network 192.168.0.0 255.255.248.0
Router(dhcp-config)#default-router 192.168.0.1
Router(dhcp-config)#exit
Router(config)#exit
Router#
%SYS-5-CONFIG_I: Configured from console by console
Router#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
```  

