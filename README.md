# Projeto Linux AWS
**Info:**
- Autor: Luiz Henrique Gasparotto
- Data: 17/03 a 27/03
- Ferramentas Utilizadas: Interface AWS e WSL (Subsystem Linux).
**Descrição do Projeto:**
- Este projeto foi desenvolvido como parte de um processo de estágio, com o objetivo de explorar e  desenvolver habilidades praticas em administração de infraestrutura AWS, configuração de servidores web e automação simples de monitoramento.

A atividade consistiu em:
- Criar uma VPC com sub-redes públicas e privadas na AWS.
- Configurar uma instância EC2 com Ubuntu Server para hospedar um site estático via Nginx.
- Implementar um script de monitoramento em Bash que verifica periodicamente se o site está no ar e registra logs.
- Fase de testes e ajustes para garantir que tudo funcione conforme o esperado.
  
**Passo-a-Passo Realizado:**

### 1. Configuração do Ambiente via Interface AWS:
   - **1.1: Criação de uma VPC:**
     - **1.2:** Com 2 subnets públicas e duas subnets privadas.
     - **1.3:** E 1 Internet Gateway.

### 2. Criação de uma Instância EC2 & Configurações:
   - **2.1:** Criar uma instância EC2 com o sistema operacional Ubuntu LTS ou Amazon Linux.
     - **2.1.1:** Essa instância deve estar configurada na sua VPC criada anteriormente, e em uma das subnets públicas.
   - **2.2: Security Group:**
     - **2.2.1:** Crie um security group com regras para acesso SSH (porta 22) e HTTP (porta 80).

### 3. Configuração do Servidor Web:
   - **3.1:** Conectar à instância EC2 via SSH.
   - **3.2:** Instalar o servidor Nginx:
     ```bash
     sudo apt-get update && apt-get upgrade
     sudo apt install nginx -y 
     ```
   - **3.3:** Criar uma página HTML simples:
     - Crie um arquivo `index.html` no diretório `/var/www/html/` com o conteúdo desejado.

### 4. Script de Monitoramento + Webhook:
   - **4.1:** Criar um script para verificar a disponibilidade do site a cada 1 minuto:
   - 
     ![image](https://github.com/user-attachments/assets/60c6644b-e005-42e8-8f07-55e5f81aa185)
    
   - **4.2:** Configurar o script para ser executado a cada minuto usando o `cron`:
     - Utilize o comando crontab -e
     - E adcione * * * * * /caminho/exemplo.sh 
     ![image](https://github.com/user-attachments/assets/3ce8fe2a-c518-4568-88bd-5a133d620986)
     - Observações:
       - Antes configurar o crontab eu criei um arquivo de log para o cron no meu usuario como mostrado na imagem, e indiquei o caminho em que o log sera gravado com ">>". Mas isso é opcional, só uma    
       questão de organização.

### 5. Testes e Documentação:
   - **5.1:** Testar a implementação:
     - Verifique se o site está acessível via navegador.
     - Verifique se o script de monitoramento está funcionando corretamente.
   - **5.2:** Documentar o processo de instalação e configuração no GitHub.
   - **5.3:** Cuidado com dados sensíveis, como chaves de API e credenciais.

### 6. Considerações Finais:
   - **6.1:** Certifique-se de que todas as configurações de segurança estejam corretas.
   - **6.2:** Mantenha o repositório atualizado com as últimas alterações e documentações.
