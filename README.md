# Terraform AWS EC2 Tutorial - Infraestrutura como Código

Este repositório contém a implementação do tutorial oficial da HashiCorp para criação de infraestrutura na AWS utilizando Terraform. O projeto provisiona uma instância EC2 com Ubuntu 24.04 na região us-west-2.

## 📋 Pré-requisitos

- [Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) (1.2.0+)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) instalado
- Conta AWS com credenciais configuradas
- Acesso à região `us-west-2` (Oregon)

## 🎯 Objetivo

Criar uma instância EC2 (t2.micro) na AWS utilizando Terraform, demonstrando os conceitos de:
- **IaC (Infrastructure as Code)**: Gerenciamento de infraestrutura através de código
- **Providers**: Plugins que permitem Terraform interagir com APIs de cloud
- **Data Sources**: Consulta dinâmica de recursos existentes
- **State Management**: Rastreamento do estado da infraestrutura

## 📁 Estrutura do Projeto

```
learn-terraform-aws/
├── terraform.tf      # Configuração do Terraform e providers
├── main.tf           # Definição da infraestrutura (provider, data source, recurso)
├── .gitignore        # Arquivos ignorados pelo Git
└── README.md         # Documentação completa
```

## 🚀 Passo a Passo

### 1. Criação do Diretório do Projeto

```bash
mkdir learn-terraform-aws
cd learn-terraform-aws
```

**Propósito:** Organizar os arquivos de configuração Terraform em um diretório dedicado.

---

### 2. Configuração do Terraform (`terraform.tf`)

Criamos o arquivo `terraform.tf` para definir o provider AWS e versões:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.92"
    }
  }

  required_version = ">= 1.2"
}
```

**Propósito:** 
- Define o provider AWS da HashiCorp Registry
- Estabelece versionamento para garantir consistência
- `~> 5.92` permite versões 5.92+ mas < 6.0

---

### 3. Definição da Infraestrutura (`main.tf`)

```hcl
provider "aws" {
  region = "us-west-2"
}

data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*"]
  }

  owners = ["099720109477"] # Canonical
}

resource "aws_instance" "app_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"

  tags = {
    Name = "learn-terraform"
  }
}
```

**Componentes:**
- **Provider:** Configura a região AWS us-west-2
- **Data Source:** Busca dinamicamente a AMI mais recente do Ubuntu 24.04
- **Resource:** Define a instância EC2 t2.micro (Free Tier) com tag identificadora

---

### 4. Formatação do Código

```bash
terraform fmt
```

**Resultado:**
```
main.tf
```

**Propósito:** Padroniza a formatação dos arquivos .tf conforme as convenções da HashiCorp.

---

### 5. Inicialização do Workspace

```bash
terraform init
```

**Resultado:**
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 5.92"...
- Installing hashicorp/aws v5.100.0...
- Installed hashicorp/aws v5.100.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above.

Terraform has been successfully initialized!
```

**Propósito:** 
- Download e instalação do provider AWS
- Criação do arquivo `.terraform.lock.hcl` para garantir versões consistentes
- Preparação do diretório para operações Terraform

---

### 6. Validação da Configuração

```bash
terraform validate
```

**Resultado:**
```
Success! The configuration is valid.
```

**Propósito:** Verifica sintaxe e consistência interna da configuração antes de aplicar mudanças.

---

### 7. Criação da Infraestrutura

```bash
terraform apply -auto-approve
```

**Resultado:**
```
data.aws_ami.ubuntu: Reading...
data.aws_ami.ubuntu: Read complete after 2s [id=ami-096f5760b00bcd95c]

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                                  = "ami-096f5760b00bcd95c"
      + instance_type                        = "t2.micro"
      + tags                                 = {
          + "Name" = "learn-terraform"
        }
      ...
    }

Plan: 1 to add, 0 to change, 0 to destroy.

aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [00m10s elapsed]
aws_instance.app_server: Still creating... [00m18s elapsed]
aws_instance.app_server: Still creating... [00m28s elapsed]
aws_instance.app_server: Creation complete after 35s [id=i-0f365259e5bcc200c]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

**Propósito:**
- Cria o plano de execução (execution plan)
- Provisiona a instância EC2 na AWS
- Exibe progresso em tempo real
- Confirma criação com sucesso

---

### 8. Inspeção do Estado

```bash
terraform state list
```

**Resultado:**
```
data.aws_ami.ubuntu
aws_instance.app_server
```

---

```bash
terraform show
```

**Resultado:** Exibe todos os atributos da infraestrutura provisionada, incluindo:
- AMI ID: `ami-096f5760b00bcd95c`
- Instance ID: `i-0f365259e5bcc200c`
- Instance Type: `t2.micro`
- Availability Zone: `us-west-2c`
- Instance State: `running`
- Public IP: `54.71.125.62`
- Private IP: `172.31.3.7`
- Security Group: `default`
- VPC: `subnet-0ca534fcb1b0cf430`

**Propósito:** Rastreia e armazena o estado da infraestrutura para gerenciamento futuro.

---

## 📊 Recursos Provisionados

### Instância EC2

| Atributo | Valor |
|----------|-------|
| **Instance ID** | i-0f365259e5bcc200c |
| **AMI** | ami-096f5760b00bcd95c (Ubuntu 24.04 Noble) |
| **Instance Type** | t2.micro |
| **Region** | us-west-2 (Oregon) |
| **Availability Zone** | us-west-2c |
| **Estado** | running |
| **Public IP** | 54.71.125.62 |
| **Private IP** | 172.31.3.7 |
| **Public DNS** | ec2-54-71-125-62.us-west-2.compute.amazonaws.com |
| **Subnet ID** | subnet-0ca534fcb1b0cf430 |
| **Security Group** | sg-09a0bfd7f99083472 |
| **Network Interface** | eni-06999611841c4c24b |
| **Volume Root** | vol-03353e1a65072af39 (8GB gp3) |
| **Tag Name** | learn-terraform |

### AMI Utilizada

| Atributo | Valor |
|----------|-------|
| **AMI ID** | ami-096f5760b00bcd95c |
| **Nome** | ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-20260610 |
| **Arquitetura** | x86_64 |
| **Virtualização** | HVM |
| **Owner** | Canonical (099720109477) |
| **Plataforma** | Linux/UNIX |

---

## 🛠️ Comandos Úteis

```bash
# Inicializar projeto
terraform init

# Validar configuração
terraform validate

# Ver plano de execução (sem aplicar)
terraform plan

# Aplicar mudanças
terraform apply

# Aplicar sem confirmação interativa
terraform apply -auto-approve

# Destruir infraestrutura
terraform destroy

# Listar recursos no estado
terraform state list

# Mostrar detalhes do estado
terraform show

# Formatar arquivos
terraform fmt
```

---

## ⚠️ Considerações Importantes

- **Custo:** A instância t2.micro se enquadra no AWS Free Tier (750h/mês gratuitas)
- **Segurança:** O arquivo `terraform.tfstate` contém informações sensíveis - não commitar em repositórios públicos sem cuidado
- **Credenciais:** AWS credentials estão configuradas em `~/.aws/credentials` (fora do repositório)
- **Destruição:** Execute `terraform destroy` quando não precisar mais da infraestrutura para evitar cobranças

---

## 📚 Referências

- [Tutorial Oficial HashiCorp](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [HCL Language](https://developer.hashicorp.com/terraform/language)

---

## ✨ Autor

Projeto criado seguindo o tutorial oficial da HashiCorp para aprendizado de Terraform e Infraestrutura como Código.

**Repositório:** https://github.com/lucasbrasil9/terraform-aws-ec2-tutorial
