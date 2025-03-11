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
10. docker build -t nginx .
11. docker run --name nginx -d -p 8080:80 meu-nginx
12. docker ps
13. ip a
14. abrir o navegador
15. IP:8080

### 2. Criando e rodando um container interativo
Inicie um container Ubuntu e interaja com o terminal dele.

🔹 Exemplo de aplicação: Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

### Resolução: 
1. docker pull ubuntu
2. docker run -it ubuntu bash
3. apt update
4. apt install -y curl -> Instalei um pacote de forma interativa
5. exit
6. mkdir ubuntu
7. cd ubuntu
8. nano script.sh
9. #!/bin/bash
echo "Este é um script de exemplo."
10. nano dockerfile
FROM ubuntu
RUN apt-get update && apt-get install -y curl
COPY script.sh /usr/local/bin/script.sh
RUN chmod +x /usr/local/bin/script.sh
CMD ["/bin/bash", "/usr/local/bin/script.sh"]
11. docker buil -t ubuntu .
12. docker run --name ubuntu -d -p 8080:80 meu-ubuntu
13. docker ps
14. docker logs ubuntu
15. a mensagem do script deve aparecer
16. docker exec -it meu-ubuntu bash
17. /usr/local/bin/script.sh
18. curl --version

### 3.Listando e removendo containers
Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
🔹 Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

### 4.Criando um Dockerfile para uma aplicação simples em Python
Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
🔹 Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.
