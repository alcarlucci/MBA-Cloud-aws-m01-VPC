aws-m01-VPC
# IGTI - MBA Cloud: provisionamento de uma Arquitetura Básica de Redes na AWS (VPC)

## Arquitetura básica de uma VPC com duas Subnets - uma pública e uma privada:

**Desenho da Arquitetura**
![M01_Cap03_Lab-VPC](https://user-images.githubusercontent.com/101406714/167273131-149eabc4-9cee-4124-9349-e735bb8be6b8.png)

## Criação e configuração dos componentes da Rede

**1. Criação da VPC (Virtual Private Cloud)**
- VPC -> Your VPCs -> Create VPC
  - Name: vpc-mbacloud-m01
  - IPv4 CIDR: 10.0.0.0/16

![image](https://user-images.githubusercontent.com/101406714/167306539-6685b1a7-8c99-4449-9477-e01f592b4744.png)

**2. Criação das Subnets Pública e Privada em zonas de disponibilidade diferentes**
- VPC -> Subnets -> Create subnet

- Subnet Pública:
  - Name: snet-mbacloud-pub-1a
  - AZ: us-east-1a
  - IPv4 CIDR: 10.0.1.0/24
- Subnet Privada:
  - Name: snet-mbacloud-priv-1b
  - AZ: us-east-1b
  - IPv4 CIDR: 10.0.2.0/24

![image](https://user-images.githubusercontent.com/101406714/167306739-0265e26c-e30b-4217-a3b3-426548727359.png)

## Configuração dos Gateways de comunicação e Tabelas de Roteamento

**3. Criação do Internet Gateway para habilitar a comunicação da VPC com a Internet**
- VPC -> Internet Gateways -> Create internet gateway
  - Name: igw-mbacloud
- Atachar o Internet Gateway na VPC
  - VPC -> Internet Gateways -> Actions -> Attach to VPC (vpc-mbacloud-m01)

![image](https://user-images.githubusercontent.com/101406714/167306913-f52c62fe-b709-47d7-bc46-9ad171310957.png)

**4. Criação do NAT Gateway para Subnet privada ter acesso a Internet (para isso ocorrer é preciso associar o NAT Gateway à Subnet pública)**
- VPC -> NAT Gateways -> Create NAT gateway
  - Name: ngw-mbacloud
  - Subnet: snet-mbacloud-pub-1a (associado à Subnet pública)
  - Connectivity type: Public

![image](https://user-images.githubusercontent.com/101406714/167307743-e88b9a87-aa72-4d62-bc20-c9fc6100732b.png)

**5. Criação de uma Tabela de Roteamento para associação com a Subnet Pública**
- VPC -> Route Tables -> Create route table
  - Name: rtab-mbacloud-pub
- Inserção de uma rota para o Internet Gateway (comunicação com a Internet)
  - Route Tables -> Routes -> Edit routes -> Add route: 0.0.0.0/0 com Target no Internet Gateway (igw-mbacloud)
- Associação com a Subnet pública
  - Route Tables -> Subnet associations -> Edit subnet associations: seleciona a Subnet pública (snet-mbacloud-pub-1a)

![image](https://user-images.githubusercontent.com/101406714/167307261-a3066b8a-217b-4b15-aedc-988d43e592ae.png)

**6. Criação de uma Tabela de Roteamento para associação com a Subnet Privada**
- VPC -> Route Tables -> Create route table
  - Name: rtab-mbacloud-priv
- Inserção de uma rota para o NAT Gateway (comunicação de saída da subnet privada com a Internet)
  - Route Tables -> Routes -> Edit routes -> Add route: 0.0.0.0/0 com Target no NAT Gateway (ngw-mbacloud)
- Associação com a Subnet privada
  - Route Tables -> Subnet associations -> Edit subnet associations: seleciona a Subnet privada (snet-mbacloud-priv-1b)

![image](https://user-images.githubusercontent.com/101406714/167308007-298c6299-3dd0-4bc8-b787-282ac03f00e8.png)

## Criação das Instâncias EC2 e configuração básica das conexões

**7. Criação das Instâncias EC2 nas Subnets da VPC**
- EC2 -> Instances -> Launch instances

- Instância Amazon Linux na Subnet Pública (snet-mbacloud-pub-1a)
  - Name: vm-Linux-pub
  - Security Group: criação de novo SG habilitando conexão SSH
- Instância Amazon Linux na Subnet Privada (snet-mbacloud-priv-1b)
  - Name: vm-Linux-priv

![image](https://user-images.githubusercontent.com/101406714/167396817-7b7402ec-6ce7-4b83-aff5-1df20bfe12e0.png)

- para testar a conexão na Instância pública via SSH, usar o comando:
```
ssh -i [nomekeypair.pem] ec2-user@[PublicIP]
```
Conectado na instância "vm-Linux-pub"
![image](https://user-images.githubusercontent.com/101406714/167397307-76379c67-402a-49e3-99e9-a7f388b0ffe3.png)

##
**André Carlucci**
