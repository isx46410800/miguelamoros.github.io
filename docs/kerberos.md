# KERBEROS  

+ Se conecta el user a keberos y recibe un ticket que demuestra que es quien es.

+ Cuando un user se conecta al server kerbertizado, presenta un ticket y el server lo comprueba sin tener que loguearse todo el rato.

+ Los tickets expiran

+ Demonios: /usr/sbin/krb5kdc y /usr/sbin/kadmind

+ Puertos: 88(keberos), 464(keberos passwd) y 749(keberos admin)

## COMANDOS

+ klist

+ /etc/krb5.conf

+ /var/kerberos/krb5kdc/kdc.conf

+ /var/kerberos/krb5kdc/kadm5.acl

+ kadmin.local

+ kadmin.local -q "addprinc -pq kmiguel miguel"

+ kinit user

+ kdestroy

+ kadmin

+ kadmin -p miguel

+ kdb5_util create -s -P masterkey

## KSERVER

+ Dockerfile:  

```  
# Version: 0.0.1
# @edt M11 2019-2020
# kerberos
# -------------------------------------
FROM fedora:27
LABEL author="Miguel Amoros"
LABEL description="KERBEROS server 2019-2020"
RUN dnf -y install krb5-server passwd procps vim nmap tree
RUN mkdir /opt/docker
COPY * /opt/docker/
RUN chmod +x /opt/docker/install.sh /opt/docker/startup.sh
WORKDIR /opt/docker
CMD ["/opt/docker/startup.sh"]
```

+ install.sh:  
```
#! /bin/bash
# @edt ASIX M11 2019-2020
# instal.lacio
# -------------------------------------
cp /opt/docker/krb5.conf /etc/krb5.conf
cp /opt/docker/kdc.conf /var/kerberos/krb5kdc/kdc.conf
cp /opt/docker/kadm5.acl /var/kerberos/krb5kdc/kadm5.acl
#creamos bbdd
kdb5_util create -s -P masterkey
#creamos unos users principales que desde el client podremos coger ticket (pass kpere para pere)
kadmin.local -q "addprinc -pw kpere pere"
kadmin.local -q "addprinc -pw kmarta marta"
kadmin.local -q "addprinc -pw kpau pau"
kadmin.local -q "addprinc -pw kjordi jordi"
kadmin.local -q "addprinc -pw kanna anna"
kadmin.local -q "addprinc -pw kmarta marta/admin"
kadmin.local -q "addprinc -pw kjulia julia"
kadmin.local -q "addprinc -pw kadmin admin"
kadmin.local -q "addprinc -pw kmiguel miguel"
kadmin.local -q "addprinc -pw kuser01 kuser01"
kadmin.local -q "addprinc -pw kuser02 kuser02"
kadmin.local -q "addprinc -pw kuser03 kuser03"
kadmin.local -q "addprinc -pw kuser04 kuser04"
kadmin.local -q "addprinc -pw kuser05 kuser05"
kadmin.local -q "addprinc -pw kuser06 kuser06"
kadmin.local -q "addprinc -randkey host/sshd.edt.org"
```

+ Startup.sh:  
```
#! /bin/bash
# @edt ASIX M06 2019-2020
# startup.sh
# -------------------------------------
#instalacio / preparacio
/opt/docker/install.sh && echo "Install Ok"
# activar els serveis
/usr/sbin/krb5kdc  && echo "krb5kdc Ok"
/usr/sbin/kadmind -nofork && echo "kadmind  Ok"
```

+ krb5.conf:  
```
# To opt out of the system crypto-policies configuration of krb5, remove the
# symlink at /etc/krb5.conf.d/crypto-policies which will not be recreated.
includedir /etc/krb5.conf.d/
[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log
[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = EDT.ORG
# default_ccache_name = KEYRING:persistent:%{uid}
[realms]
 EDT.ORG = {
  kdc = kserver.edt.org
  admin_server = kserver.edt.org
 }
[domain_realm]
 .edt.org = EDT.ORG
 edt.org = EDT.ORG
```  

+ kdc.conf:  
```
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88
[realms]
 EDT.ORG = {
  #master_key_type = aes256-cts
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }
```  

+ kadm5.acl:  
```
*/admin@EDT.ORG	*
superuser@EDT.ORG *
pau@EDT.ORG *
```

+ /etc/hosts:  
```
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.18.0.2	kserver.edt.org
``` 


## KHOST

+ Dockerfile:  
```
# Version: 0.0.1
# @edt M11 2019-2020
# kerberos
# -------------------------------------
FROM fedora:27
LABEL author="Miguel Amoros"
LABEL description="KERBEROS server 2019-2020"
RUN dnf -y install krb5-workstation passwd vim procps nmap tree
RUN mkdir /opt/docker
COPY * /opt/docker/
RUN chmod +x /opt/docker/install.sh /opt/docker/startup.sh
WORKDIR /opt/docker
CMD ["/opt/docker/startup.sh"]
```

+ /etc/hosts:  
```
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.18.0.2	kserver.edt.org
```

+ install.sh:  
```
#! /bin/bash
# @edt ASIX M11 2019-2020
# instal.lacio
# -------------------------------------
#fitxer de conf del client
cp /opt/docker/krb5.conf /etc/krb5.conf
```

+ startup.sh:  
```
#! /bin/bash
# @edt ASIX M06 2019-2020
# startup.sh
# -------------------------------------
#instalacio / preparacio
/opt/docker/install.sh && echo "Install Ok"
/bin/bash
```

+ krb5.conf:  
```
# To opt out of the system crypto-policies configuration of krb5, remove the
# symlink at /etc/krb5.conf.d/crypto-policies which will not be recreated.
includedir /etc/krb5.conf.d/
[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log
[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = EDT.ORG
# default_ccache_name = KEYRING:persistent:%{uid}
[realms]
 EDT.ORG = {
  kdc = kserver.edt.org
  admin_server = kserver.edt.org
 }
[domain_realm]
 .edt.org = EDT.ORG
 edt.org = EDT.ORG
```
