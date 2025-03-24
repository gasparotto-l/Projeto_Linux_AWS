#  Projeto Linux AWS - Monitoramento de Servidor Web

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)

## 📑 Menu de Navegação
| Seção | Descrição |
|-------|-----------|
| [ℹ️ Informações Gerais](#-informações-gerais) | Detalhes sobre o projeto |
| [🔧 Configuração do Ambiente](#-configuração-do-ambiente) | Setup da infraestrutura AWS |
| [🖥️ Configuração da EC2](#-configuração-da-ec2) | Detalhes da instância |
| [🌐 Servidor Web](#-servidor-web) | Instalação e configuração do Nginx |
| [🔔 Monitoramento](#-monitoramento) | Script de verificação |
| [🧪 Testes](#-testes) | Validação do sistema |
| [📝 Considerações](#-considerações) | Observações finais |

---

## ℹ️ Informações Gerais <a name="-informações-gerais"></a>
[🔝 Voltar ao Menu](#-menu-de-navegação)

### 📌 Detalhes do Projeto
- **Autor:** Luiz Henrique Gasparotto
- **Período:** 17/03 a 27/03
- **Ferramentas:**
  - Interface AWS Console
  - WSL (Windows Subsystem for Linux)
  - Ubuntu Server

### 🎯 Objetivo
Desenvolver habilidades práticas em:
- Administração de infraestrutura AWS
- Configuração de servidores web (Nginx)
- Automação de monitoramento com Bash

---


## 🔧 Configuração do Ambiente <a name="-configuração-do-ambiente"></a>
[🔝 Voltar ao Menu](#-menu-de-navegação)

---

### 1. Preparação 
Algumas preparações feitas antes do inicio do projeto:
- Criação de uma pagina html para utilização no site.
- Registros(prints) do ambiente AWS antes da criação da VPC e EC2.


---
### 2. Arquitetura da VPC

| Componente | Configuração |
|------------|--------------|
| **Bloco CIDR** | 10.0.0.0/16 |
| **Sub-redes Públicas** | 2 (em zonas de disponibilidade diferentes) |
| **Sub-redes Privadas** | 2 (para alta disponibilidade) |
| **Internet Gateway** | 1 (para acesso à internet) |










---

