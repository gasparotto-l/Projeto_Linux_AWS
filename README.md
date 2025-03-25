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
	![imagem VPC](img/VPC1.png)
- 2.2 Cliquei em create vpc, e parti para as configuraçõesda VPC
   	 ![image](VPC2.png)

- 2.3 Configuração da VPC:
  	imagem
  	imagem
  	imagem
---

## 3. Instância EC2

1. Na interface gráfica da AWS:
   - Pesquisei por "EC2" na barra de pesquisa  
     ![Imagem Busca EC2](img/ec2-search.png)
   - Selecionei "Launch Instance" para iniciar as configurações

---

### 3.2 Configurações da EC2

#### 3.2.1 Tipo de Imagem
- **Sistema Operacional**: Ubuntu (Amazon Machine Image)

#### 3.2.2 Configurações de Rede
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

![Imagem Security Group](img/security-group.png)

---

### 3.4 Key Pair

1. **Criação**:
   - Gerada via console AWS  
     ![Imagem Key Pair](img/key-pair.png)

2. **Configuração no WSL**:
   - Arquivo transferido para o subsistema Linux  
     ![Imagem WSL Key](img/wsl-key.png)

3. **Permissões**:
   ```bash
   chmod 400 ~/path/to/my-key.pem

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
    ```bash
    sudo mkdir /var/www/sitelocal
    cd /var/www/sitelocal
    ```
  - Adicionei meu HTML:
    ```bash
    sudo nano index.html
    ```
    ![Print HTML](img/html-code.png)

---

- **4.3 Configurando Nginx**:
  - Removi o default:
    ```bash
    sudo rm /etc/nginx/sites-enabled/default
    ```
  - Criei nova configuração:
    ```bash
    sudo nano /etc/nginx/sites-available/sitelocal
    ```
    ![Print Config](img/nginx-config.png)
  - Reiniciei o serviço:
    ```bash
    sudo systemctl restart nginx
    ```
    ![Print Status](img/nginx-status.png)

---

- **4.4 Acessando**:
  - Acessei via:
    ```bash
    http://<IP_PUBLICO>
    ```
    ![Print Site](img/site-access.png)
  
---





