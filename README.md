# Desafios-Docker

## 🟢 Fácil
### 1. Rodando um container básico
Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

🔹 Exemplo de aplicação: Use a landing page do TailwindCSS como site estático dentro do container.

### Resolução:
1. docker pull nginx
2. docker images
3. mkdir nginx
4. cd nginx
5. mkdir html
6. cd html
7. nano index.html
8. colar o conteúdo da landing pafe do TailwindCSS
9. nano docker file: FROM nginx:latest   COPY index.html /usr/share/nginx/html/index.html
10. docker run --name nginx -d -p 8080:80 meu-nginx
11. docker ps
12. ip a
13. abrir o navegador
14. IP:8080

### 2. Criando e rodando um container interativo
Inicie um container Ubuntu e interaja com o terminal dele.
🔹 Exemplo de aplicação: Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

### 3.Listando e removendo containers
Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
🔹 Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

### 4.Criando um Dockerfile para uma aplicação simples em Python
Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
🔹 Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.
