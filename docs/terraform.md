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

## WORKSPACES  

+ Se usa como si utilizaras diferentes ramas:  
`terraform workspace`
`terraform workspace new test`
`terraform workspace list`
`terraform workspace show`
`terraform workspace select test`
`terraform workspace delete test`

## FUNCTIONS  

+ Se pueden usar [funciones](https://www.terraform.io/docs/language/functions/index.html)



