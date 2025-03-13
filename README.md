# Desafios-Docker

## 🟢 Fácil
### 1. Rodando um container básico DEU CERTO 
Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

🔹 Exemplo de aplicação: Use a landing page do TailwindCSS como site estático dentro do container.

### Resolução:
``` bash
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
```

### 2. Criando e rodando um container interativo REVISAR
Inicie um container Ubuntu e interaja com o terminal dele.

🔹 Exemplo de aplicação: Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

### Resolução: 
``` bash
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
```

### 3.Listando e removendo containers DEU CERTO 
Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
🔹 Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

### Resolução: 
```bash
1. docker ps -a
2. docker stop ubuntu
3. docker rm ubuntu 
```

### 4.Criando um Dockerfile para uma aplicação simples em Python REVISAR
Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
🔹 Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.

### Resolução: 
``` bash 
1. docker pull python
2. mkdir flask-app
3. cd flask-app
4. nano app.py
```
```python
5. from flask import Flask
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class HelloWorld(Resource):
    def get(self):
        return {'message': 'Hello, World!'}

api.add_resource(HelloWorld, '/hello')

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
```bash
6. nano dockerfile 
Usando a imagem base do Python
FROM python:3.9-slim

Defina o diretório de trabalho no container
WORKDIR /app

Copie o código da aplicação para dentro do container
COPY app.py .

Instale o Flask diretamente no container
RUN pip install flask

Exponha a porta 5000
EXPOSE 5000

Comando para rodar a aplicação Flask
CMD ["python", "app.py"]

8. docker build -t flask-app . 
9. docker run -d -p 5000:5000 --name flask-container flask-app
10. ip a 
11. abrir o navegador
12. IP:5000
```

## 🟡 Médio
### 1. Criando e utilizando volumes para persistência de dados DEU CERTO 
Execute um container MySQL e configure um volume para armazenar os dados do banco de forma persistente.
🔹 Exemplo de aplicação: Use o sistema de login e cadastro do Laravel Breeze, que usa MySQL.

### Resolução:
```bash
1. docker pull mysql
2. docker run -d --name mysql-A -e MYSQL_ROOT_PASSWORD=Senha123 -p 3306:3306 -v /data/mysql-A:/var/lib/mysql mysql:latest
3. docker ps
4. docker exec -it mysql-A mysql -uroot -p
```

### Testando a resolução anterior
```bash
1. create database teste;
2. use teste;
3. create table usuarios (id int primary key, AUTO_INCREMENT, nome varchar(50));
4. insert into usuarios(nome) values ("Giovana");
5. exit
6. docker stop mysql-A
7. docker rm mysql-A
8. docker run -d --name mysql-B -e MYSQL_ROOT_PASSWORD=Senha123 -p 3306:3306 -v /data/mysql-A:/var/lib/mysql mysql:latest
9. docker ps
10. docker exec -it mysql-B mysql -uroot -p
11. show databases;
12. use teste;
13. select * from usuarios;
```

### 2. Criando e rodando um container multi-stage DEU CERTO 
Utilize um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.
🔹 Exemplo de aplicação: Compile e rode a API do Go Fiber Example dentro do container.

### Resolução
```bash
1. mkdir go
2. cd go
3. docker pull golang
4. dcoker pull alpine
5. docker images
6. nano app.go
```

```go 
7. package main
import (
    "fmt"
)

func main() {
  fmt.Println("Qual é o seu nome:? ")
  var name string
  fmt.Scanln(&name)
  fmt.Printf("Oi, %s! Eu sou a linguagem Go! ", name)
}
```
```bash
8. nano dockerfile 
FROM golang as exec

COPY app.go /go/src/app/

ENV GO111MODULE=auto

WORKDIR /go/src/app

RUN go build -o app.go .

FROM alpine

WORKDIR /appexec
COPY --from=exec /go/src/app/ /appexec
RUN chmod -R 755 /appexec
ENTRYPOINT ./app.go
9. docker image build . -t app-go:1.0
10. docker images 
11. o app-go deverá ser listado 
12. docker run -ti --name meuappOK app-go:1.0
```

### 3. Construindo uma rede Docker para comunicação entre containers RODOU MAS TEM QUE REVISAR O CÓDIGO
Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem.
🔹 Exemplo de aplicação: Utilize o projeto MEAN Todos para criar um app de tarefas usando Node.js + MongoDB.

### Resolução:
```bash
1. mkdir node-mongo
2. cd node-mongo
3. nano docker-compose.yml
4. version: '3.8'

services:
  mongo:
    image: mongo:4.4
    container_name: mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=giovana
      - MONGO_INITDB_ROOT_PASSWORD=teste
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
    networks:
      - minha-rede

  node:
    build: ./app-node
    container_name: node
    networks:
      - minha-rede
    environment:
      - MONGO_URI=mongodb://giovana:teste@mongo:27017/meu-banco?authSource=admin
    ports:
      - "3000:3000"
    depends_on:
      - mongo

networks:
  minha-rede:
    driver: bridge

volumes:
  mongo-data:


5. mkdir app-node
6. cd app-node
7. nano app.js
const express = require("express");
const mongoose = require("mongoose");

const app = express();

mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true
}).then(() => {
    console.log("Conectado ao MongoDB!");
}).catch((err) => {
    console.error("Erro de conexão com o MongoDB", err);
});

// Configuração de uma rota simples
app.get("/", (req, res) => {
    res.send("Olá, Mundo!");
});

// Definição da porta
const port = 3000;
app.listen(port, () => {
    console.log(`Servidor rodando na porta ${port}`);
});

7. nano dockerfile 

FROM node:14  
WORKDIR /app
COPY . .
RUN echo '{"name": "app-node","version": "1.0.0","main": "app.js","scripts": {"start": "node app.js"},"dependencies": {"express": "^4.17.1", "mongoose": "^5.10.9"}}' > package.json
RUN npm install
EXPOSE 3000
CMD ["node", "app.js"]

8. docker-compose up -d
9. ip a 
10. abrir navegador
11. ip:3000
```

### 4. Criando um compose file para rodar uma aplicação com banco de dados
Utilize Docker Compose para configurar uma aplicação Django com um banco de dados PostgreSQL.
🔹 Exemplo de aplicação: Use o projeto Django Polls App para criar uma pesquisa de opinião integrada ao banco.
