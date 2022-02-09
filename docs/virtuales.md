# HYPER-V  

+ [DOCUMENTACION HYPER-V](https://docs.microsoft.com/es-es/virtualization/hyper-v-on-windows/about/)  

+ [CONFIGURAR](https://www.veeam.com/blog/es/how-to-configure-hyperv-virtual-switch.html)  

+ [YOUTUBE](https://www.youtube.com/playlist?list=PLn5IkU1ZhgiaHniso3RdsF12ehcTd0jTD)  

+ Para ver si tenemos habilitado la virtualización se puede hacer desde la BIOS y en windows en Administrador de tareas - CPU y abajo ver que ponga habilitada.  

+ Añadimos el Hyper-V en panel de control - programas - activar o desactivar caracteristicas y añadimos todo lo de HyperV. Con powershell lo hacemos con `Enable-WindowsOptionalFeature -Online - FeatureName:Microsift-Hyper-V -All`.  

+ Acciones configuración de Hyper-V nos sirve para indicar en que carpeta se guardarán los discos duros y las maquinas virtuales.  

+ Crear MV: nuevo - maquina virtual y ponemos nombre, generacion, donde guardala, asignar memoria, la red, el disco duro y el SO. También en creacion rapida - nombre - iso y red. Luego te saldrán configuracion predeterminadas que se pueden cambiar en configuración.  

+ Tipos de redes:  
    - Externa: las maquinas se conectan con las maquinas y con la externa, ya que coge el adaptador fisico propio. Crea un puente
    - Interna: solo se conecta a las maquinas virtuales sin salida a internet.
    - Privada: solamente a red interna.  
> Se crean en administrador de commutadores virtuales.  

+ Tambien se pueden crear discos duros y luego se pueden asignar a maquinas en los pasos de creación de maquinas virtuales en nuevo - discos duros.  

+ Conectar dispositivos USB de fuera a la maquina virtual: cuando le damos a conectar - le damos a mostrar - recursos locales - mas y selecionamos.  

+ Habilitar HyperV o Vmware segun lo que se vaya a usar:  
    - `bcedit /set hypervisorlaunchtype off` (habilita vmware)  
    - `bcedit /set hypervisorlaunchtype auto` (habilita vmware)  

+ Cuando da error de PING entre maquinas puede ser que haya que desactivar el firewall o en las reglas de firewall en la seguridad de windows, activar el protocolo ICMP entrantes.  

+ El error de PXE que no deja el modo seguro arrancar la maquina virtual se soluciona yendo a la condiguración - seguridad y desactivar el arranque seguro.  

+ Hacer plantillas de maquinas virtuales:  
    - Hacer snapshoot o instantaneas y despues clonar desde una instantanea.
    - Dentro de una maquina, en el estado que ya queremos que se haga luego la copia, vamos a Equipo - unidad C - system32 - sysprep y ejecutamos y elegimos iniciar y habilitamos opcion y apagado. Una vez apagado, vamos a la MV , boton derecho exportar y la guardamos donde queramos. También podemos copiar el disco duro de la MV para también hacer una plantilla del disco duro y asi seleccionamos ese disco al crear la nueva MV. Luego importar maquina en acciones - selecionamos donde la otra, nueva identificador.  


+ Para poder tener HyperV dentro de una maquina virtual (virtualizacion anidada) se ha de indicar en la maquina fisica: `Set-VMProcessor -VMName DC01 -ExposeVirtualizationExtensions $true`  

+ Se puede migrar toda la maquina virtual de dentro de una maquina virtual a otro servidor de maquina virtual para meterle dentro esta maquina virtual (Shared Nothing Live Migration), se hace en caliente y en funcionamiento sin perder tiempo ni información. Vamos a la MV - boton derecho - mover - mover MV y seleccionar donde - y selecionamos el host a mover - mover todos los elementos y elegimos la carpeta de destino.  


# VMWARE  

+ [DOCUMENTACION VMWARE](https://www.profesionalreview.com/2020/06/14/vmware-maquina-virtual-guia/)  

+ [NUBE](https://docs.microsoft.com/es-es/azure/vmware-cloudsimple/quickstart-create-private-cloud-vmware-virtual-machine)  

+ [CONFIGURAR](https://docs.vmware.com/es/VMware-Fusion/12/com.vmware.fusion.using.doc/GUID-7705D72C-9D90-4363-8A05-7DC3B2CAFE80.html)  



# VIRTUALBOX  

+ [DOCUMENTACIÓN VIRTUALBOX](https://www.profesionalreview.com/2018/11/21/crear-maquina-virtual-virtualbox/)  

+ [CONFIGURAR](https://www.fpgenred.es/VirtualBox/crear_una_mquina_virtual_a_partir_del_disco_de_otra.html)  


# KVM  

+ En linux podemos usar el [virt-manager](https://vidatecno.net/como-usar-kvm-con-virtual-machine-manager/) para crear una maquina virtual.  


# VAGRANT  

+ [DOCUMENTACION VAGRANT](https://www.vagrantup.com/docs) es otro entorno de maquina virtuales que nos permite desplegar de manera mas rapida diferentes maquinas virtuales y probar distintos entornos.   

+ Instalacion:  
```
lsmod | grep kvm
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/fedora/hashicorp.repo
sudo dnf -y install vagrant
vagrant init hashicorp/bionic64
vagrant up
vagrant ssh
vagrant destroy
```  

+ El vagrantfile es el fichero que describe como seran configuradas y provisionadas las maquinas virtuales. Este fichero está escrito en Ruby. Ejemplo:  
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
 
Vagrant.configure("2") do |config|
      # Every Vagrant development environment requires a box. 
      # You can search for boxes at 
      # https://app.vagrantup.com/boxes/search
      config.vm.box = "ubuntu/focal64"
 
      # Set the HOSTNAME of the guest VM
      config.vm.hostname = "vagrant-host"
 
      # Create a private network, which allows host-only access 
      # to the machine using a specific IP
      config.vm.network "private_network", ip: "192.168.56.100"
 
      # Vagrant VirtualBox provider specific VM properties
      config.vm.provider "virtualbox" do |vb|
           # Set VM name to be displayed in the VirtualBox VM Manager window
           vb.name = "vagrant-vm"
           # Customize the amount of CPUs on the VM
           vb.cpus = 2
           # Customize the amount of memory (2GB RAM) on the VM
           vb.memory = 2048
      end
 
      # Share an additional folder to the guest VM. The first argument is
      # the path on the host to the actual folder. The second argument is
      # the path on the guest to mount the folder. And the optional third
      # argument is a set of non-required options.
      # config.vm.synced_folder "../data", "/vagrant_data"
 
      # Vagrant shell provisioner to automatically
      # install packages 'vim', 'curl', 'podman' 
      config.vm.provision "shell", inline: <<-SCRIPT
           sudo apt update
           sudo apt install -y vim curl
           . /etc/os-release
           echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
           curl -L "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key" | sudo apt-key add -
           sudo apt update
           sudo apt -y upgrade
           sudo apt install -y podman
           echo NGINX PODMAN CONTAINER RUNNING ON VIRTUALBOX VM > /vagrant-demo
           echo Provisioned at: >> /vagrant-demo
           date >> /vagrant-demo
      SCRIPT
 
      # Vagrant Podman provisioner automatically installs Podman
      # and then runs the 'nginx' image in a container
      # (We have pre-installed Podman because of provisioner bugs)
      config.vm.provision "podman" do |p|
           p.run "nginx"
      end
end
```  
> Estoy crea una maquina virtual en virtualbox con ubuntu y las caracteristicas que se especifican.  
> comando `vagrant up` para subir el fichero Vagrantfile.  
> `vagrant status` para ver el estado  
> `vagrant ssh` para conectarte a la maquina virtual por consola directamente.  
> `cat /etc/os-release` ver la versiones  
> `cat /vagrant-demo`ver cosas de las caracteristicas del nombre de la VM.  
> `sudo podman container ls` para ver el pod creado  
> `vagrant destroy` para eliminar la maquina.  


# DIGITAL OCEAN  

+ [DIGITAL OCEAN](https://www.digitalocean.com/) Es una plataforma de la nube como aws y azure que te permite crear maquinas virtuales.  

+ Un [droplet](https://marketplace.digitalocean.com/) no es más que una máquina virtual en la nube con todas las características de un servidor, totalmente escalable de acuerdo a las necesidades del negocio, es decir si tienes un droplet con un disco duro de por ejemplo 10 gb.  


# PODMAN  

+ [Podman](https://docs.podman.io/en/latest/) es como Docker y lo que se diferencia es que para interactuar docker primero pasa por el demonio de docker y luego al cli y podman es serveless.  

+ As a result of the CLI similarities, to preserve the developers experience when transitioning from Docker to Podman, simply set the following alias docker=podman. While not an exhaustive list, the basic Podman operations are enumerated below:  
```
List images available in the local cache:
$ podman image ls
Pulling an alpine image from the Docker Hub registry into the local cache:
$ podman image pull docker.io/library/alpine 
Run a container from an image (if the image is not found in local cache it will be pulled from the registry). The run command is the equivalent of podman container create followed by podman container <id> start:
$ podman container run -it alpine sh
Run a container in the background (-d option) from an nginx image from the Docker Hub registry:
$ podman container run -d docker.io/library/nginx 
List only running containers:
$ podman container ls 
List all containers: 
$ podman container ls -a
Inject a process inside a running container. This will start a bash shell in interactive (-i option) terminal (-t option) mode inside the container:
$ podman container exec -it <container_id/name> bash
Stop a running container:
$ podman container stop <container id/name> 
Delete a stopped container:
$ podman container rm  <container id/name>
```  

+ Podman network operations allow us to list networks, create custom networks, inspect them, attach containers to them, and finally remove them when no longer needed. Podman supports the creation of CNI-compliant container networks. To create container networks, Podman supports drivers for various network types. Default is the bridge driver, usable in both rootless and rooted modes. However, in rooted mode, additional two drivers can be used, the macvlan and ipvlan drivers with options such as parent to designate a host device and the mode to be set on the interface. The macvlan and ipvlan drivers are only available in rooted mode because they require access to the host network interfaces, otherwise the rootless networking needs a separate network namespace:  
```
A mix of rootless ($) and rooted (#) network operations are provided below: 
$ podman network ls
$ podman network create --subnet 192.168.20.0/24 --gateway 192.168.20.3 bridgenet
$ podman network inspect bridgenet
# podman network create -d macvlan -o parent=eth0 macvnet
# podman network inspect macvnet
```  

# PUPET  

+ [PUPPET](https://puppet.com/docs/) Es una herramienta de gestion de configuracion que usa el modelo cliente/servidor. El puppet agent tiene que estar instalado en cada maquina a la que se quiera acceder y el server solo en un LINUX.  

+ Se crea en un archivo manifiesto los [recursos](https://puppet.com/docs/puppet/7/resource_types.html) que se quieran crear:  
```
user { 'student':
  ensure     => present,
  uid        => '1001',
  gid        => '1001',
  shell      => '/bin/bash',
  home       => '/home/student'
}
```  

# CHEF  

+ [CHEF](https://www.chef.io/) Es otra herramienta de gestion cliente/servidor donde el cliente tambien tiene que ser instalado en cada maquina.  

+ Los manifiestos son llamados [cookbooks](https://docs.chef.io/cookbooks/) para instalar cosas:  
```
package "apache2" do
  action :install
end

service "apache2" do
  action [:enable, :start]
end
```  
> Son llamados recipes  

+ Knife es la herramienta de conexion entre las maquinas.  


# PACKER  

+ [PACKER](https://www.packer.io/docs) es una herramienta de software libre desarrollada por Hashicorp que nos permite crear imágenes de sistema operativo de forma automatizada y utilizando archivos de configuración para tal efecto y sirve para diferentes plataformas a la vez.

+ Una vez creada la configuración (que veremos en próximas entradas) Packer se conectará a la infraestructura de destino, creará la máquina virtual y aplicará todas las configuraciones que hayamos definido en nuestros archivos. Todo esto mientras nos tomamos un rico café.

+ Una vez hemos creado la plantilla, Packer permite crear automáticamente tantas máquinas virtuales como necesitemos. Lógicamente, cuantas más máquinas virtuales creemos a partir de una plantilla, más rentabilizaremos el tiempo empleado en la elaboración de la plantilla, pero, aún en el caso de que sólo creemos una vez la máquina virtual, la plantilla nos servirá como documentación y especificación del proceso.  

+ Packer incluye la funcionalidad necesaria para trabajar con las grandes plataformas del mercado sin la necesidad de instalar ningún plugin adicional:
    - Aws.
    - Azure.
    - DigitalOcean.
    - Docker.
    - Google Cloud Platform.
    - Hyper-V.
    - VMware.  

+ La generación automatizada de plantillas con Packer puede aportarnos muchos beneficios a nuestro flujo de trabajo:
    + Velocidad de aprovisionado: Con Packer nuestras plantillas pueden disponer siempre de las últimas versiones de sistema operativo y de aplicaciones, con lo que podemos desplegar una plantilla siempre lista para su uso en cuestión de segundos o pocos minutos.
    + Consistencia: Utilizando Packer nos aseguramos de que todas las máquinas virtuales que despleguemos partirán de la misma configuración.
    + Consistencia multiplataforma: Siguiendo con el punto anterior, con Packer podemos partir de imágenes idénticas si disponemos de entornos híbridos. En todo momento estaremos seguros que nuestra instancia de Compute Engine de GCP es identica a nuestra máquina virtual de VMware vSphere.
    + Versionado de configuración: Tener nuestras configuraciones en archivos de texto nos permite versionarlas de forma muy sencilla, por ejemplo, con Git, con lo que siempre podremos echar la vista atrás a la hora de hacer troubleshooting de nuestras máquinas.

+ Packer utiliza templates en formato JSON con los que definiremos las distintas configuraciones necesarias para el despliegue, configuración y post procesado de nuestras plantillas.  

+ [Instalacion](https://www.packer.io/downloads)  

+ Ejemplo de template:  
```
{
	"variables": {
	"aws_access_key": "YOUR_AWS_ACCESS_KEY",
	"aws_secret_key": "YOUR_AWS_SECRET_KEY"
	},
	"builders": [{
		"type": "amazon-ebs",
		"access_key": "{{user `aws_access_key`}}",
		"secret_key": "{{user `aws_secret_key`}}",
		"region": "us-east-1",
		"source_ami_filter": {
			"filters": {
				"virtualization-type": "hvm",
				"name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
				"root-device-type": "ebs"
			},
			"owners": ["099720109477"],
			"most_recent": true
		},
		"instance_type": "t2.micro",
		"ssh_username": "ubuntu",
		"ami_name": "packer-example {{timestamp}}"
	}]
}
```  

+ Ejemplo 2:  
```
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```  
> Lo construimos con `packer build template.json`  


