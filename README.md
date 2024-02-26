# Configurando uma API REST com domínio (AWS EC2, Nginx e CloudFlare)

## 1 - Requisitos necessários

Para este tutorial, você vai precisar ter os itens abaixo:
- Um domínio novo, pois, vamos usar a CloudFlare para gerenciar o DNS dele;
- Conta na CloudFlare já ativada e com email verificado;
- Conta na AWS já ativada, com conta de email e meio de pagamentos verificados.

## 2 - O que faremos nesse tutorial

- 2.1 Criaremos uma instancia EC2 free tier, que permite uso de 750 horas de máquina por mês de graça para novas contas, durante 1 ano;
- 2.2 Faremos a configuração de um servidor Nginx como proxy reverso, disponibilizando nossa API REST que irá rodar localmente através de um subdomínio do nosso domínio novo;
- 2.3 Faremos a configuração de DNS do nosso domínio no site onde ele foi comprado para que a gestão de DNS ocorra pelo gerenciador de DNS da CloudFlare;
- 2.4 Faremos a configuração usando SSL/TLS ponta a ponta, fazendo que nossas informações trafeguem de forma segura entre o cliente e o servidor;

## 3 - Mãos a obra!

Agora, vamos colocar em prática cada uma das tarefas acima. Acompanhe comigo a seguir.


### 3.1 - Criando uma instancia free tier na AWS
- Imagem Ubuntu 22.04 
- Servidor T2 (Free tier)
- Gerar um novo arquivo `.pem` para acessar remotamente o servidor
- Habilitar porta 80 (HTTP)
- Habilitar porta 443 (HTTPS)
- Deixar habilitado acesso de qualquer IP
- Conectar usando o arquivo `.pem`

### 3.2 - Atualizando o servidor
- sudo apt update
- sudo apt full-upgrade -y
- Dar ok nas mensagens sobre os pacotes
- sudo reboot

### 3.3 - Instando o nginx
- sudo apt update
- sudo apt install nginx -y 
- sudo systemctl status nginx

### 3.4 - Criando a configuração do sub domínio da API 
- sites-available
- nginx -t
- link simbolico sites-enabled
- restart do nginx
- teste da api

### 3.5 - Instalando o Certbot para gerar o certificados SSL/TLS
- instalação usando snap do certbot classic
- instalação do plugin para cloudflare

### 3.6 - Configurando o sub domínio na CloudFlare
- Configurando o DNS da CloudFlare para que o domínio seja gerenciado por ela
- Criando a entrada do tipo A para apontar o subdominio `api` para o servidor EC2 

### 3.7 - Gerando token de configuração para plugin do Certbot para CloudFlare
- Gerar token dentro da CloudFlare para gerenciar o DNS do domínio com validade de 1 dia
- Criar arquivo `.ini` com o token gerado

### 3.8 - Configurar entrada TXT para o teste ACME na geração do certificado
- Executar o comando de geração de certificado com certbot (seguir atentamente nessa etapa)
- Informar um email para receber notificação de quando o certificado está próximo de vencer
- Aceitar os termos para teste ACME
- Opcionalmente, aceitar receber emails sobre a Let's Encrypt
- Configurar um registro do tipo TXT com nome _acme e com o conteúdo gerado pelo certbot
- Reexecutar a geração do certificado

### 3.9 - Instalando o certificado para o sub domínio 
- sudo certbot install
- Selecionar o sub domínio que estamos configurando

### 3.10 - Habilitando o modo Full Encrypt na CloudFlare
- Acessar configuração SSL/TLS na CloudFlare e habilitar modo Full Encrypt

### 3.11 - Rodando a aplicação usando PM2
- Instalando o PM2 com npm
- Configurando nosso servidor para rodar via PM2
- Persistindo a configuração com pm2 save
- Configurar o PM2 como serviço
- Testar reboot da máquina 

### 3.12 - Considerações finais
- Mudança de IP ao fazer encerrar e inicializar uma instancia