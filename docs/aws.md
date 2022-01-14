# AWS  

## GUÍA AWS  

### S3 (Simple Storage)  

+ En esta parte podemos crear los llamados cajones/cestas en la nube para guardar ciertos tipo de datos en nuestro AWS. Se llaman BUCKETS.  

+ En estos BUCKETS podemos meter archivos, especificar distintas medidas de almacenamiento, podemos indicarle el versioning de lo que tenemos dentro a lo actualizado, logging si se necesita loguearse para poder acceder al bucket y podemos gestionar otro tipo de medidas y seguridad para poder acceder al bucket y conectarse a el.  

+ Otro tipo de uso de los BUCKETS son el uso de almacenar paginas web estaticas.

### IAM - SECURITY  

+ En esta sección podemos crear:  
  - Usuarios
  - Grupos
  - Politicas de grupo
  - Roles 

### EC2 (Elastic Compute Services)






## IAC CLOUDFORMATION

+ IAC (Infrastructure as Code)

+ AWS CloudFormation le ofrece una forma sencilla de modelar un conjunto de recursos relacionados de AWS y de terceros, aprovisionarlos de manera rápida y consistente y administrarlos a lo largo de sus ciclos de vida tratando la infraestructura como un código. La plantilla de CloudFormation describe los recursos que desea y sus dependencias para que los pueda lanzar y configurar juntos como una pila. Puede usar la plantilla para crear, actualizar y eliminar toda una pila como una única unidad, tantas veces como lo necesite, en lugar de administrar los recursos de manera individual. Puede administrar y aprovisionar pilas en varias cuentas y regiones de AWS.

+ web (https://aws.amazon.com/es/cloudformation/)

+ Documentación (https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

+ Sintaxis simple:  
```
---
Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-123456789
            InstanceType: t2.micro
```

+ Sintaxis compleja:  
```
---
Parameters:
    SecurityGroupDescription:
        Description: Security-Group Description
        Type: String

Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-123456789
            InstanceType: t2.micro
            SecurityGroups:
                - !Ref SSHSecurityGroup
                - !Ref ServerSecurityGroup
    
    # an elastic Ip for our instance
    MyEIP:
        Type: AWS::EC2::EIP
        Properties:
            InstanceId: !Ref MyInstance

    # our EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Enable port 22
            SecurityGroupIngress:
            - CidrIp: 0.0.0.0/0
              FromPort: 22
              IpProtocol: tcp
              ToPort: 22

    # our second EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: !Red SecurityGroupDescription
            SecurityGroupIngress:
            - IpProtocol: tcp
              FromPort: 80
              ToPort: 80
              CidrIp: 0.0.0.0/0
            - IpProtocol: tcp
              FromPort: 22
              ToPort: 22
              CidrIp: 10.10.0.1/32
```

### Create STACK  

+ Vamos a consola , cloudformation, crear stack, crear template, subir fichero y ponemos nuestra instancia simple de código en el fichero y veremos como crea una instancia del tipo que se le indica:  
```
---
Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-123456789
            InstanceType: t2.micro
```

+ Para actualizar la que tenemos vamos a crear stack, replace current template, subimos fichero y veremos en los eventos como se actualiza:  
```
---
Parameters:
    SecurityGroupDescription:
        Description: Security-Group Description
        Type: String

Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-123456789
            InstanceType: t2.micro
            SecurityGroups:
                - !Ref SSHSecurityGroup
                - !Ref ServerSecurityGroup
    
    # an elastic Ip for our instance
    MyEIP:
        Type: AWS::EC2::EIP
        Properties:
            InstanceId: !Ref MyInstance

    # our EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Enable port 22
            SecurityGroupIngress:
            - CidrIp: 0.0.0.0/0
              FromPort: 22
              IpProtocol: tcp
              ToPort: 22

    # our second EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: !Red SecurityGroupDescription
            SecurityGroupIngress:
            - IpProtocol: tcp
              FromPort: 80
              ToPort: 80
              CidrIp: 0.0.0.0/0
            - IpProtocol: tcp
              FromPort: 22
              ToPort: 22
              CidrIp: 10.10.0.1/32
```
> Despues se puede borrar con Delete en los eventos.  

### Parámetros  

+ Podemos indicar una serie de [parametros](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) a la hora de crear nuestra IAC:  

+ Ejemplo 1:  
```
Parameters:
  InstanceTypeParameter:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
```

+ Ejemplo2:  
```
Parameters: 
  DBPort: 
    Default: 3306
    Description: TCP/IP port for the database
    Type: Number
    MinValue: 1150
    MaxValue: 65535
  DBPwd: 
    NoEcho: true
    Description: The database admin account password
    Type: String
    MinLength: 1
    MaxLength: 41
    AllowedPattern: ^[a-zA-Z0-9]*$
```  

### Recursos  

+ En esta sección declaramos qué [recursos](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html#resources-section-structure-examples) queremos crear en nuestro stack

+ Ejemplo:  
```
Resources: 
  MyInstance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      UserData: 
        "Fn::Base64":
          !Sub |
            Queue=${MyQueue}
      AvailabilityZone: "us-east-1a"
      ImageId: "ami-0ff8a91507f77f867"
  MyQueue: 
    Type: "AWS::SQS::Queue"
    Properties: {}
```

+ Todos los tipos de [recursos](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_EC2.html) 

+ Ejemplo:  
```
Type: AWS::EC2::Instance
Properties: 
  AdditionalInfo: String
  Affinity: String
  AvailabilityZone: String
  BlockDeviceMappings: 
    - BlockDeviceMapping
  CpuOptions: 
    CpuOptions
  CreditSpecification: 
    CreditSpecification
  DisableApiTermination: Boolean
  EbsOptimized: Boolean
  ElasticGpuSpecifications: 
    - ElasticGpuSpecification
  ElasticInferenceAccelerators: 
    - ElasticInferenceAccelerator
  EnclaveOptions: 
    EnclaveOptions
  HibernationOptions: 
    HibernationOptions
  HostId: String
  HostResourceGroupArn: String
  IamInstanceProfile: String
  ImageId: String
  InstanceInitiatedShutdownBehavior: String
  InstanceType: String
  Ipv6AddressCount: Integer
  Ipv6Addresses: 
    - InstanceIpv6Address
  KernelId: String
  KeyName: String
  LaunchTemplate: 
    LaunchTemplateSpecification
  LicenseSpecifications: 
    - LicenseSpecification
  Monitoring: Boolean
  NetworkInterfaces: 
    - NetworkInterface
  PlacementGroupName: String
  PrivateIpAddress: String
  RamdiskId: String
  SecurityGroupIds: 
    - String
  SecurityGroups: 
    - String
  SourceDestCheck: Boolean
  SsmAssociations: 
    - SsmAssociation
  SubnetId: String
  Tags: 
    - Tag
  Tenancy: String
  UserData: String
  Volumes: 
    - Volume
```  

### Mapping  

+ [Mapea](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html) asignando una clave a un valor  

+ Ejemplo:  
```
AWSTemplateFormatVersion: "2010-09-09"
Mappings: 
  RegionMap: 
    us-east-1:
      HVM64: ami-0ff8a91507f77f867
      HVMG2: ami-0a584ac55a7631c0c
    us-west-1:
      HVM64: ami-0bdb828fd58c52235
      HVMG2: ami-066ee5fd4a9ef77f1
    eu-west-1:
      HVM64: ami-047bb4163c506cd98
      HVMG2: ami-0a7c483d527806435
    ap-northeast-1:
      HVM64: ami-06cd52961ce9f0d85
      HVMG2: ami-053cdd503598e4a9d
    ap-southeast-1:
      HVM64: ami-08569b978cc4dfa10
      HVMG2: ami-0be9df32ae9f92309
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
      InstanceType: m1.small
```  


### Outputs  

+ Los [outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) nos salidas de mensajes. Intentar no poner información sensible.  

+ Ejemplo:  
```
Outputs:
  BackupLoadBalancerDNSName:
    Description: The DNSName of the backup load balancer
    Value: !GetAtt BackupLoadBalancer.DNSName
    Condition: CreateProdResources
  InstanceID:
    Description: The Instance ID
    Value: !Ref EC2Instance
```  

```
Outputs:
  BackupLoadBalancerDNSName:
    Description: The DNSName of the backup load balancer
    Value: !GetAtt BackupLoadBalancer.DNSName
    Condition: CreateProdResources
  InstanceID:
    Description: The Instance ID
    Value: !Ref EC2Instance
``` 

### Conditions  

+ Los [conditions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html) nos indican que hace algo si se cumple tal condición.  

+ Ejemplo:  
```
AWSTemplateFormatVersion: 2010-09-09
Parameters:
  EnvType:
    Description: Environment type.
    Default: test
    Type: String
    AllowedValues:
      - prod
      - test
    ConstraintDescription: must specify prod or test.
Conditions:
  CreateProdResources: !Equals 
    - !Ref EnvType
    - prod
Resources:
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: ami-0ff8a91507f77f867
  MountPoint:
    Type: 'AWS::EC2::VolumeAttachment'
    Condition: CreateProdResources
    Properties:
      InstanceId: !Ref EC2Instance
      VolumeId: !Ref NewVolume
      Device: /dev/sdh
  NewVolume:
    Type: 'AWS::EC2::Volume'
    Condition: CreateProdResources
    Properties:
      Size: 100
      AvailabilityZone: !GetAtt 
        - EC2Instance
        - AvailabilityZone
```


### Functions  

+ Hay diferentes [funciones](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html) internas en la creación de un template IAC 


### User data  

+ Podemos crear plantillas escribiendo ordenes de linux con [user data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)  

+ Ejemplo:  
```
---
Parameters:
    SSHKey:
        Description: Name of an existing EC2 keypair to enable ssh access to the instance
        Type: AWS::EC2::KeyPair::KeyName

Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-009d6802948d06e52
            InstanceType: t2.micro
            KeyName: !Ref SSHKey
            SecurityGroups:
                - !Ref SSHSecurityGroup
            UserData:
              Fn::Base64: |
                #!/bin/bash -xe
                yum update -y
                yum install -y httpd
                systemctl start httpd
                systemctl enable httpd
                echo "Hello World from user data" > /var/www/html/index.html

    # our EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Enable port 22
            SecurityGroupIngress:
            - CidrIp: 0.0.0.0/0
              FromPort: 22
              IpProtocol: tcp
              ToPort: 22
            - CidrIp: 0.0.0.0/0
              FromPort: 80
              IpProtocol: tcp
              ToPort: 80
```  
> Creamos una stack con el template, ponemos el parametro creado y vemos que sea crea tanto por la ip:80 como por consola.  


### Cfn-init  

+ Vemos la docu de [cfn init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html) 

+ Ejemplo:  
```
---
Parameters:
    SSHKey:
        Description: Name of an existing EC2 keypair to enable ssh access to the instance
        Type: AWS::EC2::KeyPair::KeyName

Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
            AvailabilityZone: us-east-1a
            ImageId: ami-009d6802948d06e52
            InstanceType: t2.micro
            KeyName: !Ref SSHKey
            SecurityGroups:
                - !Ref SSHSecurityGroup
            UserData:
              Fn::Base64:
                !Sub |
                  #!/bin/bash -xe
                  # get the latest cloudformation package
                  yum update -y aws-cfn-bootstrap
                  # start cfn-init
                  /opt/aws/bin/cfn-init -s ${AWS::StackId} -r MyInstance --region ${AWS::Region} || error_exit "Failed to run cfn-init"
    Metadata:
      Comment: Install a simple Apache HTTP page
      AWS::CloudFormation::Init:
        config:
          package:
            yum:
              httpd:[]
          files:
            "/var/www/html/index.html"
              content: |
                <h1>Hello word, using cfn-init</h1>
              mode: '000644'
          commands:
            hello:
              command: "echo 'hello world'"
          services:
            sysvinit:
              httpd:
                enabled: 'true'
                ensureRunning: 'true'

    # our EC2 security group
    SSHSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
            GroupDescription: Enable port 22
            SecurityGroupIngress:
            - CidrIp: 0.0.0.0/0
              FromPort: 22
              IpProtocol: tcp
              ToPort: 22
            - CidrIp: 0.0.0.0/0
              FromPort: 80
              IpProtocol: tcp
              ToPort: 80
```  

### Cfn-signal  

+ Docu de [cfn-signal](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-signal.html)

+ Ejemplo:  
```
AWSTemplateFormatVersion: 2010-09-09
Description: Simple EC2 instance
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Metadata:
      'AWS::CloudFormation::Init':
        config:
          files:
            /tmp/test.txt:
              content: Hello world!
              mode: '000755'
              owner: root
              group: root
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
      UserData: !Base64
        'Fn::Join':
          - ''
          - - |
              #!/bin/bash -x
            - |
              # Install the files and packages from the metadata
            - '/opt/aws/bin/cfn-init -v '
            - '         --stack '
            - !Ref 'AWS::StackName'
            - '         --resource MyInstance '
            - '         --region '
            - !Ref 'AWS::Region'
            - |+

            - |
              # Signal the status from cfn-init
            - '/opt/aws/bin/cfn-signal -e $? '
            - '         --stack '
            - !Ref 'AWS::StackName'
            - '         --resource MyInstance '
            - '         --region '
            - !Ref 'AWS::Region'
            - |+

    CreationPolicy:
      ResourceSignal:
        Timeout: PT5M
```  


### Rollback  

+ Cuando creamos una stack podemos indicar si queremos un rollback para poder volver a un estado anterior.  

### Cfn nested stacks  

+ Docu de stack anidadas entre sí[cfn nested](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-nested-stacks.html)

+ Ejemplo:  
```
---
Parameters:
    SSHKey:
        Description: Name of an existing EC2 keypair to enable ssh access to the instance
        Type: AWS::EC2::KeyPair::KeyName

Resources:
    MyInstance:
        Type: AWS::EC2::Instance
        Properties:
          TemplateURL: https://s3.amazonaws.com/cloudformation-templates-us-east-1/LAMP_Single-Instance.template
          Parameters:
            KeyName: !Ref SSHKey
            DBName: "mydb"
            DBUser: "user"
            DBPassword: "pass"
            DBRootPassword: "passroot"
            InstanceType: t2.micro
            SSHLocation: "0.0.0.0/0"

Outputs:
  StackRef:
    value: !Ref myStack
  OutputFromNestedStack:
    value: !GetAtt mystack.Outputs.WebsiteURL
```  

### Change sets  

+ [DOCU](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html)

+ Sirve para actualizar o subir otra cosa de una stack ya creada y podremos ver el proceso de si se haría bien o no. Si nos interesa, le damos a EXECUTE y empieza a cambiarse, sino DELETE.  


### Depends on  

+ [DOCU](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-dependson.html)  

+ De crear una instancia que depende de otra creada.  

+ Ejemplo:  
```
AWSTemplateFormatVersion: '2010-09-09'
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0ff8a91507f77f867
    us-west-1:
      AMI: ami-0bdb828fd58c52235
    eu-west-1:
      AMI: ami-047bb4163c506cd98
    ap-northeast-1:
      AMI: ami-06cd52961ce9f0d85
    ap-southeast-1:
      AMI: ami-08569b978cc4dfa10
Resources:
  Ec2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Fn::FindInMap:
        - RegionMap
        - Ref: AWS::Region
        - AMI
    DependsOn: myDB
  myDB:
    Type: AWS::RDS::DBInstance
    Properties:
      AllocatedStorage: '5'
      DBInstanceClass: db.t2.small
      Engine: MySQL
      EngineVersion: '5.5'
      MasterUsername: MyName
      MasterUserPassword: MyPassword
```  

### Detect drift stack  

+ Sirve para hacer marcas y ver donde se cambió si actualizas la template o modificas algo de la template  

+ [DOCU](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html)

+ Vamos a stack actions - detect drift una vez ya hemos metido nuestra template creada. Podemos ver en view drift y si modificamos cosas como el security groups veremos que la marca cambia.



























