# manual-docker
Aqui estão alguns exemplos do comando `docker pull` para diferentes situações e tipos de aplicações:  

---

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

### **9. Baixar a imagem do Alpine Linux para contêineres leves**  
```sh
docker pull alpine:latest
```
**Quando usar?**  
- Para criar imagens Docker **otimizadas e leves**.  
- Muito usado em aplicações de **microsserviços** e ambientes de produção.  

---

### **10. Baixar o Jenkins para CI/CD**  
```sh
docker pull jenkins/jenkins:lts
```
**Quando usar?**  
- Para configurar um **servidor de integração contínua (CI/CD)** em um contêiner.  
- Para executar **pipelines automatizados** de build, teste e deploy.  

---

### **11. Baixar TensorFlow para Machine Learning e IA**  
```sh
docker pull tensorflow/tensorflow:latest
```
**Quando usar?**  
- Para treinar e rodar modelos de **Deep Learning** sem precisar instalar dependências localmente.  
- Para desenvolvimento de **IA** com suporte a **GPU** (caso tenha uma placa NVIDIA).  

---
