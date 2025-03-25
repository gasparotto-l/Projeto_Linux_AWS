#  Projeto Linux AWS - Monitoramento de Servidor Web

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)

## 📑 Menu de Navegação


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
	![image](https://github.com/user-attachments/assets/2a3fd46f-a588-4c8c-b9e3-e19dcd88780d)
- 2.2 Cliquei em create vpc, e parti para as configuraçõesda VPC
   	 ![image](https://github.com/user-attachments/assets/4cdb9d1e-fb67-43e7-90ae-772768c1f83b)

- 2.3 Configuração da VPC:
  	imagem
  	imagem
  	imagem
---

### 3. Instancia EC2:
Na interface grafica da AWS pesquisei por EC2 na barra de pesquisa
  Imagem
 E selecionei Launch EC2 e parti para as configurações.

- 3.2 Configurações da Ec2:
	- 3.2.1 Selecionei o ubuntu como tipo de imagem de sistema operacional.
   	- 3.2.2 Começei as configurações de rede:
  		- 3.3: Selecionei minha vpc criada anteriormente
   	  	- 3.3.1 Selecionei uma das minhas subnets publicas
   	  	- 3.3.2 Habilitei o a Atribuição de IP público.
   	- 3.4: Criei um Security group para dar acesso ssh(porta 22) e http(porta 80) & Key pairs
   	  	- 3.4.1 Deixei o ssh vinculado apenas ao ip da minha maquina por questoes de seguranças, por ser o acesso as configurações da instancia.
   	  	  Imagem
   	  	- 3.4.1 Já o http está aberto para 0.0.0.0 para que possa ser acessado na internet por outras maquinas e em outros lugares. A medida foi tomada para fim de testes da conectividade do servidor web.
   	  	  Imagem
   	  	- 3.5 Criei uma Key pair na interface AWS, como medida de segurança, para o acesso ssh.
   	  	  	Imagem
   	  	  	- 3.5.1 Como estou usando o Wsl foi preciso mover a key para o subsistem por meio do explorador de arquivos.
   	  	  	Imagem
		- 3.6 Fui ate a pasta em que minha key pair estava e alterei as permissoes de acesso da para 400. Essa é uma medida de segurança para que essa key nao pode ser lida ou alterada por nenhum usuario, exceção ao root.
   	- 3.7 Acesso via ssh:
   	  	3.7.1 Utilizando o comando
###


### 3. Instalação e configuração da pagina web(Nginx)(#-servidor-web)<a name="-servidor-web"></a>

**1 . Instalando:**
  - Logado em minha instacia EC2, realizei primeiramente atualizações basicas do sistema
    - usei esses dois comandos: `$ sudo apt-get update` e `$ sudo apt-get upgrade` apos as atualizações serem realizadas está tudo pronto da realização da instalação do nginx
  - Instale o programa do nginx utilizando esses comandos:
	  `$ sudo apt-get install nginx -y`

**2 . Configurando:**
- 2.1 Criando pagina web:
	- Acessei o diretorio /var/www/ -> E criei um novo diretorio para alocar o html da minha pagina web '' $ mkdir sitelocal ''
	- criei um arquivo "index.html" e adicionei meu html pre-feito no visual code, ao arquivo: 
`$ sudo nano index.html`
  
**2.2 Configurando o caminho:**
- Exclui o arquivo default base do nginx e criei um novo arquivo de mesmo nome e adicionei as seguintes configurações:
	- [PRINT DA CONFIG]
- Ativei o site com " " e reiniciei o sistema do nginx para as configurações serem aplicados.
	- [PRINT DO SITE]
	- [PRINT DO STATUS NGINX]
- OBS: caso queira voce pode criar um arquivo de configuração unico para seu site ao inves de alterar o default.
	- [EXEMPLO]
  
**2.3 Acessando o site:**
- Acessei meu site pelo endereço de ipv4 publico vinculado a subnet " ".
- Também acessei por m	eu do dns publico da aws para verificar sua disponibilidade web via http.
[PRINTS DO ACESSO]
  
---





