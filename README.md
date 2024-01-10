Atividade Docker Compass


# Atividade
![compass-uol-logo](https://github.com/Vynys/Atividade-Linux-Compass/assets/152445208/32377d62-dc80-430e-88d2-5e2fa0906f0e)

1. instalação e configuração do DOCKER
ou CONTAINERD no host EC2;
Ponto adicional para o trabalho utilizar
a instalação via script de Start Instance
(user_data.sh)
2. Efetuar Deploy de uma aplicação
Wordpress com:
container de aplicação
RDS database Mysql
3. configuração da utilização do serviço
EFS AWS para estáticos do container
de aplicação Wordpress
4. configuração do serviço de Load
Balancer AWS para a aplicação
Wordpress
Pontos de atenção:
não utilizar ip público para saída do
serviços WP (Evitem publicar o
serviço WP via IP Público)
sugestão para o tráfego de internet
sair pelo LB (Load Balancer Classic)
pastas públicas e estáticos do
wordpress sugestão de utilizar o
EFS (Elastic File Sistem)
Fica a critério de cada integrante
(ou dupla) usar Dockerfile ou
Dockercompose;
Necessário demonstrar a aplicação
wordpress funcionando (tela de
login)
Aplicação Wordpress precisa estar
rodando na porta 80 ou 8080;
Utilizar repositório git para
versionamento;
Criar documentação.

# Passo a Passo

Passo 1: Criar a VPC
- Na AWS busque por VPC.
- No menu de VPC clique em CRIAR VPC
- Após criar um VPC ainda no menu vá até Gateways NAT.
- Clique em criar gateway
- Nomeie o Nat Gateway e em Sub-rede selecione uma das sub-redes públicas.
- Mantenha Tipo de conectividade como público
- Em seguida clique em Criar gateway NAT.
- Após criar o NAT gateway, acesse Tabelas de rotas.
- Na tebela de rotas, selecione as rotas privadas, clique em ações e selecione Editar rotas, será necessário realizar isso para as duas rotas.
- Em Alvo selecione Gateway NAT e selecione o NAT gateway criado anteriormente 
- Clique em salvar alterações.
- Para verificar se sua BPC estgá corrte acesse Suas VPCs em seguida selecione a BPC criada anterioemente e a opção Resource Map.

Passo 2: Criar o EFS
- Busque por EFS na Amazon AWS o serviço de arquivo de NFS escalável da AWS
- Na página de EFS clique em Criar sistema de arquivos
- Selecione o SG criado para o EFS e finalize a criação
- Crie o Security Group do EFS

|       Type        | Protocol | Port Range | Source Type | Source |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| NFS | TCP | 2049  | Anywhere  | 0.0.0.0/0  |

Passo 3: Criar o RDS
- Busque por RDS na Amazon AWS.
- Na página de RDS clique em Create database.
- Em Engine options selecione MySQL
- Em Templates selecione Free tier
- Em Settings dê um nome identicador para o DB.
- Escolha um username.
- Escolha uma senha.
- Em Storage determine o tamanho do armazenamento do DB.
- Selecione a VPC criada.
- Habilite o acesso público.
- Selecione o SG-RDS.
- Dê um nome inicial ao DB
- Com o RDS criado, clique no nome do DB
- Aqui se encontra o Endpointdo RDS
- Crie o Security Group do RDS
  
|       Type        | Protocol | Port Range | Source Type | Source |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| MYSQL/Aurora | TCP | 3306  | Anywhere  | 0.0.0.0/0  |

Passo 4: Criar o Template 
- Na AWS busque por EC2
- No menu de EC2 clique em Launch Template.
- Configure o template com forme solicitado.
- No campo advanced details vá até o quadro do User data e cole o scrript
Criar o Security Group do EC2

|       Type        | Protocol | Port Range | Source Type | Source |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| SSH| TCP | 22  | Anywhere  | 0.0.0.0/0  |
| SHTTP| TCP | 80  | Custom  | SG-ALB  |

Passo 5: Criar o Target group
- No menu EC2 procure por Grupos de destino na barra de navegação à esquerda.
- Acesse e clique em Criar grupo de destino.
- Em Escolha um tipo de destino clique em Instâncias.
- Nomeie o grupo de destino.
- Em Protocolo mantenha HTTP e em Porta mantenha a porta 80.
- Como VPC selecione a VPC criada anteriormente.
- Mantenha a Versão do protocolo como HTTP1.
- A seguir clique em Próximo.
- Na página de Registrar destinos não selecione nenhuma instância.
- Selecione Criar grupo de destino.

Passo 6: Criar o Load Balancer
- No menu EC2 procure por load Balancer na barra de navegação à esquerda.
- Acesse e clique em Criar load balancer.
- Selecione Criar Application Load Balancer.
- Nomeie o load balancer.
- Em Esquema selecione Voltado para a internet.
- Em Tipo de endereço IP mantenha IPv4.
- Na aba Mapeamento de rede selecione a rede VPC.
- Selecione as duas subnets públicas criadas anteriormente.
- Como Grupo de segurança selecione o SG-ALB.
- Em Listeners e roteamento mantenha HTTP:80 e selecione o grupo de destino criado anteriormente.
  Clique em Criar load Balancer.
  Criar o Security Group do ALB
  
|       Type        | Protocol | Port Range | Source Type | Source |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| HTTP | TCP | 80  | Anywhere  | 0.0.0.0/0  |

Passo 7: Criar o Auto Scaling: 
- No menu EC2 procure por Auto Scaling na barra de navegação à esquerda.
- Acesse e clique em Criar grupo do Auto Scaling.
- Nomeio o grupo de Auto Scaling.
- Selecione o modelo de execução criado anteriormente.
- A seguir clique em Próximo.
- Selecione a VPC criada anteriormente.
- Selecione as Sub-redes Privadas.
- A seguir clique em Próximo.
- Marque a opção Anexar a um balanceador de carga existente.
- Marque a opção Escolha entre seus grupos de destino de balanceador de carga.
- Selecione o grupo de destino criado anteriormente.
- A seguir clique em Próximo.
- Em Tamanho do grupo selecione:
 Capacidade desejada: 2
Capacidade mínima: 2
Capacidade máxima: 3
- A seguir clique em Pular para a revisão.
- Clique em Criar grupo de auto Scaling.
