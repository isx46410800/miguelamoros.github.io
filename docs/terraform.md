# TERRAFORM  

+ [Tutoriales](https://learn.hashicorp.com/terraform)  

## INSTALL  

+ `sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl`  

+ `curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -`  

+ `sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"`  

+ `sudo apt-get update && sudo apt-get install terraform`  

+ `terraform -help`  
    - touch ~/.bashrc
    - terraform -install-autocomplete

+ Creamos un primer recurso de prueba en la carpeta con extensión `main.tf`  
```
provider "aws"{
    region = "us-east-1"
}
resource "aws_vpc" "myvpc"{
    cidr_block = "10.0.0.0/16"
}
```

## IAM USERS  

+ Podemos crear un [usuario](https://console.aws.amazon.com/iamv2/home?#/users) en IAM y podemos asignarle tags, roles como accessAdministrator, y nos daría los tokens para conectarse.  

+ Luego en users, en credentials podemos modificar y obtener nuevos keys o descargar el que teníamos.  

## TERRAFORM INIT  

+ Una vez tenemos el directorio donde trabajamos con por ejemplo el simple fichero de `main.tf`, hacemos un terraform init para crear como el directorio de trabajo, muy parecido al git init. Te crea el terraform provider y otros ficheros ocultos.  

+ Ejemplo main.tf:  
```
provider "aws"{
    region = "us-east-1"
}
resource "aws_vpc" "myvpc"{
    cidr_block = "10.0.0.0/16"
}
```  

## TERRAFORM PLAN  

+ Esta orden `terraform plan` sirve como para ver si estaría bien el proceso ejecutado antes de hacer un terraform aply y las cosas que se crearían con nuestro fichero de codigo.  

+ Ejemplo main.tf:  
```
provider "aws"{
    region = "us-east-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_vpc" "myvpc"{
    cidr_block = "10.0.0.0/16"
}
```   

## TERRAFORM APPLY  

+ Si estamos contentos con el terraform plan, hacemos un `terraform aply` para que se ejecute lo indicado en el fichero.  

+ Se puede usar la opción `--auto-approve` cuando ya sabemos lo que va a crear y saltar todos las lineas de creación.

+ Lo creado se crea en el apartado de [VPC](https://eu-west-2.console.aws.amazon.com/vpc/home?region=eu-west-2#vpcs:). Virtual Private Cloud


## TERRAFORM DESTROY  

+ Se puede eliminar todo lo creado manualmente o con la orden `terraform destroy`  


## VARIABLES  

+ Se crea en el fichero main.tf  

+ Variable sring:  
```
variable "mystringvar" {
    type = string
    default = "my first var"
}
```  

+ Variable número:  
```
variable "mynumvar" {
    type = number
    default = 100
}
```  

+ Variable booleana:  
```
variable "isenabled" {
    default = false
}
```  

+ Variable lista:  
```
variable "mylistvar" {
    type = list(string)
    default = ["apple","20"]
}
```  

+ Variable map:  
```
variable "mymapvar" {
    type = map
    default = {
        Key1 = "value1"
        Key2 = "value2"
    }
}
```  

+ Variable tuple:  
```
variable "mytuplevar" {
    type = tuple([string,number,string])
    default = ["var1",10,"var2"]
}
```  

+ Variable input:  
```
variable "myinputvar" {
    type = string
    description = "please enter a vpc name"
}
```  

+ Varibale objecto:  
```
variable "myobjectvar" {
    type = object({name = string, port = list(number)})
    default = {
        name = "myports"
        port = [22,80,443]
    }
}
```  

+ Output. [DOC](https://www.terraform.io/docs/language/values/outputs.html):  
```
output "myoutput" {
    value = aws_vpc.myVPC.id
}
```  

+ Recurso con alguna llamada a la variable:  
```
resource "aws_vpc" "myVPC" {
    cidr_block = "10.10.10.0/24"
    tags = {
        Name = var.mystringvar
    }
}
```  

```
resource "aws_vpc" "myVPC" {
    cidr_block = "10.10.10.0/24"
    tags = {
        Name = var.mylistvar[0]
    }
}
```  

```
resource "aws_vpc" "myVPC" {
    cidr_block = "10.10.10.0/24"
    tags = {
        Name = var.mymapvar["Key2"]
    }
}
```  

```
resource "aws_vpc" "myVPC" {
    cidr_block = "10.10.10.0/24"
    tags = {
        Name = var.myinputvar
    }
}
```  

### En ficheros  

+ Si creas una variable pero sin darle valor default te sale un input al hacer un apply:  
```
variable "subnet_prefix" {
    description = "cidr block de la subnet"
    #default
}
resouce "aws_subnet" "subnet-1" {
    vpc_id = aws_vpc.prod-vpc.id
    cidr_block = var.subnet_prefix
    availability_zone = "us-east-1a"
}
```  
> También en vez de un input, pasando por variable con `terraform apply -var "subnet_prefix=10.10.1.0/24"`

+ O en un fichero `terraform-tfvars`:  
`subnet_prefix="10.10.1.0/24"`  

+ Si lo creamos con otro nombre, por ejemplo `ejemplo.tfvars` luego lo pasamos con un `terraform apply -var-file example.tfvars`

+ O de tipo string y fichero (`subnet_prefix=["10.10.1.0/24"]`):  
```
variable "subnet_prefix" {
    description = "cidr block de la subnet"
    type = string
}
resouce "aws_subnet" "subnet-1" {
    vpc_id = aws_vpc.prod-vpc.id
    cidr_block = var.subnet_prefix
    availability_zone = "us-east-1a"
}
```  
> Y en un fichero terraform.tfvars hacer un apply normal

+ Tambien con un listado:  
`subnet_prefix=["10.10.1.0/24", "10.0.0.5/14"]`  
```
variable "subnet_prefix" {
    description = "cidr block de la subnet"
    type = string
}
resouce "aws_subnet" "subnet-1" {
    vpc_id = aws_vpc.prod-vpc.id
    cidr_block = var.subnet_prefix[1]
    availability_zone = "us-east-1a"
}
```  

## CREAR INSTANCIA  

+ [Documentacion de fichero main.tf](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). Aqui descubrimos modulos, recursos etc que se pueden indicar y crear en el fichero.

+ En el fichero main.tf:  
```
provider "aws" {
    region = "us-east-1"
    access_key = "xxxx"
    secret_key = "xxxx"
}

resource "aws_instance" "EC2" {
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
}
```
> Despues hariamos un terraform init, plan y aply.  

### EIP  

+ [EIP](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip) 

```
provider "aws" {
    region = "us-east-1"
    access_key = "xxxx"
    secret_key = "xxxx"
}

resource "aws_instance" "EC2" {
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
}

resource "aws_eip" "myEIP" {
    instance = aws_instance.EC2.id
}

ouput "myOutEIP" {
    value = aws_eip.myEIP.public_ip
}
```


### SECURITY GROUP  

+ Crear un security group:  
```
resource "aws_instance" "EC2" {
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    security_groups = [aws_security_group.mySG.name]
}
resource "aws_security_group" "mySG" {
    name = "Allow HTTPS"
    ingress {
        description = "allow https from any where"
        from_port = 443
        to_port = 443
        protocol = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }
    egress {
        description = "allow https from any where"
        from_port = 443
        to_port = 443
        protocol = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }
}
```

### USER  

+ Ejemplo de crear un usuario con ciertas directrices:  

```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_iam_user" "myname"{
    name = "test-user"
}
resource "aws_iam_policy" "myPolicy"{
    name = "test-custom-policy"
    policy = <<EOF
    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "ec2:*",
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "elasticloadbalancing:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "cloudwatch:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "autoscaling:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "autoscaling.amazonaws.com",
                        "ec2scheduled.amazonaws.com",
                        "elasticloadbalancing.amazonaws.com",
                        "spot.amazonaws.com",
                        "spotfleet.amazonaws.com",
                        "transitgateway.amazonaws.com"
                    ]
                }
            }
        }
    ]
    }
    EOF
}
resource "aws_iam_policy_attachment" "attachMyPolicy"{
    name = "attachment"
    users = [aws_iam_user.myname.name]
    policy_arn = aws_iam_policy.myPolicy.myPolicy_arn
}
```  
> la politicas las copiamos de IAM-politicas  

### RDS  

+ Crear una instancia de base de datos:  
```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_db_instance" "myRDS"{
    name = "myDB"
    instance_class = "db.t2.micro"
    allocated_storage = 5
    storage_type = "gp2"
    engine = "mysql"
    engine_version = "5.7"
    username = "test"
    password = "jupiter"
    parameter_group_name = "default.mysql5.7"
    skip_final_snapshot = true
    port = 3306
}
```


## BUCKET

+ Los buckets son depositos que se pueden crear en s3 para guardar cosas permanentes.

+ ejemplo:  
```
main.tf
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_vpc" "test"{
    cidr_block = "10.0.0.0/16"
}
backend.tf
terraform{
    backend "s3"{
        key = "terraform/tfstate.tfstate"
        bucket "el_creado_en_s3"
        egion = "us-west-1"
        access_key = "xxxxx"
        secret_key = "xxxxx"
    }
}
```  

## SCALING

+ Podemos crear varios recursos del mismo tipo con `count`:  
```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_instance" "DB"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    count = 3
}
```

## IMPORT  

+ Cuando creas un VPC puedes importar la info de lo creado en un fichero:  
```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_vpc" "myVPC"{
    cidr_block = "10.0.0.0/16"
}
```

+ Despues en la web vemos que id tenemos y hacemos en consola `terraform import aws_vpc.myVPC.idxxx`

## DEPENDS ON  

+ Cuando queremos crear algo que primero dependa de una cosa creada:  
```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_instance" "DB"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
}
resource "aws_instance" "web"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    depends_on = [aws_instance.DB]
}
```

## VALIDATE  

+ Podemos usar la orden `terraform validate` para ver que esté correcta la sintaxi del fichero de configuración que se crea.  

## FORMAT  

+ Podemos usar la orden `terraform fmt` para ver que esté correcta la anidación del fichero de configuración que se crea.  

## MULTIPLOS PROVIDERS  

+ Podemos crear diferentes recursos en diferentes zonas y provedores.  

+ Ejemplo:  
```
provider "aws"{
    region = "us-west-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
    alias = west
}
provider "aws"{
    region = "us-east-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
    alias = east
}
resource "aws_instance" "DB"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    provider = aws.west
}
resource "aws_instance" "web"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    depends_on = [aws_instance.DB]
}
```

## LOCAL PROVISIONER  

+ Para usar un comando que haga cosas en local mientras se crea el codigo del fichero.  
```
provider "aws"{
    region = "us-east-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_instance" "DB"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    provisioner "local-exec"{
        command = "echo ${aws_instance.DB.private_ip}" >> private_ip.txt
    }
}
```

## REMOTE PROVISIONER  

+ Para usar un comando que haga cosas en REMOTO mientras se crea el codigo del fichero.  
```
provider "aws"{
    region = "us-east-1"
    access_key = "xxxxx"
    secret_key = "xxxxx"
}
resource "aws_instance" "DB"{
    ami = "ami-047a51fa27710816e"
    instance_type = "t2.micro"
    key_name = "tf_test"
    provisioner "remote-exec"{
        inline = [
            "sudo dnf install nginx -y"
            "sudo systemctl start nginx"
        ]
    }
    connection {
        type = "ssh"
        user = "ec2-user"
        private_key = file("./test.pem")
        host = sef.public_ip
    }
}
```

## PLAN DESTROY

+ Para borrar los planes se usa `terraform plan -destroy`

## TARGET  

+ Sirve para destruir y aplicar un solo recurso en concreto al que quieras eliminar/añadir. También arrastrará a todo lo que lleve relacionado:  
`terraform destroy -target aws_instance.web-server-instance`  

## WORKSPACES  

+ Se usa como si utilizaras diferentes ramas:  
`terraform workspace`
`terraform workspace new test`
`terraform workspace list`
`terraform workspace show`
`terraform workspace select test`
`terraform workspace delete test`

## STATE  

+ Se puede utilizar diferentes comandos para ver estados:  
`terraform state`
`terraform state list`
`terraform state show aws_eip.one(el recurso creado)`


## FUNCTIONS  

+ Se pueden usar [funciones](https://www.terraform.io/docs/language/functions/index.html)


## EJERCICIO PRÁCTICO

+ En este ejercicio vamos a crear una instancia con ubuntu y un webserver de apache.  

```
provider "aws" {
    region = "us-east-1"
    access_key = "xxx"
    secret_key = "xxx"
}

# 1. Create vpc
resource "aws_vpc" "prod-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "production"
  }
}
# 2. Create Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.prod-vpc.id
}
# 3. Create Custom Route Table
resource "aws_route_table" "prod-route-table" {
  vpc_id = aws_vpc.prod-vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
  route {
    ipv6_cidr_block = "::/0"
    gateway_id      = aws_internet_gateway.gw.id
  }
  tags = {
    Name = "Prod"
  }
}
# 4. Create a Subnet 
resource "aws_subnet" "subnet-1" {
  vpc_id            = aws_vpc.prod-vpc.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  tags = {
    Name = "prod-subnet"
  }
}
# 5. Associate subnet with Route Table
resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.subnet-1.id
  route_table_id = aws_route_table.prod-route-table.id
}
# 6. Create Security Group to allow port 22,80,443
resource "aws_security_group" "allow_web" {
  name        = "allow_web_traffic"
  description = "Allow Web inbound traffic"
  vpc_id      = aws_vpc.prod-vpc.id
  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = {
    Name = "allow_web"
  }
}
# 7. Create a network interface with an ip in the subnet that was created in step 4
resource "aws_network_interface" "web-server-nic" {
  subnet_id       = aws_subnet.subnet-1.id
  private_ips     = ["10.0.1.50"]
  security_groups = [aws_security_group.allow_web.id]
}
# 8. Assign an elastic IP to the network interface created in step 7
resource "aws_eip" "one" {
  vpc                       = true
  network_interface         = aws_network_interface.web-server-nic.id
  associate_with_private_ip = "10.0.1.50"
  depends_on                = [aws_internet_gateway.gw]
}
output "server_public_ip" {
  value = aws_eip.one.public_ip
}
# 9. Create Ubuntu server and install/enable apache2
resource "aws_instance" "web-server-instance" {
  ami               = "ami-085925f297f89fce1"
  instance_type     = "t2.micro"
  availability_zone = "us-east-1a"
  key_name          = "main-key"
  network_interface {
    device_index         = 0
    network_interface_id = aws_network_interface.web-server-nic.id
  }
  user_data = <<-EOF
                #!/bin/bash
                sudo apt update -y
                sudo apt install apache2 -y
                sudo systemctl start apache2
                sudo bash -c 'echo your very first web server > /var/www/html/index.html'
                EOF
  tags = {
    Name = "web-server"
  }
}
output "server_private_ip" {
  value = aws_instance.web-server-instance.private_ip
}
output "server_id" {
  value = aws_instance.web-server-instance.id
}
resource "<provider>_<resource_type>" "name" {
    config options.....
    key = "value"
    key2 = "another value"
}
```

### Con variables  

+ En este ejercicio practico se puede crear variables en otros ficheros para construir los recursos:  
```
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAJTTSSUF2PB6HDCCA"
  secret_key = "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"
}

variable "subnet_prefix" {
  description = "cidr block for the subnet"

}



resource "aws_vpc" "prod-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "production"
  }
}

resource "aws_subnet" "subnet-1" {
  vpc_id            = aws_vpc.prod-vpc.id
  cidr_block        = var.subnet_prefix[0].cidr_block
  availability_zone = "us-east-1a"

  tags = {
    Name = var.subnet_prefix[0].name
  }
}

resource "aws_subnet" "subnet-2" {
  vpc_id            = aws_vpc.prod-vpc.id
  cidr_block        = var.subnet_prefix[1].cidr_block
  availability_zone = "us-east-1a"

  tags = {
    Name = var.subnet_prefix[1].name
  }
}
```

+ Un fichero creao llamado `terraform-tfvars`:  
`subnet_prefix = [{ cidr_block = "10.0.1.0/24", name = "prod_subnet" }, { cidr_block = "10.0.2.0/24", name = "dev_subnet" }]`

+ Para construir este fichero se utiliza un `terraform apply`  