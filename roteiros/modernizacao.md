*Modernização para AWS com Elastic Kubernetes Service (EKS) e CI/CD Detalhado*
*ATENÇÃO:*
Fazer Backup das Máquinas de front, back, e banco de dados.
 Primeiro fazer a modernização com essse backup como ambiente de teste.
 Validando com o cliente pode se fazer um novo backup do ambiente de produção e fazer a modernização do ambiente de produção para a aws. A primeira modernizaçãp poderá ficar como um ambiente de desenvolvimento que pode ser ligada e desligada para testes.

A modernização da infraestrutura atual para a AWS busca aplicar as melhores práticas de arquitetura cloud-native, como alta disponibilidade, segurança, escalabilidade e automação. Seguindo essas diretrizes, os componentes principais da nova arquitetura serão:

EKS (Elastic Kubernetes Service): Orquestração de contêineres para gerenciar o backend e o frontend.
Amazon RDS (MySQL) em Multi-AZ: Banco de dados gerenciado com backups automáticos e alta disponibilidade.
Amazon S3: Armazenamento de objetos estáticos como imagens e vídeos.
AWS CodePipeline e CodeBuild: Automação de integração e entrega contínua (CI/CD).
AWS WAF, Security Groups, Secrets Manager, e IAM: Garantir a segurança da aplicação e dos dados.
Passo a Passo Detalhado

*1. Preparar o Ambiente de Contêineres*
Criar Cluster EKS
Configuração do Cluster:

Utilize ferramentas como eksctl, Terraform, ou AWS CloudFormation.
Defina subnets privadas para os nós do cluster e subnets públicas para o Classic Load Balancer (LB).
Configure IAM Roles for Service Accounts (IRSA) para permitir acesso seguro dos pods a serviços AWS como RDS, S3 e Secrets Manager.
Autoscaling e Nodes:

Configure o Cluster Autoscaler para redimensionamento automático de nós.
Utilize Auto Scaling Groups para os worker nodes do cluster.
Instâncias recomendadas: t3.medium para ambiente de desenvolvimento e m5.large para produção.
Configurar a Rede
VPC:
Crie uma VPC com subnets públicas e privadas.
Habilite o NAT Gateway para pods que precisem acessar a internet.
Configure o Internet Gateway para subnets públicas.
Ferramentas
kubectl: Para gerenciar o cluster Kubernetes.
AWS CLI: Configuração geral da infraestrutura.
IAM Roles: Permitir operações Kubernetes e acesso seguro a outros serviços AWS.
*2. Containerizar as Aplicações*
Criar Dockerfiles
Backend:
Configure APIs no Dockerfile com:

dockerfile

FROM node:18-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm", "start"]
EXPOSE 3000

Frontend:
Para aplicações React/Angular:
dockerfile

FROM node:18-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 80
CMD ["npx", "serve", "-s", "build"]

Armazenar Imagens no ECR
Crie repositórios no Amazon Elastic Container Registry (ECR):
bash

aws ecr create-repository --repository-name backend-api
aws ecr create-repository --repository-name frontend-app

Build e Push:
bash

docker build -t backend-api:v1 .
docker tag backend-api:v1 <aws_account_id>.dkr.ecr.<region>.amazonaws.com/backend-api:v1
docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/backend-api:v1

Configurar Variáveis de Ambiente
Utilize o Secrets Manager para armazenar credenciais do banco, endpoints e tokens de API.

*3. Implementar CI/CD com AWS*
Configuração do CI/CD
Criação do Repositório:

Utilize o AWS CodeCommit para gerenciar o código-fonte.
bash

aws codecommit create-repository --repository-name app-repo
Pipeline do CodePipeline:

Crie um pipeline com as seguintes etapas:
Source: Integre com o CodeCommit (ou GitHub).
Build: Execute testes e crie imagens Docker.
Deploy: Realize deploy no EKS.
Detalhamento de Cada Etapa
Source (CodeCommit):

Push do código para o branch principal (main ou master).
Build (CodeBuild):

Crie um arquivo buildspec.yml para o CodeBuild:
yaml

version: 0.2
phases:
  install:
    runtime-versions:
      docker: 20
    commands:
      - echo Installing dependencies...
      - npm install
  build:
    commands:
      - echo Building Docker image...
      - docker build -t backend-api:v1 .
      - docker tag backend-api:v1 <aws_account_id>.dkr.ecr.<region>.amazonaws.com/backend-api:v1
      - docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/backend-api:v1
  post_build:
    commands:
      - echo Build completed successfully.
Deploy:

Utilize um script automatizado no CodeDeploy ou execute kubectl apply nos manifests do Kubernetes.

*4. Banco de Dados Gerenciado*
Configure o Amazon RDS em modo Multi-AZ para alta disponibilidade.
Habilite backups automáticos e snapshots periódicos.
Ajuste o Security Group para aceitar conexões apenas do cluster EKS.

*5. Armazenamento no Amazon S3*
Crie um bucket S3 para armazenar objetos (imagens, vídeos, etc.).
Configure Bucket Policies e IAM Roles para acesso seguro.

*6. Segurança e Observabilidade*
Medidas de Segurança
AWS WAF: Bloqueio de ameaças em nível de aplicação (SQLi, XSS).
Security Groups: Controle de tráfego nos nós e banco de dados.
Secrets Manager: Armazenar senhas e tokens.
KMS: Criptografia de dados sensíveis.
Monitoramento
CloudWatch Logs e Metrics: Coleta de logs e métricas dos pods.
Alarms: Notificação de anomalias, como alta latência ou erros HTTP 5xx.

*7. Backup*
RDS:
Backups automáticos habilitados.
Snapshots diários para retenção de longo prazo.
S3:
Versão habilitada para objetos críticos.
Replicação para outra região, garantindo DR (Disaster Recovery).
Diagrama da Nova Arquitetura
Cluster EKS: Subnets privadas.
LB (Ingress Controller): Subnets públicas.
RDS Multi-AZ: Para persistência de dados.
S3: Armazenamento de objetos.
CodePipeline + CodeBuild: CI/CD integrado.
CloudWatch: Monitoramento centralizado.

|Service           |Price (Monthly)    |Region          |
|------------------|-------------------|----------------|
|RDS               |$1.587,80          |North Virginia  |
|Route 53          |$ 00,50            |North Virginia  |
|LB                |$ 19,05            |North Virginia  |
|WAF               |$ 06,00            |North Virginia  |
|S3                |$ 02,30            |North Virginia  |
|CloudWatch        |$ 27,68            |North Virginia  |
|Secrets Manager   |$ 01,20            |North Virginia  |
|KMS               |$ 11,00            |North Virginia  |
|EKS Cluster       |$ 73,00            |North Virginia  |
|VPC               |$ 78,35            |North Virginia  |
|EC2 (Worker Nodes)|$ 132,03           |North Virginia  |
|ECR               |$ 00,50            |North Virginia  |
|AWS Backup        |$ 12,50            |North Virginia  |
|AWS CodePipeline  |$ 00,80            |North Virginia  |
|AWS CodeBuild     |$ 09,00

|**Custo mensal**             |**1.961,71 USD**         
