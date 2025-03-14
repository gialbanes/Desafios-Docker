# DevSecOps - Exercícios Docker 

## 🟢 Fácil
### 1. Rodando um container básico 
Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

🔹 Exemplo de aplicação: Use a landing page do TailwindCSS como site estático dentro do container.

### Resolução:
1. No seu computador, clone o repositório da landing page do TailwindCSS

Baixa a imagem do Nginx
``` bash
docker pull nginx
```
Verifica se a imagem do Nginx foi baixada 
```bash
docker images
```

Cria a pasta com o arquivo index.html. Dentro dele, cole o conteúdo da página que você quer exibir
```bash
mkdir nginx
cd nginx
mkdir html
cd html
nano index.html
```

Cria o arquivo dockerfile com o seguinte conteúdo:  
```bash
nano docker file
FROM nginx:latest   
COPY index.html /usr/share/nginx/html/index.html
```

Construa a imagem
```bash
docker build -t nginx .
```

Cria o container 
```bash
docker run --name nginx -d -p 8080:80 meu-nginx
```
Verifica o IP do container
```bash
ip a
```
No navegador, acesse a página pelo IP
- IP:8080

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
Lista todos os container:
```bash
docker ps -a
```

Para um container em execução 
```bash
docker stop <nome ou ID do container>
``` 

Remove um container
```bash
docker rm <nome ou ID do container>
```

### 4.Criando um Dockerfile para uma aplicação simples em Python 
Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.

🔹 Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.

### Resolução: 
Criei um aplicação que pergunta ao usuário o seu nome e depois exibido na tela.

Não entendi como usar a API de exemplo.

Baixa a imagem do python
``` bash 
docker pull python
```

Cria uma pasta e dentro dela a aplicação Python utilizando o Flask
```bash
mkdir flask-app
cd flask-app
nano app.py
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

Cria o dockerfile com o seguinte conteúdo:
```bash
nano dockerfile 
FROM python:3.9-slim
WORKDIR /app
COPY app.py .
RUN pip install flask
EXPOSE 5000
CMD ["python", "app.py"]
```

Construa a imagem
```bash
docker build -t flask-app . 
```

Cria o conteiner
```bash
docker run -d -p 5000:5000 --name flask-container flask-app
```
Verifica o IP
```bash
ip a 
```
No navegador, acesse a página pelo IP
- IP:5000

## 🟡 Médio
### 5. Criando e utilizando volumes para persistência de dados 
Execute um container MySQL e configure um volume para armazenar os dados do banco de forma persistente.

🔹 Exemplo de aplicação: Use o sistema de login e cadastro do Laravel Breeze, que usa MySQL.

### Resolução:
Criei um container Docker e, dentro dele, configurei um banco de dados com uma tabela e inseri alguns dados. Posteriormente, apaguei esse container e criei um novo. Ao acessar o novo container, verifiquei se as informações ainda estavam disponíveis

Baixa a imagem do MySQL
```bash
docker pull mysql
```

Cria um container com base na imagem do MySQL
```bash
docker run -d --name mysql-A -e MYSQL_ROOT_PASSWORD=Senha123 -p 3306:3306 -v /data/mysql-A:/var/lib/mysql mysql:latest
```

Entra no container criado 
```bash
docker exec -it mysql-A mysql -uroot -p
```

### Testando a resolução anterior
Cria um database, uma tabela e faz a inserção de dados nela 
```bash
create database teste;
use teste;
create table usuarios (id int primary key AUTO_INCREMENT, nome varchar(50));
insert into usuarios(nome) values ("Giovana");
exit
```

Para e apaga o container mysql-A
```bash
docker stop mysql-A
docker rm mysql-A
```

Cria outro container no mesmo volume do anterior, e entra nele
```bash
docker run -d --name mysql-B -e MYSQL_ROOT_PASSWORD=Senha123 -p 3306:3306 -v /data/mysql-A:/var/lib/mysql mysql:latest

docker exec -it mysql-B mysql -uroot -p
```

Pede para mostrar os databases existentes, logo, o "teste" estará presente
```bash
show databases;
use teste;
select * from usuarios;
```

### 6. Criando e rodando um container multi-stage DEU CERTO E NÃO PRECISA REVISAR
Utilize um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.

🔹 Exemplo de aplicação: Compile e rode a API do Go Fiber Example dentro do container.

### Resolução
Nesse exercício criei uma aplicação que pergunta ao usuário o seu nome e depois exibe na tela uma mensagem com o nome informado.

O link do exemplo não foi encontrado. 

Baixa a imagem do Go e Alpine
```bash
docker pull golang
docker pull alpine
```

Cria a pasta e o arquivo app.go dentro
```bash
mkdir go
cd go
nano app.go
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

Cria o arquivo dockerfile com o seguinte contúdo:
```bash
nano dockerfile 
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
```

Cria a imagem
```bash
docker image build . -t app-go:1.0
```

Executa o container com base na imagem criada
```bash
docker run -ti --name meuappOK app-go:1.0
```

### 7. Construindo uma rede Docker para comunicação entre containers 
Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem.

🔹 Exemplo de aplicação: Utilize o projeto MEAN Todos para criar um app de tarefas usando Node.js + MongoDB.

### Resolução:
Criei uma aplicação que exibe a mensagem 'Hello World!' no navegador. Além disso, no terminal, é exibida uma mensagem informando que o Node está rodando na porta especificada e que a conexão com o MongoDB foi estabelecida com sucesso.

Cria a pasta e o arquivo docker-compose.yml
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
```

Cria a pasta app-node dentro da node-mongo com o arquivo app.js
```bash
mkdir app-node
cd app-node
nano app.js
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
```

Cria o dockerfile com o seguinte conteúdo:
```bash
nano dockerfile 
    FROM node:14  
    WORKDIR /app
    COPY . .
    RUN echo '{"name": "app-node","version": "1.0.0","main": "app.js","scripts": {"start": "node app.js"},"dependencies": {"express": "^4.17.1", "mongoose": "^5.10.9"}}' > package.json
    RUN npm install
    EXPOSE 3000
    CMD ["node", "app.js"]
```

Inicia os containers definidos no docker-compose.yml em segundo plano
```bash
docker-compose up -d
```

Abra o navegador com IP:3000

Verifica os logs
```bash
docker logs node
docker logs mongo
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

Dentro da pasta do projeto, instale:
```BASH
apt install python3-venv
```

Crie o ambiente e entre nele
```bash
python3 -m venv django
source django/bin/activate
```

Instale o DJANGO
```bash
pip install django
```

Inicie o projeto e entre nele
``` bash 
django-admin startproject desafio
cd desafio
```

7. python manage.py startapp polls

Entre na pasta poll e adicione o seguinte dentro do arquivo settings.py
```bash
nano settings.py
    INSTALLED_APPS = [
        ...
        'polls',
    ]
```

Fora do diretório, instale o Psyco e edite o arquivo settings.py novamente
```bash
cd ..
pip install psycopg2-binary
nano settings.py
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
```

Dentro do projeto crie o docker-compose.yml
```bash
cd desafio

nano docker-compose.yml
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
```

Crie o arquivo dockerfile com o seguinte conteúdo:
```bash
nano Dockerfile
    FROM python:3.9-slim
    WORKDIR /app
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt
    COPY . .
    EXPOSE 8000
```

Crie o arquivo requirements.txt com o seguinte conteúdo:
```bash
nano requirements.txt
    Django==3.2
    psycopg2-binary==2.9.3
```

Construa o projeto
```bash
docker-compose up --build
```


Abra um novo terminal e navegue até o diretório desafio.

Migra o banco de dados dentro do container de acordo com as mudanças do Django
```bash
docker-compose exec web python manage.py migrate
```

Acessar a página do Django via navegador http://localhost:8000.

Por fim, faça o teste com o banco de dados:
```bash
docker-compose exec db psql -U felipe -d db
```




## 🔴 Difícil

### 9. Criando uma imagem personalizada com um servidor web e arquivos estáticos DEU CERTO E NÃO PRECISA REVISAR 
- Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático.

🔹 Exemplo de aplicação: Utilize a landing page do Creative Tim para criar uma página moderna hospedada no container.

### Resolução:
Baixa a imagem do apache
```bash
docker pull apache
```

Crie a pasta com o dockerfile e o seguinte conteúdo:
```bash
mkdir apache
cd apache 
nano dockerfile 
    FROM httpd:alpine
    RUN apk update && apk add git
    RUN rm -rf /usr/local/apache2/htdocs/*
    RUN git clone https://github.com/creativetimofficial/material-kit.git /usr/local/apache2/htdocs
    EXPOSE 80
```

Construa a imagem
```bash
docker build -t apache-material-kit .
```

Execute o container
```bash
docker run -d -p 8080:80 apache-material-kit
```

No navegador, acesse a página pelo IP
- IP:8080