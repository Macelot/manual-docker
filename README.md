# Manual Docker

## Sumário

- [1. Exemplos do comando `docker pull`](#1-exemplos-do-comando-docker-pull)
- [2. Diferença entre `Dockerfile` e `docker-compose.yml`](#2-diferença-entre-dockerfile-e-docker-composeyml)
- [3. Quando usar cada um?](#3-quando-usar-cada-um)
- [4. Trabalhando com PostgreSQL no Docker](#4-trabalhando-com-postgresql-no-docker)
- [5. Atividade prática: Criando um ambiente Docker](#5-atividade-prática-criando-um-ambiente-docker)

---

## 1. Exemplos do comando `docker pull`

### **1. Baixar uma imagem do Python para desenvolvimento**  
```sh
docker pull python:3.10
```
**Quando usar?**  
- Para desenvolver aplicações **Python**.  
- Ideal para criar contêineres para **scripts, APIs e aplicações web**.

---

### **2. Baixar uma imagem do PostgreSQL para bancos de dados**  
```sh
docker pull postgres:16
```
**Quando usar?**  
- Para rodar um banco de dados **PostgreSQL** localmente em um contêiner.  
- Útil para aplicações que usam **ORMs como SQLAlchemy**.

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
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Execução:
```sh
docker build -t minha-app .
docker run -p 5000:5000 minha-app
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
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

**docker-compose.yml:**
```yaml
version: "3"
services:
  app:
    build: .
    ports:
      - "5000:5000"
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

---

## 4. Trabalhando com PostgreSQL no Docker

### **Criando uma tabela e inserindo dados**
Após iniciar o contêiner do PostgreSQL com Docker, conecte-se ao banco de dados:
```sh
docker exec -it <id_do_container> psql -U user -d postgres
```

Criação de uma tabela chamada `clientes`:
```sql
CREATE TABLE clientes (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100)
);
```

Inserindo dados na tabela:
```sql
INSERT INTO clientes (nome, email) VALUES ('João Silva', 'joao@email.com');
INSERT INTO clientes (nome, email) VALUES ('Maria Souza', 'maria@email.com');
```

Consultando os dados inseridos:
```sql
SELECT * FROM clientes;
```

---

## 5. Atividade prática: Criando um ambiente Docker

### **Objetivo**
Os alunos deverão criar um ambiente Docker utilizando um contêiner para um banco de dados PostgreSQL e executar consultas via um script Python.

### **Instruções**
1. Criar uma pasta chamada `projeto-docker` e acessar o diretório:
   ```sh
   mkdir projeto-docker && cd projeto-docker
   ```
2. Criar os dois arquivos: Dockerfile:
```Dockerfile
# Usando uma imagem base do Python
FROM python:3.9-slim

# Definindo o diretório de trabalho dentro do container
WORKDIR /app

# Copiando o arquivo de requisitos para o container
COPY requirements.txt .

# Instalando as dependências
RUN pip install -r requirements.txt

# Copiando o código da aplicação para o container
COPY . .

# Expondo a porta 5000
EXPOSE 5000

# Comando para rodar a aplicação
CMD ["python", "app.py"]
```
 
e `docker-compose.yml` com o seguinte conteúdo:
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
   Abrir o programa Docker Desktop
   Testas seu deu certo até aqui: 
```
docker build -t meu-flask-app .
```   

---

4. Criar um script Python chamado `database.py` para conectar ao PostgreSQL:
   ```python
   import psycopg2

   conn = psycopg2.connect(
       dbname="postgres",
       user="user",
       password="password",
       host="localhost",
       port="5432"
   )

   cur = conn.cursor()
   cur.execute("CREATE TABLE IF NOT EXISTS clientes (id SERIAL PRIMARY KEY, nome VARCHAR(100), email VARCHAR(100))")
   cur.execute("INSERT INTO clientes (nome, email) VALUES ('Carlos Lima', 'carlos@email.com')")
   conn.commit()

   cur.execute("SELECT * FROM clientes")
   for row in cur.fetchall():
       print(row)

   cur.close()
   conn.close()
   ```
5. Executar o seguinte comando para rodar o banco de dados:
   ```sh
   docker-compose up -d
   ```
6. Executar o script Python para testar a conexão e inserção de dados:
   ```sh
   python database.py
   ```

### **Desafio**
- Criar uma nova tabela `produtos` e inserir alguns dados para teste.
- Consultar todos os produtos cadastrados.

