A migração visa mover todos os recursos da infraestrutura atual (servidores de aplicação, banco de dados e backend) para a AWS, utilizando os serviços adequados para garantir alta disponibilidade, escalabilidade, segurança e continuidade dos serviços.

Objetivos principais:

Migrar servidores de aplicação (frontend e backend) para a AWS.
Migrar banco de dados MySQL para o Amazon RDS.
Garantir que o tráfego de usuários continue fluindo sem interrupções.
Implementar uma arquitetura escalável e segura.


 Etapa 1: As-Is
Atividades Necessárias para a Migração
Planejamento da Migração

Levantamento detalhado da arquitetura atual (servidores, serviços, dados, tráfego).
Avaliação de dependências, como comunicação entre servidores, APIs e o banco de dados.
Definir o planejamento de migração (data e hora de corte, riscos, rollback).
Configuração da AWS

Criar uma conta AWS (caso não tenha).
Criar uma VPC (Virtual Private Cloud) para isolar sua rede.
Configurar sub-redes públicas e privadas para separar os diferentes serviços.
Criar grupos de segurança para controlar o tráfego de entrada e saída de seus servidores.
Configurar IAM (Identity and Access Management) para controle de acessos a recursos.

3. Migração de Servidores (EC2)

Frontend (Aplicação): Migrar servidores web para EC2.
Backend (APIs e Nginx): Migrar servidores backend para EC2.
Banco de Dados MySQL: Utilizar AWS DMS para migrar o banco de dados MySQL.

4. Verificação de Rede e Conectividade

Verificar conectividade entre os servidores EC2, RDS
Verificar a configuração de DNS e ajustar as entradas, se necessário.

5. Testes e Validação Pós-Migração

Testar a acessibilidade do front-end e da API.
Validar a integridade do banco de dados e a consistência dos dados.
Realizar testes de carga para garantir que a infraestrutura na AWS suporte o volume de tráfego.

6. Pós-Migração

Monitorar a performance e os logs utilizando CloudWatch.
Implementar ajustes finos e backups regulares.

7. Ferramentas e Serviços Utilizados

AWS Application Migration Service (MGN):
Serviço da AWS para migração de servidores de forma "lift-and-shift", ou seja, movendo a infraestrutura para a AWS sem grandes modificações.

AWS Database Migration Service (DMS):
Ferramenta usada para migrar o banco de dados MySQL para o Amazon RDS sem causar grandes interrupções, mantendo a consistência dos dados.

Amazon EC2:
Instâncias de computação para hospedar o frontend, backend e outros serviços da aplicação.

Amazon RDS (MySQL):
Serviço de banco de dados gerenciado para substituir o MySQL local, com escalabilidade automática e backups.

AWS Backup:
Serviço de backup automatizado e centralizado para EC2 e RDS.

 -------------Detalhamento do Processo de Migração------------
 *ATENÇÃO:*
 Fazer Backup das Máquinas de front, back, e banco de dados.
 Primeiro fazer a migração com essse backup como ambiente de teste.
 Validando com o cliente pode se fazer um novo backup do ambiente de produção e fazer a migração do ambiente de produção para a aws. A primeira migração poderá ficar como um ambiente de desenvolvimento que pode ser ligada e desligada para testes.

*---WS Application Migration Service (MGN)---*

*Instalação e Configuração do MGN na Máquina de Origem:*

*Passo 1:* No Console AWS, acesse o AWS MGN e crie uma nova Configuração de Migração.
*Passo 2:* Baixe o agente MGN na máquina de origem.
*Passo 3:* Instale o agente na máquina de origem (servidor de aplicação e backend) seguindo os passos de instalação fornecidos pela AWS.
*Passo 4:* Ao instalar, o agente começará a replicar os dados da máquina para a AWS.

*Configuração na AWS:*

*Passo 1:* No Console AWS, crie uma instância EC2 de destino para cada servidor que você migrará, configure o EBS para o tamanho necessario e para não excluir no encerramento.
*Passo 2:* Configure os grupos de segurança, sub-redes e roles IAM para garantir acesso seguro.
*Passo 3:* Inicie a replicação de dados e a sincronização entre o servidor de origem e a instância EC2 na AWS.
*Passo 4:* Após a sincronização, execute o processo de cutover para realizar a migração final, desligando a máquina original e ativando a instância EC2.


*----AWS Database Migration Service (DMS)----*

*Configuração de DMS:*
*Passo 1:* Provisionar Amazon RDS. Escolha versão do MySQL compatível.
Para um lift and shift TOTAL, será utilizado um RDS single-AZ
*Passo 2:* No Console AWS, acesse AWS DMS e crie um Endpoint de Origem (para o MySQL on-premises).
*Passo 3:* Crie um Endpoint de Destino para o Amazon RDS (MySQL).
*Passo 4:* Crie uma Tarefa de Migração que definirá os dados a serem migrados, considerando migração contínua ou migração em lote (real-time ou batch).
*Passo 5:* Inicie a tarefa de migração e monitore os logs e a integridade dos dados durante a migração.

*Pós-Migração no DMS:*

Após a migração inicial, o DMS pode continuar replicando os dados até que a migração seja totalmente concluída.
Verifique os dados no RDS para garantir que não houve perda de informações.

*DMS Validação*

```sql
-- Validar contagem de registros
SELECT COUNT(*) FROM table_name;
-- Validar últimos registros
SELECT * FROM table_name ORDER BY timestamp DESC LIMIT 10;
```

*----Requisitos de Segurança----*
*VPC:*
 Criação de uma VPC privada para isolar a infraestrutura da AWS.

Sub-redes públicas para front-end.
Sub-redes privadas para backend.
Sub-redes privadas para banco de dados.
Internet gatway
Natgatway
Rotas publicas Front-end com Internet Gatway
Rotas privadas Back-end com Natgatway

*Grupos de Segurança:*

*Grupo para ec2 backend*
Apenas entrada portas 80 e 443 da internet
Entrada e saída com o grupo do RDS porta 3306
Entrada e saída com o grupo do ec2 front portas 80 e 443
*Grupo para ec2 frontend*
Entrada e saída livre para todos os ips portas 80 e 443
*Grupo para RDS*
Entrada e saída porta 3306 para grupo do ec2 back-end

Configuração de regras restritivas para controlar o tráfego (somente permitido entre servidores necessários).

*IAM:*
Configuração de roles e políticas específicas para garantir o acesso mínimo necessário para os usuários e sistemas de acordo com a exigencia e necessidade do cliente.

Políticas Granulares: Crie políticas específicas para cada serviço, aplicando o princípio do menor privilégio.
Roles para Migração:
Role para MGN com acesso apenas a recursos necessários.
Role para DMS com permissões restritas ao RDS e à origem de dados.
Gerenciamento de Usuários: Segmente permissões conforme as responsabilidades, garantindo auditoria e rastreabilidade.


*Criptografia:*

RDS:
 Criptografar o banco de dados MySQL com KMS (AWS Key Management Service).

*----Processo de Backup---*

*AWS Backup:*

Configuração de políticas de backup para EC2 e RDS.
Realização de backups automáticos diários, semanais e mensais.
Armazenamento de backups em Amazon S3 Glacier para retenção de longo prazo.
Backups Incrementais:

Para minimizar custos, use backups incrementais, onde apenas as alterações são salvas após o backup inicial.


*----Cálculo de Custos (Estimativa com AWS Calculator)-----*
|Service           |Price (Monthly)    |Region          |
|------------------|-------------------|-----------------
|MGN               |$ 0,00             |North Virginia  |
|EC2 (MGN)         |$ 42,47            |North Virginia  |
|EC2 (React)       |$ 16,18            |North Virginia  |
|EC2 (Back-end)    |$ 28,45            |North Virginia  |
|RDS               |$ 293,34           |North Virginia  |
|DMS               |$ 169,92           |North Virginia  |
|VPC               |$ 104,48           |North Virginia  |
|**Total**            |**$ 000,00**


*Validação:*
DNS Temporário
Aponte um subdomínio para o IP público do EC2 Frontend.
Verifique se o backend e o RDS estão respondendo corretamente.
Checar Logs e Performance
Monitorar a saúde das instâncias EC2 (CPU, memória) e do RDS (latência, conexões).
Verificar se APIs, frontend e DB estão funcionando sem erros.

*Pós-Cutover*  
- Atualizar DNS/IPs  
- Validar conectividade  
- Monitorar performance  

*Extras*

IAM:
Automatize a rotação de chaves KMS para criptografia de backups e banco de dados.
Implante o AWS Identity Center para gerenciar acessos temporários em grandes equipes.
Backup:
Configure alertas via Amazon CloudWatch para falhas de backup.
Use o AWS Backup Audit Manager para verificar conformidade com regulamentos e boas práticas.

------------------------------------------
*Resumo:*

Criar a VPC, sub-redes, grupos de segurança e roles IAM na AWS.
Configurar MGN na máquina de origem e na AWS, migrando servidores EC2.
Configurar DMS para migrar o banco de dados MySQL para o Amazon RDS.
Testar a infraestrutura migrada na AWS.
Monitorar e otimizar o desempenho após a migração