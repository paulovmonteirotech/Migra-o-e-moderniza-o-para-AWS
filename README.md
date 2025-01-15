# 🚀 TI SOLUÇÕES INCRÍVEIS 
**Projeto:** _`Modernização de infraestrutura de servidores de aplicação Ecommerce`_  
**Cliente:** _`Fast Engineering S/A`_  
**Criado em:** _`10 de janeiro de 2025`_ - **Última atualização:** _`15 de janeiro de 2025`_

## 📋 Sumário
- [Visão Geral](#-vis%C3%A3o-geral)
- [Situação Atual](#-situa%C3%A7%C3%A3o-atual)
- [Arquiteturas](#-arquiteturas)
  - [Arquitetura Atual](#arquitetura-atual)
  - [Migração Lift-and-Shift](#migração-lift-and-shift)
  - [Modernização com Kubernetes](#modernização-com-kubernetes)
- [Etapas do Projeto](#-etapas-do-projeto)
  - [Etapa 1: Lift-and-Shift](#etapa-1-migra%C3%A7%C3%A3o-lift-and-shift---roteiro)
  - [Etapa 2: Modernização](#etapa-2-moderniza%C3%A7%C3%A3o-com-kubernetes----roteiro)

## 🎯 Visão Geral

A Fast Engineering S/A busca modernizar seu sistema de eCommerce através de uma migração para AWS em duas etapas: uma migração inicial "lift-and-shift" seguida de uma modernização completa utilizando Kubernetes.

## 🏢 Situação Atual

Sistema on-premise composto por:
- Servidor de Banco de Dados MySQL (500GB, 10GB RAM, 3 Core CPU)
- Servidor Frontend React (5GB, 2GB RAM, 1 Core CPU)
- Servidor Backend com 3 APIs + Nginx (5GB, 4GB RAM, 2 Core CPU)

## 📐 Arquiteturas

### Arquitetura Atual

```mermaid
flowchart TD
    subgraph "Infra On-Premise"
        CLIENT[Cliente] --> FE[Frontend Server - React]
        FE --> BE[Backend Server - Nginx]
        BE --> DB[Database Server - MySQL]
    end
```

### Migração Lift-and-Shift


### Modernização com Kubernetes



## 📍 Etapas do Projeto

### Etapa 1: Migração Lift-and-Shift -> [Roteiro](lift-and-shift.md)

- **Objetivo**: Migração rápida com mínimas alterações
- **Componentes Principais**:
  - Utilização de serviços AWS equivalentes:
      - Instâncias EC2 para frontend, backend e RDS
  - Segurança básica com grupos de segurança e ACLs
- **Ferramentas**:
  - AWS Application Migration Service (AWS MGN)
  - AWS Database Migration Service (DMS)
- **Requisitos de segurança**:
  - Implementação de IAM roles e policies
  - Configuração de Security Groups e Network ACLs
- **Processo de backup**:
  - Configuração de snapshots automáticos para EC2
  - Backup de banco de dados com AWS Backup
- **Custo da infraestrutura na AWS**:
  - [Utilização da AWS Pricing Calculator para estimar custos](https://calculator.aws/#/estimate?id=e9cb7dffa4052ff3c416d089e356852cf2e901ca)

### Etapa 2: Modernização com Kubernetes  -> [Roteiro](modernizacao.md)

- **Objetivo**: Modernização completa da infraestrutura
- **Componentes Principais**:
  - Migração para Amazon EKS
  - Implementação de CI/CD
  - Banco de dados gerenciado com Aurora Multi-AZ
  - Sistema robusto de backup
  - Segurança avançada com WAF e Shield
  - Monitoramento com CloudWatch
- **Ferramentas**:
  - Amazon EKS
- **Requisitos de segurança**:
  - Implementação de políticas de segurança no Kubernetes
  - Monitoramento e logging com AWS CloudWatch
  - Cloudfront como CDN para uma criar uma camada adicional
  - WAF para mitigação de SQL Injection e Script Cross-Site
- **Processo de backup**:
  - Backup de volumes EBS e snapshots de banco de dados
  - Utilização de AWS S3 para armazenamento de backups
- **Custo da infraestrutura na AWS**:
  - Utilização da AWS Pricing Calculator para estimar custos

## 📝 Observações

Os diagramas mostram a evolução da arquitetura em cada fase, com foco em:
1. Situação atual on-premise
2. Migração lift-and-shift mantendo a estrutura similar
3. Modernização completa com Kubernetes e serviços gerenciados

## 🔒 Segurança

- Implementação de WAF para proteção contra ataques web
- AWS Shield para proteção DDoS
- Grupos de segurança e Network ACLs
- Multi-AZ para alta disponibilidade
- Backup automatizado de dados críticos

## 📊 Monitoramento

- CloudWatch para métricas e logs
- Alertas automáticos
- Dashboard de monitoramento
- Rastreamento de performance

---
⚡️ Desenvolvido por Paulo Monteiro e Mariana
