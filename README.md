 #  Projeto Linux AWS - Monitoramento de Servidor Web

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)


---


### Menu do Projeto:
- | [Menu do Projeto](#menu-do-projeto) | [Detalhes do Projeto](#detalhes-do-projeto) | [Objetivo do projeto](#objetivo-do-projeto) |
- | [Introdução](#introdução) | - [VPC](#vpc) | [EC2](#ec2) | 
- | [Servidor Web Nginx](#servidor-web-nginx) | [Webhook com Discord](#webhook-com-discord) | [Script de Monitoramento](#script-de-monitoramento) | [Automação com Cron](#automação-com-cron) |
- | [Testes](#testes) | [Observações](#observações) | [Experiencia e Considerações Finais](#experiencia-e-considerações-finais) |



---


### Detalhes do Projeto
- **Autor:** Luiz Henrique Gasparotto
- **Período:** 17/03 a 27/03
- **Ferramentas:**
  - Interface AWS Console
  - WSL (Windows Subsystem for Linux)
  - Ubuntu Server

### Objetivo do Projeto
Desenvolver habilidades práticas em:
- Administração de infraestrutura AWS
- Configuração de servidores web (Nginx)
- Automação de monitoramento com Bash


---

### Introdução
- Neste projeto pessoal, explorei a infraestrutura em nuvem da AWS e o ambiente Linux para criar um servidor web básico com monitoramento automatizado. Utilizei ferramentas como a Interface AWS e o WSL (Windows Subsystem for Linux) para configurar uma instância EC2, implantar um site estático com Nginx e desenvolver um script em Bash para verificar se o site estava no ar.

Minhas principais tarefas foram:
- Criar uma VPC com sub-redes públicas e privadas na AWS
- Configurar uma instância EC2 com Ubuntu Server
- Instalar e configurar o Nginx para hospedar uma página HTML simples
- Desenvolver um script de monitoramento que verifica a disponibilidade do site e registra logs
- Automatizar o processo usando CRON para execução periódica

O objetivo foi praticar administração de sistemas em nuvem, automação e configuração de servidores, desenvolvendo conhecimentos em AWS, Linux e Bash.


---


### VPC
Infraestrutura:
| Componente | Configuração |
|------------|--------------|
| **Bloco CIDR** | 10.0.0.0/16 |
| **Sub-redes Públicas** | 2 (em zonas de disponibilidade diferentes) |
| **Sub-redes Privadas** | 2 (para alta disponibilidade) |
| **Internet Gateway** | 1 (para acesso à internet) |

**Criação da VPC**
- Acessei a interface grafica da aws e procurei por VPC na barra de pesquisa
![Image](https://github.com/user-attachments/assets/8e317ab4-625e-4344-bedf-bd1f359f582f)
- Cliquei em create vpc, e parti para as configurações.
![Image](https://github.com/user-attachments/assets/b7b4180f-6138-4755-9152-83f550b09265)

### 2.3 Configuração da VPC:
  
  ![Image](https://github.com/user-attachments/assets/05d5fbdd-260d-4c3e-9130-21f3bf5af9f6)

**Minhas Escolhas:**
- VPC e outros recursos
  - Criei tudo completo (sub-redes, gateways) pra já ficar pronto pra usar -
- Nome automático "testesegunda"
  - Todos os recursos ficam com o mesmo prefixo, mais fácil de achar
- Bloco de IPs 10.0.0.0/16
  - Espaço grande (65 mil IPs) pra não faltar endereço
- Sem IPv6: Só usei IPv4 pra simplificar nesse projeto
- 2 Zonas de Disponibilidade:
  - O mínimo recomendado pra ter redundância básica
  
![Image](https://github.com/user-attachments/assets/c873eb99-906b-449c-9aff-efb626408399)

**Estrutura Básica:**
- Redes: Subnets & zonas
  - 2 Zonas (AZs): Redundância mínima
  - 2 Sub-redes públicas: Para servidores web (1 em cada AZ)
  - 2 Sub-redes privadas: Para bancos de dados (1 em cada AZ)

- Conexões: 
  - 1 NAT Gateway em AZ: Para permitir o acesso a internet pelas subnets.
  - Gateway S3: Acesso direto e seguro ao S3 (economiza custos)

Extra:
- DNS ativado: Para resolver nomes internos

**Visão Geral da VPC:**
![Image](https://github.com/user-attachments/assets/b4cf262f-5825-4b63-90ec-95fbc27a17ea)
- Observações:
  - O nome escolhido para vpc foi referente ao dia em que ela estava sendo testada, se aparecer outro nomenclatura para a vpc ou ec2 durante essa documentação não se importe, os nomes não alteram o processo do projeto.




   
---

### EC2

1. Na interface gráfica da AWS:
   - Pesquisei por "EC2" na barra de pesquisa  
     ![Image](https://github.com/user-attachments/assets/857aea70-9ef9-4053-be8b-fe1a41e3295d)
   - Selecionei "Launch Instance" para iniciar as configurações
     ![Image](https://github.com/user-attachments/assets/d5c532c4-0e10-46c8-96a7-1a806620112c)
---

### 3.2 Configurações da EC2

#### 3.2.1 Tipo de Imagem
- **Sistema Operacional**: Ubuntu (Amazon Machine Image)
    ![Image](https://github.com/user-attachments/assets/c08cdd3b-d90c-4767-96ed-cad38294a426)
- **Tipo de Instancia**: T2 micro. -> Escolhi por ser uma instancia mais simples, e estar disponivel no free tier
    ![Image](https://github.com/user-attachments/assets/598f29f1-1043-42f6-8b56-965274d3f8f1)
  
#### 3.2.2 Configurações de Rede
![Image](https://github.com/user-attachments/assets/7340bb9c-30e1-4e78-b92b-061724b4cfa1)
- **VPC**: 
  - Utilizei a VPC criada anteriormente
- **Subnet**:
  - Selecionada uma subnet pública
- **IP Público**:
  - Atribuição automática habilitada

---

### 3.3 Security Group

**Regras configuradas**:

| Porta | Protocolo | Origem               | Finalidade                |
|-------|-----------|----------------------|---------------------------|
| 22    | TCP       | Meu IP (ex: 189.5.0.1) | Acesso SSH seguro         |
| 80    | TCP       | 0.0.0.0/0            | Acesso HTTP para testes   |

![Image](https://github.com/user-attachments/assets/0845ab5c-d773-447e-aff1-410141718335)

---

### 3.4 Key Pair

- 3.4.1 **Criando Key pair e acessando via ssh**:
   - Gerada via console AWS  
     ![Image](https://github.com/user-attachments/assets/4b26d7cb-db0f-46a1-9d59-d721e441b377)

- 3.4.2 **Configuração no WSL**:
   - Arquivo transferido para o subsistema Linux  
     ![Image](https://github.com/user-attachments/assets/b8b307be-9ed6-4313-b5cb-c0337adc7b4e)

- 3.4.3 **Permissões**:
![Image](https://github.com/user-attachments/assets/46371724-1927-4250-a25c-90831425bd94)
   ```bash
   chmod 400 ~/path/to/my-key.pem
- 3.5 **Acessando a via ssh:**
    - 3.5.1 Primeiro peguei o IPV4:
    ![Image](https://github.com/user-attachments/assets/c269726f-fad5-4ff7-8885-fa16bd70c9f9)
    - Peguei o ipv4 publico para conecatar via ssh na instancia, a partir disso vou poder partir pras configurações.
- 3.6 **Acesso via console WSL:**
  
![image](https://github.com/user-attachments/assets/62a2c895-0954-4e46-a00a-5950c4293645)

- Utilezei esse comando pra acessar o ssh, com minha key pair e ipv4 publico da instancia.
  ```bash
    $ sudo ssh -i chaveteste.pem ubuntu@ipv4VaiAqui
  ```    
      
---


### Servidor Web Nginx

- **4.1 Instalando**:
  - Atualizei o sistema com:
    ```bash
    sudo apt-get update
    sudo apt-get upgrade -y
    ```
  - Instalei o Nginx usando:
    ```bash
    sudo apt-get install nginx -y
    sudo apt-get update -y
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```

---

- **4.2 Configurando**:
  - Utilizando o Nano adicionei meu HTML a ser exibido ao acessar o servidor. 
    ```bash
    sudo nano /var/www/html/index.html
    ```
  ![Image](https://github.com/user-attachments/assets/798751e3-c338-4548-ae6f-993d1b84bbbd)
    
   - O html pre-feito no visual studio code:
    ```bash
    <!DOCTYPE html>
  <html lang="pt-BR">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Projeto Linux AWS - Luiz Henrique Gasparotto</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            color: #333;
        }
        h1 {
            color: #2c3e50;
            border-bottom: 2px solid #3498db;
            padding-bottom: 10px;
        }
        h2 {
            color: #2980b9;
        }
        .info-box {
            background-color: #f8f9fa;
            border-left: 4px solid #3498db;
            padding: 15px;
            margin: 20px 0;
        }
        .section {
            margin-bottom: 30px;
        }
        .date {
            font-style: italic;
            color: #7f8c8d;
        }
        ul {
            padding-left: 20px;
        }
    </style>
  </head>
  <body>
    <header>
        <h1>Projeto Linux AWS</h1>
        <div class="info-box">
            <p><strong>Autor:</strong> Luiz Henrique Gasparotto</p>
            <p class="date"><strong>Período:</strong> 17/03 a 27/03</p>
            <p><strong>Ferramentas Utilizadas:</strong> Interface AWS e WSL (Subsystem Linux)</p>
        </div>
    </header>

    <main>
        <section class="section">
            <h2>Descrição do Projeto</h2>
            <p>Este projeto foi desenvolvido como parte de um processo de estágio, com o objetivo de explorar e desenvolver habilidades práticas em administração de infraestrutura AWS, configuração de servidores web e automação simples de monitoramento.</p>
            
            <p>A atividade consistiu em:</p>
            <ul>
                <li>Criar uma VPC com sub-redes públicas e privadas na AWS</li>
                <li>Configurar uma instância EC2 com Ubuntu Server para hospedar um site estático via Nginx</li>
                <li>Implementar um script de monitoramento em Bash que verifica periodicamente se o site está no ar e registra logs</li>
                <li>Fase de testes e ajustes para garantir que tudo funcione conforme o esperado</li>
            </ul>
        </section>

        <section class="section">
            <h2>Passo-a-Passo Realizado</h2>
            <ol>
                <li>
                    <strong>Configuração do Ambiente via Interface AWS:</strong>
                    <ul>
                        <li>Criação de uma VPC com 2 subnets públicas e duas subnets privadas</li>
                        <li>Criação de 1 Internet Gateway</li>
                    </ul>
                </li>
                <li>
                    <strong>Criação de uma Instância EC2 & Configurações:</strong>
                    <ul>
                        <li>Criação de instância EC2 com Ubuntu LTS</li>
                        <li>Configuração de Security Group com regras para SSH (porta 22) e HTTP (porta 80)</li>
                    </ul>
                </li>
                <li>
                    <strong>Configuração do Servidor Web:</strong>
                    <ul>
                        <li>Instalação do Nginx</li>
                        <li>Criação de página HTML simples</li>
                    </ul>
                </li>
                <li>
                    <strong>Script de Monitoramento + Webhook:</strong>
                    <ul>
                        <li>Desenvolvimento de script para verificação periódica</li>
                        <li>Configuração do CRON para execução automática</li>
                    </ul>
                </li>
                <li>
                    <strong>Testes e Documentação</strong>
                </li>
            </ol>
        </section>

        <section class="section">
            <h2>Considerações Finais</h2>
            <ul>
                <li>Certifique-se de que todas as configurações de segurança estejam corretas</li>
                <li>Mantenha o repositório atualizado com as últimas alterações e documentações</li>
                <li>Nunca armazene credenciais ou informações sensíveis no código</li>
            </ul>
        </section>
    </main>

    <footer>
        <p>Projeto desenvolvido para fins educacionais e de aprendizado prático.</p>
        <p class="date">Março, 2024</p>
    </footer>
    </body>
    </html>
    ```

    - Reiniciei o serviço:
    ```bash
    sudo systemctl restart nginx
    ```
   - E agora o site está online e com o html alterado.
 
---

- **4.4 Acessando**:
  - Acessei via:
    ```bash
    http://<IP_PUBLICO_DA_INSTANCIA> 
    ```
    ```bash
    http://localhost 
    ```
![image](https://github.com/user-attachments/assets/3b0097c8-da42-4b13-ab68-d1c29628b5fd)
  
---

### Configuração do SystemD
- Editei o serviço do nginx para garantir reinicilização automatica:
```bash 
   $ sudo nano /usr/lib/systemd/system/nginx.service
```
- Com arquivo aberto editei a seção [Service] adicionando a reinicialização automatica:
```bash 
   Restart=always
   RestartSec=7s
```
- Utilizei esses dois comandos: O primeiro para reiniciar as configurações do nginx.service e o segundo para reiniciar o programa, aplicando as configurações.
```bash 
   $ sudo systemctl daemon-reload
   $ sudo systemctl restart nginx
```
- Testei usando esses comandos:
```bash 
  $ sudo systemctl status nginx
  $ sudo pkill -9 nginx
  $ sudo systemctl status nginx
```

---

### Webhook com Discord
 - Antes de começar a criar e usar o script de monitoramento, fiz a integração do webhook no discord:
 - Comecei criando um servidor pessoal e criei dois canais de texto, um em que fiz testes do script localmente e o outro para atender a instancia EC2 que estou utilizando no projeto
![image](https://github.com/user-attachments/assets/53c3ac03-a345-4e5f-8d4d-e9d042b16b6c)
 - No canal de texto #monitora-aws, cliquei no icone configurações e adicionei a integração do webhook
![image](https://github.com/user-attachments/assets/42cc2b51-2ef6-438e-9995-df08c3ed2566)
 - Peguei o Url do webhook e adicionei ao script para criar a integração.

### Script de Monitoramento 

 - Acessei a pasta de usuario e criei o arquivo em que estará meu script.
   ```bash
   $ cd ~
   $ sudo nano monitoramento.sh
   ```
  - Adicionei meu script de monitoramento feito previamente ao arquivo:
    ```bash
    #!/usr/bin/env bash
    #==============================================================================================================#
    #--Script de Monitoramento de Servidor Web Nginx | Versão: 1.0.0 | autor: Luiz Henrique Gasparotto |===========#
    #==============================================================================================================#

    #==Declarando variaveis pro Script | Url do site | Arquivos de Log | Webhook com Discord==#
    SITE="http://localhost"
    LOGSITE="/var/log/monitoramento.log"
    WEBHOOK_DISC="https://discord.com/api/webhooks/1351565786560594060/YOQiWhNPl9Q0_gUDnNXj4a1-bjSJ-VbfhesI4ygLcmopa_Nq_7g8r8Ey_pWcyW_FaLcN"

    #==============================================================================================================#
    #==Inicio do Script==#

    if curl -s --head "$SITE" | grep "200 OK" > /dev/null; then
    # Online, registra no log
    echo "$SITE está no ar - $(date)" >> "$LOGSITE"

    # Qual a mensagem irá pro webhook?
    MSGDISC=" **Status do Nginx**: O servidor está online!\n\n"
    MSGDISC+=" **Monitoramento.sh Executado**: Verifique os logs em /var/log/monitoramento.log."

    # Mensagem  webhook
    curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
    else
    # Offline, Registra no log
    echo "$SITE está offline - $(date)" >> "$LOGSITE"

    # Qual a mensagem irá pro webhook
    MSGDISC="  **Status do Nginx**: O servidor está offline no momento...\n\n"
    MSGDISC+=" **Monitoramento.sh Executado**: Verifique os logs em /var/log/monitoramento.log.\n"
    MSGDISC+=" **Status do Nginx**: Serviço Nginx será reiniciado ..."

    # Mensagem webhook (Discord)
    curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
    fi

    #==Fim do Script==#
    #==============================================================================================================#
    ```
  - Explicação do Script: ADICIONAR AINDA

- Naveguei até a pasta padrão pra guardar logs e criei o arquivo monitoramento.log para guardar os logs do site.
```bash
  $ cd /var/log/
  $ touch monitoramento.log
```
- Para acessar o arquivo de log basta utilizar este comando:
```bash
  $ cat /var/log/monitoramento.log
```
### Automação com Cron
- Para automatizar a execução do script com o Cron, utilizei o seguinte comando para acessar suas configurações.
```bash
$ sudo crontab -e
```
- Dentro do arquivo adicionei esse comando, oque garante que o monitoramento será executado a cada 1 minuto.
```bash
$ */1 * * * * /home/ubuntu/monitoramento.sh
```

### Testes 

### Observações

### Experiencia e Considerações Finais
  






