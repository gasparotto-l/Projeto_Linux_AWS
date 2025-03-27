# Projeto Linux AWS - Monitoramento de Servidor Web

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)

---

### Menu do Projeto:
- [Menu do Projeto](#menu-do-projeto) | [Detalhes do Projeto](#detalhes-do-projeto) | [Objetivo do projeto](#objetivo-do-projeto)
- [Introdução](#introdução) | [VPC](#vpc) | [EC2](#ec2) 
- [Servidor Web Nginx](#servidor-web-nginx) | [Webhook com Discord](#webhook-com-discord) | [Script de Monitoramento](#script-de-monitoramento) | [Automação com Cron](#automação-com-cron)
- [Testes](#testes) | [Observações](#observações) | [Experiência e Considerações Finais](#experiência-e-considerações-finais)

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
Neste projeto pessoal, explorei a infraestrutura em nuvem da AWS e o ambiente Linux para criar um servidor web básico com monitoramento automatizado. Utilizei ferramentas como a Interface AWS e o WSL (Windows Subsystem for Linux) para configurar uma instância EC2, implantar um site estático com Nginx e desenvolver um script em Bash para verificar se o site estava no ar.

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
- Acessei a interface gráfica da AWS e procurei por VPC na barra de pesquisa
  ![Image](https://github.com/user-attachments/assets/2c856270-a623-41ee-b4e0-7e026c331764)
- Cliquei em "Create VPC" e parti para as configurações
  ![Image](https://github.com/user-attachments/assets/65f73e83-1263-400f-809f-26c28cec924c)

### Configuração da VPC:
![Image](https://github.com/user-attachments/assets/1d0880dd-2058-4877-b17e-505a301681b2)
  
**Minhas Escolhas:**
- VPC e outros recursos
  - Criei tudo completo (sub-redes, gateways) para já ficar pronto para usar
- Nome automático "testesegunda"
  - Todos os recursos ficam com o mesmo prefixo, mais fácil de encontrar
- Bloco de IPs 10.0.0.0/16
  - Espaço grande (65 mil IPs) para não faltar endereço
- Sem IPv6: Só usei IPv4 para simplificar neste projeto
- 2 Zonas de Disponibilidade:
  - O mínimo recomendado para ter redundância básica
  
![Image](https://github.com/user-attachments/assets/b992d9bf-97b3-4867-a634-8b82d37d4832)

**Estrutura de Redes:**
- Redes: Subnets & zonas
  - 2 Zonas (AZs): Redundância mínima
  - 2 Sub-redes públicas
  - 2 Sub-redes privadas

- Conexões: 
  - Sem NAT Gateway em AZ: o acesso à internet para as subnets é irrelevante para esse projeto
  - Desativei o gateway S3 Acesso, só tem utilidade se estiver utilizando um NAT gateway

Extra:
- DNS ativado: Para resolver nomes internos

**Visão Geral da VPC:**
![Image](https://github.com/user-attachments/assets/1853bbd8-3622-4280-9eb3-a66d417982c5)

- Observações:
  - O nome escolhido para VPC foi referente ao dia em que ela estava sendo testada. Se aparecer outra nomenclatura para a VPC ou EC2 durante esta documentação, não se preocupe, os nomes não alteram o processo do projeto.

---

### EC2
- **Na interface gráfica da AWS:**
   - Pesquisei por "EC2" na barra de pesquisa  
    ![Image](https://github.com/user-attachments/assets/1e327aca-1945-4955-88e1-cfc6ce77e8fb)
   - Selecionei "Launch Instance" para iniciar as configurações
    ![Image](https://github.com/user-attachments/assets/c724fead-0bdc-4b89-a595-7b1f462f2a94)

---

### Configurações da EC2

#### Tipo de Imagem
- **Sistema Operacional**: Ubuntu (Amazon Machine Image)
    ![Image](https://github.com/user-attachments/assets/a8f27d44-7360-476b-9d6a-35209b39afe6)
- **Tipo de Instância**: T2 micro -> Escolhi por ser uma instância mais simples e estar disponível no free tier
    ![Image](https://github.com/user-attachments/assets/9f4345e7-843b-4b2d-aeab-8b5eb5fa5667)
  
#### Configurações de Rede
![Image](https://github.com/user-attachments/assets/b993225d-61c4-4818-a936-a5b452f6bcbf)
- **VPC**: 
  - Utilizei a VPC criada anteriormente
- **Subnet**:
  - Selecionada uma subnet pública
- **IP Público**:
  - Atribuição automática habilitada

---

### Security Group

**Regras configuradas**:

| Porta | Protocolo | Origem               | Finalidade                |
|-------|-----------|----------------------|---------------------------|
| 22    | TCP       | Meu IP               | Acesso SSH seguro         |
| 80    | TCP       | 0.0.0.0/0            | Acesso HTTP Web para testes |

![Image](https://github.com/user-attachments/assets/cb0b90b6-00b3-40e4-8fdf-8a9ef2e72abe)

---

### Key Pair

- **Criando Key pair e acessando via SSH**:
   - Gerada via console AWS  
   ![Image](https://github.com/user-attachments/assets/a909de3c-3f7e-4f3b-8b12-a403ebf00e28)

- **Configuração no WSL**:
   - Arquivo transferido para o subsistema Linux  
   ![Image](https://github.com/user-attachments/assets/cb21132a-766f-4fca-a0dc-962ded61d6fd)

- **Permissões**:
  ![Image](https://github.com/user-attachments/assets/46371724-1927-4250-a25c-90831425bd94) 
   ```bash
   chmod 400 ~/path/to/my-key.pem
- **Acessando a via ssh:**
    - Primeiro peguei o IPV4:
    ![Image](https://github.com/user-attachments/assets/9347968c-abfd-4860-9eda-cdc70990b92f)
    - Peguei o ipv4 publico para conecatar via ssh na instancia, a partir disso vou poder partir pras configurações.
- **Acesso via console WSL:**
  
![image](https://github.com/user-attachments/assets/62a2c895-0954-4e46-a00a-5950c4293645) 

- Utilizei esse comando pra acessar o ssh, com minha key pair e ipv4 publico da instancia.
  ```bash
    $ sudo ssh -i chaveteste.pem ubuntu@ipv4VaiAqui
  ```    


      
---



### Servidor Web Nginx

- **Instalando**:
  - Atualizei o sistema com:
    ```bash
    sudo apt-get update
    sudo apt-get upgrade -y
    ```
  - **Instalei o Nginx usando:**
    ```bash
    sudo apt-get install nginx -y
    sudo apt-get update -y
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```


---



- **Configurando**:
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



- **Acessando**:
  - Acessei via:
    ```bash
    http://<IP_PUBLICO_DA_INSTANCIA> 
    ```
    ```bash
    http://localhost 
    ```
**Site Web Online**
![Image](https://github.com/user-attachments/assets/8a5a2a19-b066-4979-9437-38ad7d11fd85)


  
---



### Configuração do SystemD
- Editei o serviço do nginx para garantir reinicialização automatica:
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



### Estrutura do Nginx:
 - Configurações gerais do nginx
 ```bash
    /etc/nginx/nginx.conf 
 ```
 - Configurações de sites disponiveis
 ```bash
    /etc/nginx/sites-available/
 ```
 - Configurações dos sites ativados
 ```bash
    /etc/nginx/sites-enabled/
 ```  
 - Configuração do serviço do Nginx
 ```bash
    /lib/systemd/system/nginx.service
 ```    
- Organização Geral:
```bash
    /
├── etc/
│   ├── nginx/
│   │   ├── nginx.conf
│   │   ├── sites-available/
│   │   └── sites-enabled/
│   └── systemd/
│        └── system/
│             └── nginx.service
│       
│         
│
├── var/
│   ├── www/
│   │   └── html/
│   │       └── index.html
│   └── log/
│       ├── nginx/
│       │   ├── access.log
│       │   └── error.log
│       └── MonitoraWeb.log
│
└── home/
    └── ubuntu/
        └── MonitoraWeb.sh

 ``` 




---

### Webhook com Discord
 - Antes de começar a criar e usar o script de monitoramento, fiz a integração do webhook no discord:
 - Comecei criando um servidor pessoal e criei dois canais de texto, um em que fiz testes do script localmente e o outro para atender a instancia EC2 que estou utilizando no projeto
 ![Image](https://github.com/user-attachments/assets/2d5d98a2-5581-4d6f-8564-f8db90f676e2)
 - No canal de texto #monitora-aws, cliquei no icone configurações e adicionei a integração do webhook
![Image](https://github.com/user-attachments/assets/a7e6aba8-3d7e-42be-bde2-a985707e71cf)
 - Peguei o Url do webhook e adicionei ao script para criar a integração.

---

### Script de Monitoramento 

 - Acessei a pasta de usuario e criei o arquivo em que estará meu script.
   ```bash
   $ cd ~
   $ sudo nano MonitoraWeb.sh
   ```
  - Adicionei meu script de monitoramento feito previamente ao arquivo:
    ```bash
    #!/usr/bin/env bash
    #==============================================================================================================#
    #--Script de Monitoramento de Servidor Web Nginx | Versão: 1.0.0 | autor: Luiz Henrique Gasparotto |===========#
    #==============================================================================================================#

    #==Declarando variaveis pro Script | Url do site | Arquivos de Log | Webhook com Discord==#
    SITE="http://localhost" #Ou ipv4 publico da instancia
    LOGSITE="/var/log/MonitoraWeb.log" 
    WEBHOOK_DISC="https://discord.com/api/webhooks/1351565786560594060/YOQiWhNPl9Q0_gUDnNXj4a1-bjSJ-VbfhesI4ygLcmopa_Nq_7g8r8Ey_pWcyW_FaLcN"

    #==============================================================================================================#
    #==Inicio do Script==#

    if curl -s --head "$SITE" | grep "200 OK" > /dev/null; then
    # Online, registra no log
    echo "$SITE está no ar - $(date)" >> "$LOGSITE"

    # Qual a mensagem irá pro webhook?
    MSGDISC=" **Status do Nginx**: O servidor está online agora!\n\n"
    MSGDISC+=" **MonitoraWeb Executado**: Verifique os logs em /var/log/MonitoraWeb.log"

    # Mensagem  webhook
    curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
    else
    # Offline, Registra no log
    echo "$SITE está offline - $(date)" >> "$LOGSITE"

    # Qual a mensagem irá pro webhook
    MSGDISC="  **Status do Nginx**: O servidor está offline no momento...\n\n"
    MSGDISC+=" **MonitoraWeb Executado**: Verifique os logs em /var/log/MonitoraWeb.log.\n"
    MSGDISC+=" **Status do Nginx**: Serviço Nginx será reiniciado ..."

    # Mensagem webhook (Discord)
    curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
    fi

    #==Fim do Script==#
    #==============================================================================================================#
    ```
    
### Explicação do Script:
-  Visão geral do Script:
   - Este script verifica periodicamente se o servidor Nginx está respondendo corretamente (status HTTP 200). Caso contrário, ele registra o incidente em um arquivo de log e envia uma notificação para um canal Discord através de     um webhook.

- Variaveis:
  ```bash
  SITE="http://localhost(Ou IPv4 público da instancia)"  # URL do site a ser monitorado
  LOGSITE="/var/log/MonitoraWeb.log"  # Arquivo de log para registros
  WEBHOOK_DISC="https://discord.com/api/webhooks/..."  # Webhook do Discord
  ```
### Explicação detalhada do Script:

**Shebang e Cabeçalho:**
```bash
#!/usr/bin/env bash
```
- `#!/usr/bin/env bash`: Especifica que o script deve ser executado pelo interpretador Bash.

**Verificação de Status do Site:**
```bash
if curl -s --head "$SITE" | grep "200 OK" > /dev/null; then
```

  - `curl -s --head "$SITE"`: Faz uma requisição HTTP HEAD silenciosa (-s) para a URL.
  - `grep "200 OK"`: Filtra a saída procurando pelo status HTTP 200(esse número representa sucesso na saída).
  - `> /dev/null`: Redireciona a saída para o "buraco negro" do Linux (descarta a saída).
  - `if...then`: Estrutura condicional que executa o bloco seguinte se o comando retornar sucesso.

**Registro em Log (Online)**
```bash
echo "$SITE está no ar - $(date)" >> "$LOGSITE"
```
  - `echo`: Para exibir a mensagem no terminal.
  - `$(date)`: Inserir a data/hora atual.
  - `>>`: Redireciona a saída para o final do arquivo.
  - `$LOGSITE`: Variável que contém o caminho do arquivo de log.

**Construção da Mensagem para Discord (Online)**
```bash
MSGDISC=" **Status do Nginx**: O servidor está online agora!\n\n"
MSGDISC+=" **MonitoraWeb Executado**: Verifique os logs em /var/log/MonitoraWeb.log"
```

  - `MSGDISC=`: Atribuição de string à variável.
  - `\n`: Caractere de nova linha.
  - `+=`: Concatenação de strings à variável existente.

**Envio para Webhook Discord (Online)**
```bash
curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
```

  - `curl -X POST`: Envia uma requisição HTTP POST.
  - `-X POST `: Indica que é uma requisição do tipo POST
  - `-H "Content-Type: application/json"`: Define o cabeçalho HTTP.
  - `-d "{\"content\":\"$MSGDISC\"}"`: Corpo da requisição em JSON.
  - `$WEBHOOK_DISC`: Variável com URL do webhook.

**Bloco Else (Offline)**

```bash
else
```

- Entra neste bloco quando o `if` anterior falha (status não é 200 OK).
- Registro em Log (Offline)

```bash
echo "$SITE está offline - $(date)" >> "$LOGSITE"
```

- Similar ao online, mas registra status offline.

**Construção da Mensagem para Discord (Offline)**

```bash
MSGDISC=" **Status do Nginx**: O servidor está offline no momento...\n\n"
MSGDISC+=" **MonitoraWeb Executado**: Verifique os logs em /var/log/MonitoraWeb.log.\n"
MSGDISC+=" **Status do Nginx**: Serviço Nginx será reiniciado ..."
```

  - Mensagem mais detalhada sobre o status offline.
  - Envio para Webhook Discord (Offline)

```bash
curl -X POST -H "Content-Type: application/json" -d "{\"content\":\"$MSGDISC\"}" "$WEBHOOK_DISC"
```

  - Igual ao online, mas envia a mensagem de offline.

 **Estrutura de Controle:**

```bash
fi
```

- Fecha o bloco condicional `if` iniciado no começo.


---


### Criação dos Logs
  
- Naveguei até a pasta padrão pra guardar logs e criei o arquivo monitoramento.log para guardar os logs do site.
```bash
  $ cd /var/log/
  $ touch MonitoraWeb.log
```
- Para acessar o arquivo de log basta utilizar este comando:
```bash
  $ cat /var/log/MonitoraWeb.log
```



---

### Automação com Cron
- Para automatizar a execução do script com o Cron, utilizei o seguinte comando para acessar suas configurações.
```bash
$ sudo crontab -e
```
- Dentro do arquivo adicionei esse comando, oque garante que o monitoramento será executado a cada 1 minuto.
```bash
$ */1 * * * * /home/ubuntu/MonitoraWeb.sh
```

---


### Testes 
- Primeiro Teste: Executei meu script(sem cron) e verifiquei se fui notificado no discord.
   ![Image](https://github.com/user-attachments/assets/06bc248c-447d-4209-8fb9-8ffab6853e96)
   - Discord me notificou que o site está online, ou seja, script funcionando.
   ![Image](https://github.com/user-attachments/assets/551bb564-e0c2-438f-98ad-3520411643f7)
- Segundo Teste: Matei/Desativei o Nginx, executei meu script(sem cron) e verifiquei se fui notificado no discord.
   ![Image](https://github.com/user-attachments/assets/4fd56244-5a0e-4ce7-9471-64dc214c2a15)
   - Discord me notificou que o site está offline, ou seja, script funcionando.
   ![Image](https://github.com/user-attachments/assets/5a2c711f-dc75-4295-8ba5-0fce695c2e10)
- Terceiro Teste: No terceiro teste o cron já estava ativado e executando a verificação do script a cada um minuto
  ![Image](https://github.com/user-attachments/assets/71f14aa1-751d-47fd-8c50-543ee725c921)
  - Executei os comandos anteriores e graças ao systemd o site reiniciou e fui notificado, ous seja, tudo certo!
  ![Image](https://github.com/user-attachments/assets/ba878236-4b2c-43a1-8681-1ec99009a5d0)



### Observações:
  - Algumas nomenclaturas podem variar entre as seções pois os testes foram realizados em dias diferentes
  - A interface da AWS foi utilizada tanto em português quanto em inglês durante o projeto
  - Disponível para correções ou ajustes necessários

### Experiência e Considerações Finais:
Experiência enriquecedora no gerenciamento de infraestrutura cloud
Boa oportunidade para desenvolver habilidades em:
  - Administração AWS
  - Configuração de servidores web
  - Automação de tarefas
  - Projeto adequado para fins educacionais e portfólio
  






