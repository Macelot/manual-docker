# Manual Docker

## Sumário

- [1. Exemplos do comando `docker pull`](#1-exemplos-do-comando-docker-pull)
- [2. Diferença entre `Dockerfile` e `docker-compose.yml`](#2-diferença-entre-dockerfile-e-docker-composeyml)
- [3. Quando usar cada um?](#3-quando-usar-cada-um)

---

## 1. Exemplos do comando `docker pull`

### **1. Baixar uma imagem do Node.js para desenvolvimento frontend/backend**  
```sh
docker pull node:20
```
**Quando usar?**  
- Para desenvolver aplicações **Node.js** (React, Vue, Angular, Express.js, NestJS).  
- Ideal para criar contêineres para **builds e execução** de projetos Node.js.  

---

### **2. Baixar uma imagem do Nginx para servir aplicações web**  
```sh
docker pull nginx:latest
```
**Quando usar?**  
- Para hospedar aplicações web estáticas, como **React, Angular ou Vue.js**.  
- Como **proxy reverso** para APIs e serviços backend.  

---

### **3. Baixar o PostgreSQL para bancos de dados**  
```sh
docker pull postgres:16
```
**Quando usar?**  
- Para rodar um banco de dados **PostgreSQL** localmente em um contêiner.  
- Útil para aplicações que usam **ORMs como Sequelize, Prisma ou SQLAlchemy**.  

---

### **4. Baixar o MySQL para bancos de dados relacionais**  
```sh
docker pull mysql:8
```
**Quando usar?**  
- Quando precisar de um banco **MySQL** para testes e desenvolvimento.  
- Para rodar sistemas que utilizam MySQL, como **WordPress, Magento, Joomla**.  

---

### **5. Baixar o MongoDB para aplicações NoSQL**  
```sh
docker pull mongo:7
```
**Quando usar?**  
- Para desenvolver APIs com **MongoDB** e frameworks como **Mongoose**.  
- Aplicações que utilizam um banco de dados **NoSQL** para armazenar documentos JSON.  

---

### **6. Baixar o Redis para cache e filas de mensagens**  
```sh
docker pull redis:latest
```
**Quando usar?**  
- Para criar **cache em memória** para acelerar consultas e reduzir a carga do banco.  
- Como **fila de mensagens** em aplicações que utilizam **Celery, Sidekiq ou BullMQ**.  

---

### **7. Baixar uma imagem do Python para desenvolvimento de scripts e APIs**  
```sh
docker pull python:3.12
```
**Quando usar?**  
- Para desenvolver aplicações **Flask, Django** ou scripts em Python.  
- Para rodar **Jupyter Notebooks** para análise de dados e aprendizado de máquina.  

---

### **8. Baixar o OpenJDK para rodar aplicações Java**  
```sh
docker pull openjdk:21
```
**Quando usar?**  
- Para rodar aplicações Java, como **Spring Boot, Quarkus ou Micronaut**.  
- Para compilar e executar projetos **Maven ou Gradle** em um ambiente isolado.  

---

## 2. Diferença entre `Dockerfile` e `docker-compose.yml`

| Característica | Dockerfile | docker-compose.yml |
|--------------|------------|-----------------|
| **O que é?** | Um arquivo para **construir** uma imagem personalizada | Um arquivo para **orquestrar** múltiplos contêineres |
| **Finalidade** | Define **como construir** uma imagem Docker | Define **como rodar** os contêineres e suas conexões |
| **Principais comandos** | `FROM`, `RUN`, `COPY`, `WORKDIR`, `CMD`, `ENTRYPOINT` | `services`, `volumes`, `networks`, `depends_on`, `environment` |
| **Execução** | Usado com `docker build` para criar uma imagem | Usado com `docker-compose up` para rodar múltiplos serviços |
| **Uso ideal** | Quando você precisa **personalizar** um ambiente com bibliotecas, dependências, configurações | Quando precisa rodar **múltiplos contêineres juntos** (exemplo: app + banco de dados + cache) |

---

## 3. Quando usar cada um?

### **Quando usar só o `Dockerfile`?**
- Quando você precisa apenas de uma imagem personalizada, sem múltiplos serviços.

Exemplo:
```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "server.js"]
```

Execução:
```sh
docker build -t minha-app .
docker run -p 3000:3000 minha-app
```

### **Quando usar só o `docker-compose.yml`?**
- Quando você precisa apenas orquestrar serviços prontos, sem personalizar imagens.

Exemplo:
```yaml
version: "3"
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
```

Execução:
```sh
docker-compose up -d
```

### **Quando usar os dois juntos?**
- Quando você precisa de uma imagem personalizada **e** de serviços extras.

Exemplo:
**Dockerfile:**
```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "server.js"]
```

**docker-compose.yml:**
```yaml
version: "3"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
```

Execução:
```sh
docker-compose up --build
```

