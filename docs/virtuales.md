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


## VAGRANT  

+ [DOCUMENTACION VAGRANT](https://www.vagrantup.com/docs) es otro entorno de maquina virtuales que nos permite desplegar de manera mas rapida diferentes maquinas virtuales y probar distintos entornos.  

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
> comando `vagrant up` para subir el fichero Vagrantfile.  
> `vagrant status` para ver el estado  
> `vagrant ssh` para conectarte a la maquina virtual por consola directamente.  
> `cat /etc/os-release` ver la versiones  
> `cat /vagrant-demo`ver cosas de las caracteristicas del nombre de la VM.  
> `sudo podman container ls` para ver el pod creado  
> `vagrant destroy` para eliminar la maquina.  




