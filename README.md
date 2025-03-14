# DevSecOps - Exercícios Docker 

## 🟢 Fácil
### 1. Rodando um container básico 
Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

🔹 Exemplo de aplicação: Use a landing page do TailwindCSS como site estático dentro do container.

### Resolução:
1. No seu computador, clone o repositório da landing page do TailwindCSS

``` bash
1. docker pull nginx
2. docker images
3. mkdir nginx
4. cd nginx
5. mkdir html
6. cd html
7. nano index.html
8. colar o conteúdo da landing page do TailwindCSS
9. nano docker file: 
    FROM nginx:latest   
    COPY index.html /usr/share/nginx/html/index.html
10. docker build -t nginx .
11. docker run --name nginx -d -p 8080:80 meu-nginx
12. docker ps
13. ip a
14. abrir o navegador
15. IP:8080
```

### 2. Criando e rodando um container interativo DEU CERTO MAS REVISAR
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
echo "Testando o script..."
10. nano dockerfile
FROM ubuntu
COPY script.sh /usr/local/bin/script.sh
RUN chmod +x /usr/local/bin/script.sh
CMD ["/bin/bash", "/usr/local/bin/script.sh"]
11. docker build -t ubuntu .
12. docker run --name meu-ubuntu -d -p 8080:80 ubuntu sleep 1500
13. docker ps
14. docker exec meu-ubuntu /bin/bash -c "/usr/local/bin/script.sh"
15. a mensagem do script deve aparecer
```

### 3.Listando e removendo containers 
Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.

🔹 Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

### Resolução: 
```bash
1. docker ps -a
2. docker stop <nome ou ID do container>
3. docker rm <nome ou ID do container>
```

### 4.Criando um Dockerfile para uma aplicação simples em Python 
Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.

🔹 Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.

### Resolução: 
Criei um aplicação que pergunta ao usuário o seu nome e depois exibido na tela.

Não entendi como usar a API de exemplo.

``` bash 
1. docker pull python
2. mkdir flask-app
3. cd flask-app
4. nano app.py
```
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
```bash
6. nano dockerfile 
FROM python:3.9-slim
WORKDIR /app
COPY app.py .
RUN pip install flask
EXPOSE 5000
CMD ["python", "app.py"]

8. docker build -t flask-app . 
9. docker run -d -p 5000:5000 --name flask-container flask-app
10. ip a 
11. abrir o navegador
12. IP:5000
```

## 🟡 Médio
### 5. Criando e utilizando volumes para persistência de dados 
Execute um container MySQL e configure um volume para armazenar os dados do banco de forma persistente.

🔹 Exemplo de aplicação: Use o sistema de login e cadastro do Laravel Breeze, que usa MySQL.

### Resolução:
Criei um container Docker e, dentro dele, configurei um banco de dados com uma tabela e inseri alguns dados. Posteriormente, apaguei esse container e criei um novo. Ao acessar o novo container, verifiquei se as informações ainda estavam disponíveis

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
3. create table usuarios (id int primary key AUTO_INCREMENT, nome varchar(50));
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

### 6. Criando e rodando um container multi-stage DEU CERTO E NÃO PRECISA REVISAR
Utilize um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.

🔹 Exemplo de aplicação: Compile e rode a API do Go Fiber Example dentro do container.

### Resolução
Nesse exercício criei uma aplicação que pergunta ao usuário o seu nome e depois exibe na tela uma mensagem com o nome informado.

O link do exemplo não foi encontrado. 

```bash
1. docker pull golang
2. docker pull alpine
3. docker images
4. mkdir go
5. cd go
6. nano app.go
```

```go 
package main
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
    CMD ./app.go
9. docker image build . -t app-go:1.0
10. docker images  
11. docker run -ti --name meuappOK app-go:1.0
```

### 7. Construindo uma rede Docker para comunicação entre containers 
Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem.

🔹 Exemplo de aplicação: Utilize o projeto MEAN Todos para criar um app de tarefas usando Node.js + MongoDB.

### Resolução:
Criei uma aplicação que exibe a mensagem 'Hello World!' no navegador. Além disso, no terminal, é exibida uma mensagem informando que o Node está rodando na porta especificada e que a conexão com o MongoDB foi estabelecida com sucesso.

```bash
1. mkdir node-mongo
2. cd node-mongo
3. nano docker-compose.yml
    version: '3.8'

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
    }).catch((error) => {
        console.error("Erro de conexão com o MongoDB", error);
    });

    app.get("/", (req, res) => {
        res.send("Olá, Mundo!");
    });

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
12. docker logs node
```

### 8. Criando um compose file para rodar uma aplicação com banco de dados
Utilize Docker Compose para configurar uma aplicação Django com um banco de dados PostgreSQL.

🔹 Exemplo de aplicação: Use o projeto Django Polls App para criar uma pesquisa de opinião integrada ao banco.

### Resolução:
```bash
1. git clone https://github.com/devmahmud/Django-Poll-App.git
2. cd Django-Poll-App
3. nano docker-compose.yml
    version: '3.8'

    services:
      db:
        image: postgres:13
        environment:
          POSTGRES_DB: db
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        volumes:
          - postgres_data:/var/lib/postgresql/data/

      web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
          - .:/code
        ports:
          - "8000:8000"
        depends_on:
          - db

    volumes:
      postgres_data:

4. nano dockerfile
    FROM python:3.9-slim
    ENV PYTHONUNBUFFERED=1
    WORKDIR /code
    COPY . /code/
    RUN pip install --no-cache-dir -r requirements.txt
    CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"] 

5. cd pollme
6. ALLOWED_HOSTS = ['IP']
7. DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'pollsdb',
        'USER': 'user',
        'PASSWORD': 'password',
        'HOST': 'db',
        'PORT': '5432',
    }
}

8. docker-compose up --build
```

8. abrir o navegador
9. IP:8000

```bash
10. python manage.py shell
11. python manage.py createsuperuser
12. from django.contrib.auth.models import User
13. User.objects.all()

```


```BASH
1. apt install python3-venv
2. python3 -m venv django
3. source django/bin/activate
4. pip install django
5. django-admin startproject desafio
6. cd desafio
7. python manage.py startapp polls
8. nano settings.py
    INSTALLED_APPS = [
        ...
        'polls',
    ]

9. cd ..
10. pip install psycopg2-binary
11. nano settings.py
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'db',
            'USER': 'felipe',
            'PASSWORD': '123',
            'HOST': 'db',
            'PORT': '5432',
        }
    }

12. cd desafio
13. nano docker-compose.yml
    version: '3.8'

    services:
      db:
        image: postgres:13
        volumes:
          - postgres_data:/var/lib/postgresql/data
        environment:
          POSTGRES_DB: db
          POSTGRES_USER: giovana
          POSTGRES_PASSWORD: teste
        networks:
          - minha-rede

      web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
          - .:/app
        ports:
          - "8000:8000"
        depends_on:
          - db
        networks:
          - minha-rede

    volumes:
      postgres_data:

    networks:
      minha-rede:


14. nano Dockerfile
    FROM python:3.9-slim
    WORKDIR /app
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt
    COPY . .
    EXPOSE 8000

15. nano requirements.txt
    Django==3.2
    psycopg2-binary==2.9.3

16. docker-compose up --build
17. Abra um novo terminal e navegue até o diretório desafio.

18. docker-compose exec web python manage.py migrate
19. Acessar a página do Django via navegador http://localhost:8000.

20. docker-compose exec db psql -U felipe -d db
```




## 🔴 Difícil

### 9. Criando uma imagem personalizada com um servidor web e arquivos estáticos DEU CERTO E NÃO PRECISA REVISAR 
- Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático.

🔹 Exemplo de aplicação: Utilize a landing page do Creative Tim para criar uma página moderna hospedada no container.

### Resolução:
```bash
1. mkdir apache
2. cd apache 
3. nano dockerfile 
    FROM httpd:alpine
    RUN apk update && apk add git
    RUN rm -rf /usr/local/apache2/htdocs/*
    RUN git clone https://github.com/creativetimofficial/material-kit.git /usr/local/apache2/htdocs
    EXPOSE 80
4. docker build -t apache-material-kit .
5. docker run -d -p 8080:80 apache-material-kit
```

6. abrir navegador
7. IP:8080