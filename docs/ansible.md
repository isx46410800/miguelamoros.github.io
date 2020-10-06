# Comandos para GIT
# ANSIBLE  
+ Es un software de gestión de la configuración automática y remota.  
+ Nos permite centralizar la configuración de numerosas servidores, dispositivos de red y Cloud Providers de una forma sencilla y automatizada.  
+ Podremos aprovisionar servidores en AWS, Azure o VMWARE y automatizar la configuración de dichos servidores.  

+ Ventajas:  
    * No requiere agentes  
    * Multiplataforma, eficiente y seguro  
    * Aprovisiona infraestructuras  
    * Configura dispositivos de red  

+ Se necesita un __Ansible Controller__ ejecutando en un SO Linux. Se puede administrar equipos Windows/Max pero el Ansible Controller debe ser LINUX.  

## Instalación  
+ `yum install ansible` __RedHat__  
+ `dnf install ansible` __Fedora__  
+ `apt-get install ansible` __Ubuntu__  
+ `pip install ansible` __Python-Pip__  
+ `brew install ansible` __MAC__  


+ `ansible --version` comprobamos la versión instalada.  

## Inventarios  
+ Ansible trabaja ejecutando tareas contra diferentes equipos remotos, dispositivos de red o APIs.  
+ Nos permiten definir dichos equipos, agruparlos y especificar valores grupales o individuales de los mismos.  
+ Formato Ansible INI, YAML o JSON.  

+ `/etc/ansible/hosts` fichero por defecto donde se define o ruta concreta `-i file`.  
+ `ansbible.cfg` fichero de configuración.  

+ EJEMPLO:  
```
[masters] # nombre general
master ansible_host=IP/FQDN/service_docker ansible_user=remote_user ansible_private_key_file=xxx.pem # nombre - maqquina a conectar - usuario a conectar - private_key
```  

+ Comprobamos la conexión: 
`ansible -i inventory -m ping all`  
`ansible -m ping -i hosts master` -m de modulo -i fichero y maquina  

```
master | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
```  

## Comando básico  
+ `ansible -i <inventory_path> -m {modulo} -a "{modulo opciones}" <nodos: all/master>`  
+ Ejemplo: `ansible -i hosts -m shell -a "echo 'hola'" all`  

+ Ejemplo: `ansible -i hosts -m shell -a "echo 'hola'" all`  
+ Ejemplo: `ansible -i hosts -m shell -a "ls -l /etc" all/masters`  
+ Ejemplo: `ansible -i hosts -b -m user -a "name=andy state=present shell=/bin/bash" all`  
> atacamos a todos los users(all) y le creamos un usuario andy. -b de superuser, con una shell concreta

## Ayuda Ansible  
`ansible-doc -l`  

+ Ejemplo de ayuda de un módulo concreto:  
`ansible-doc (-s) user`  

## Playbook  
+ Los Playbooks describen configuraciones, despliegue, y orquestación en Ansible. ​ El formato del Playbook es YAML. ​ Cada Playbook asocia un grupo de hosts a un conjunto de roles. Cada rol está representado por llamadas a lo que Ansible define como Tareas.  

+ Ejemplo:  
```
- name: Demo Install Ansible
  hosts: all
  become: yes
  tasks:
  ## instalando ansible usando apt-get
  - name: install ansible using apt
    apt:
      name: ansible
      state: present
```  

+ Ejemplo:  

```
cat play.yml 
- hosts: test1
  tasks:

    - shell: echo "Hola Mundo desde Ansible y Jenkins" > /tmp/hola-ansible.txt
```  

+ Ejemplo:  

```
- hosts: test1
  tasks:
    - debug:
       var: MSG
```  

+ Ejemplo:  

```
- hosts: test1
  tasks:
    - debug:
       var: MSG
    - debug:
       msg: "Yo no me voy a ejecutar :("
     tags: no-exec
    - debug:
       msg: "Yo sí me voy a ejecutar :)"
     tags: si-exec
```  

+ Ejemplo completo de crear un user: 

```
- hosts: master
  become: yes # ser superuser
  tasks:
  - name: create user andy
    user:
      name: andy
      state: present
      shell: /bin/bash
  - name: create user miguel
    user: name=andy state= present
```  

+ ORDEN:  
`ansible-playbook -i hosts playbook.yml --syntax`  
`ansible-playbook -i hosts playbook.yml --check (solo simula)`  

## Módulos  
+ Conocidos también task plugins o library plugins, son unidades discretas de código que se pueden utilizar desde linea de comandos o playbook.  
+ Se suelen utilizar en el nodo de destino remoto y recopila los valores de retorno. Se pueden utilizar en ad-hoc commands, playbooks y roles.  

+ Ejemplo módulo apt:  
```
- name: Demo Install Ansible
  hosts: all
  become: yes
  tasks:
  ## instalando ansible usando apt-get
  - name: install ansible using apt
    apt:
      name: ansible
      state: present
```  

+ Ejemplo módulo authorized_keys:  

```
- hosts: master
  become: yes # ser superuser
  tasks:
  - name: create user andy
    user:
      name: andy
      state: present
      shell: /bin/bash
  - name: create ssh keys
    authorized_keys:
      user: andy
      key: "{{ item }}"
      state: present
    with_file:
      - ~/.ssh/id_rsa.pub
      no_log: yes
```  

## Variables  
+ Ejemplo de variables para Ansible:  

```
- name: Demo Install Ansible
  hosts: all
  become: yes
  ## definimos las variables
  vars:
    package: ansible
    state: present
  tasks:
  ## instalando ansible usando apt-get
  - name: install ansible using apt
    apt:
      name: "{{ package }}"
      state: "{{ state }}"
```  

## Condicionales  
+ Realizar tareas segun ciertas cosas o parámetros:  
+ Ejemplo condicional:  

```
- name: Demo Install Ansible
  hosts: all
  become: yes
  ## definimos las variables
  vars:
    package: ansible
    state: present
  tasks:
  ## instalando ansible usando apt-get
  - name: install ansible using apt
    apt:
      name: "{{ package }}"
      state: "{{ state }}"
    ## indicando la condicion de solo en master
    when: "'master' in inventory_hostname"
```  

## Bucles  
+ Ejemplo de bucle:  

```
- name: Demo Install Ansible
  hosts: all
  become: yes
  tasks:
  ## instalando ansible usando apt-get
  - name: install ansible using apt
    apt:
      name: "{{ item }}"
      state: present
    ## indicando bucle de paquetes a instalar
    loop:
    - ansible
    - apache2
```  

```
- name: Demo Install Ansible
  hosts: all
  become: yes
  tasks:
  - name: create users
    user:
      name: "{{ item }}"
      state: present/absent
    ## indicando bucle de crear users
    with_items:
    - andy
    - miguel
    - mario
```  

## Roles  
+ Los roles son formas de cargar automáticamente una estructura de archivos/directorios, archivos de variables, tareas y controladores basados en una estructura de archivos conocida.  
+ Agrupar contenido por roles permite compartir los roles con otros usuarios y poder reutilizar código.  
+ Los roles esperan que los archivos esten en ciertos directorios, deben incluir al menos uno de estos.  

+ Ejemplo de role:  

```
- name: Play to demo roles
  hosts: all
  become: yes
  ## roles block
  roles:
  ## the role we want to install
  - apache ## dentro de este directorio hay muchos files, playbooks, tasks...
```  

## Ansible Galaxy  
+ Es un sitio gratuito para buscar, descargar, calificar y revisar toto tipo de roles de Ansible desarrollados por la comunidad y puede ser una excelente manera de impulsar nuestros proyectos de automatización.  
+ El cliente __ansible-galaxy__ está incluido en Ansible.  

+ Ejemplo:  
```
## ansible-galaxy
## install a role in 'roles' folder
ansible-galaxy install "ansible.docker" -p roles/

## create a role folders/files structure
ansible-galaxy init "my-role"

## search for a role
ansible-galaxy search 'docker'
```  