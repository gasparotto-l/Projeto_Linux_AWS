 #  Projeto Linux AWS - Monitoramento de Servidor Web

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)

---

### Detalhes do Projeto
- **Autor:** Luiz Henrique Gasparotto
- **Período:** 17/03 a 27/03
- **Ferramentas:**
  - Interface AWS Console
  - WSL (Windows Subsystem for Linux)
  - Ubuntu Server

### Objetivo
Desenvolver habilidades práticas em:
- Administração de infraestrutura AWS
- Configuração de servidores web (Nginx)
- Automação de monitoramento com Bash

---

### 1. Preparação 
Algumas preparações feitas antes do inicio do projeto:
- Criação de uma pagina html para utilização no site.
- Criação de um guia para realização do projeto, para reutilização.
- Registros(prints) do ambiente AWS antes da criação da VPC e EC2.


---
### 2.VPC(Virtual Private Cloud)
Infraestrutura:
| Componente | Configuração |
|------------|--------------|
| **Bloco CIDR** | 10.0.0.0/16 |
| **Sub-redes Públicas** | 2 (em zonas de disponibilidade diferentes) |
| **Sub-redes Privadas** | 2 (para alta disponibilidade) |
| **Internet Gateway** | 1 (para acesso à internet) |

**2.1 Criação da VPC**
- 2.1.1 Acessei a interface grafica da aws e procurei por VPC na barra de pesquisa
![Image](https://github.com/user-attachments/assets/8e317ab4-625e-4344-bedf-bd1f359f582f)
- 2.2 Cliquei em create vpc, e parti para as configurações.
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

## 3. Instância EC2

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


## 4. Instalação e configuração da página web (Nginx)

- **4.1 Instalando**:
  - Atualizei o sistema com:
    ```bash
    sudo apt-get update
    sudo apt-get upgrade -y
    ```
  - Instalei o Nginx usando:
    ```bash
    sudo apt-get install nginx -y
    ```
  ![Print Instalação](img/install-nginx.png)

---

- **4.2 Configurando**:
  - Criei o diretório do site:
  ![Image](https://github.com/user-attachments/assets/93a1432e-b404-42e8-940f-d3d31cbb78f9)
    ```bash
    sudo mkdir /var/www/sitelocal
    cd /var/www/sitelocal
    ```
  - Adicionei meu HTML:
  ![Image](https://github.com/user-attachments/assets/798751e3-c338-4548-ae6f-993d1b84bbbd)
    ```bash
    sudo nano index.html
   - Utilizei o nano para editar e adicionar meu html pre-feito no visual studio code.
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

---

- **4.3 Configurando Nginx**:
  - Removi o default:
    ```bash
    sudo rm /etc/nginx/sites-enabled/default
    ```

  - Criei nova configuração:
    ```bash
    sudo nano /etc/nginx/sites-available/site
    ```
   ![image](https://github.com/user-attachments/assets/adcc60ff-7546-47ab-b967-de77daaeb525)

  - Configurações do acesso ao site:
    ```bash
      server {
      listen 80 default_server;
      listen [::]:80 default_server;

      root /var/www/sitelocal;
      index index.html;

      server_name _;

      location / {
          try_files $uri $uri/ =404;
      }
    }
    ```
  
    - Reiniciei o serviço:
    ```bash
    sudo systemctl restart nginx
    ```
   - Agora o site está agora online e com o html alterado.
 

---

- **4.4 Acessando**:
  - Acessei via:
    ```bash
    http://<IP_PUBLICO>
    ```
![image](https://github.com/user-attachments/assets/3b0097c8-da42-4b13-ab68-d1c29628b5fd)
  
---

### 5. Script de Monitoramento & WebHook com Discord
 - Acessei a past /opt (Pasta padrão para programas adicionais do sistema).
   ```bash
   $ sudo cd opt/
   ```
 - Criei o arquivo
   ```bash
   $ sudo nano monitoramento.sh
   ```
  - Adicionei meu script de monitoramento ao arquivo:
![Image](https://github.com/user-attachments/assets/fcde86b9-8247-4c6e-b4a8-e765cc5f4f5e)
  - Explicação do Script:

- Criei um arquivo monitoramento.log para guardar os logs:
![image](https://github.com/user-attachments/assets/38152843-617f-4603-87d8-ac9b78270284)

- Executei o comando para ver se está funcionando:
  ![image](https://github.com/user-attachments/assets/9839bb44-383c-4f0a-b1bb-b1cd55ea17f1)
  -  A mensagem deve aparecer no discord verificando a disponibilidade do site
  ![image](https://github.com/user-attachments/assets/f2b59431-8618-4ce3-812d-5239af7a0480)
 
  






