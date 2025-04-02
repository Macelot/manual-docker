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
caso já tenha instalado o Banco de dados PostgreSQL, vai dar uma mensagem de porta em uso. Para resolver pode colocar outra porta no computador local, por exemplo:
```yaml
    ports:
      - "5455:5432"
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

# Manual: Criando um Ambiente Docker com PostgreSQL e Python

## 🎯 Objetivo
Os alunos deverão criar um ambiente Docker utilizando um contêiner para um banco de dados PostgreSQL e executar consultas via um script Python.

---

## 🛠 Passo a Passo

### 1️⃣ Criar a pasta do projeto
Abra o terminal e execute:
```sh
mkdir projeto-docker && cd projeto-docker
```
Essa pasta será onde todos os arquivos do projeto serão armazenados.

---

### 2️⃣ Criar o arquivo `Dockerfile`
Dentro da pasta `projeto-docker`, crie um arquivo chamado `Dockerfile` e adicione o seguinte conteúdo:
```dockerfile
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
```
Este arquivo define uma imagem personalizada para rodar um aplicativo Python dentro do Docker.

---

### 3️⃣ Criar o arquivo `docker-compose.yml`
Crie um arquivo chamado `docker-compose.yml` na pasta `projeto-docker` e adicione o seguinte conteúdo:
```yaml
version: "3.8"
services:
  app:
    build: .
    volumes:
      - .:/app
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/postgres
    depends_on:
      - db
    ports:
      - "5001:5000"
  
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5455:5432"
```
Este arquivo define dois serviços:
- `app`: O aplicativo Python que será executado no Docker.
- `db`: O banco de dados PostgreSQL rodando no contêiner.

O mapeamento de portas garante que:
- O banco de dados estará disponível na porta **5455** no seu computador, mas **5432** dentro do Docker.
- O app rodará na porta **5001** no seu computador, mas **5000** dentro do Docker.

---

### 4️⃣ Criar o arquivo `requirements.txt`
Na pasta `projeto-docker`, crie um arquivo chamado `requirements.txt` e adicione:
```
flask
psycopg2
```
Isso garante que os pacotes necessários para rodar o app sejam instalados no contêiner.

---

### 5️⃣ Criar o script Python `database.py`
Crie um arquivo chamado `database.py` e adicione o seguinte código:
```python
import psycopg2
import os

DATABASE_URL = os.getenv("DATABASE_URL", "postgresql://user:password@db:5432/postgres")

conn = psycopg2.connect(DATABASE_URL)
cur = conn.cursor()

# Criar tabela clientes
cur.execute("""
    CREATE TABLE IF NOT EXISTS clientes (
        id SERIAL PRIMARY KEY,
        nome VARCHAR(100),
        email VARCHAR(100)
    )
""")

# Inserir um registro
cur.execute("INSERT INTO clientes (nome, email) VALUES ('Carlos Lima', 'carlos@email.com')")
conn.commit()

# Consultar registros
cur.execute("SELECT * FROM clientes")
for row in cur.fetchall():
    print(row)

cur.close()
conn.close()
```
Este script:
1. Conecta ao banco de dados PostgreSQL.
2. Cria a tabela `clientes` caso ainda não exista.
3. Insere um cliente no banco de dados.
4. Recupera e exibe todos os registros da tabela.

---

### 6️⃣ Subir o ambiente Docker
Agora que todos os arquivos foram criados, inicie o Docker e execute:
```sh
docker-compose up -d --build
```
Isso:
- Constrói e inicia os contêineres.
- O banco de dados será iniciado no contêiner `db`.
- O aplicativo Python será iniciado no contêiner `app`.

Verifique se os contêineres estão rodando:
```sh
docker ps
```

---

### 7️⃣ Executar o script `database.py`
Para rodar o script dentro do contêiner:
```sh
docker-compose run app python database.py
```
Isso executará o script dentro do ambiente Docker e mostrará os registros no banco.

---

## 🚀 Desafio
1. Criar uma nova tabela `produtos` com os campos `id`, `nome` e `preco`.
2. Inserir alguns produtos de exemplo.
3. Exibir todos os produtos cadastrados.

---

## 🔧 Comandos úteis
- Parar e remover os contêineres:
  ```sh
  docker-compose down
  ```
- Verificar logs dos contêineres:
  ```sh
  docker-compose logs
  ```

- Verificar logs do app:
  ```sh
  docker-compose logs app
  ```
  
- Acessar o contêiner do banco de dados e rodar comandos SQL:
  ```sh
  docker exec -it projeto-docker-db-1 psql -U user -d postgres
  ```

  

Agora seu ambiente Docker com PostgreSQL e Python está pronto! 🚀


-------

Para visualizar algo no navegador, é necessário que o `app.py` rode um servidor web, como Flask. Atualmente, o `database.py` apenas executa comandos no banco de dados, sem disponibilizar uma interface web.

Para visualizar no navegador, siga estes passos:

1. **Criar o arquivo `app.py`**  
   Crie um novo arquivo `app.py` na pasta `projeto-docker` e adicione:
   ```python
   from flask import Flask, jsonify
   import psycopg2
   import os

   app = Flask(__name__)

   DATABASE_URL = os.getenv("DATABASE_URL", "postgresql://user:password@db:5432/postgres")

   def get_db_connection():
       return psycopg2.connect(DATABASE_URL)

   @app.route('/')
   def home():
       return "API está rodando! Acesse /clientes para ver os dados."

   @app.route('/clientes')
   def get_clientes():
       conn = get_db_connection()
       cur = conn.cursor()
       cur.execute("SELECT * FROM clientes")
       clientes = cur.fetchall()
       cur.close()
       conn.close()

       return jsonify(clientes)

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=5000)
   ```
   Isso cria um servidor Flask com:
   - Rota `/` para indicar que a API está rodando.
   - Rota `/clientes` para listar todos os clientes cadastrados no banco.

2. **Atualizar o `Dockerfile`**  
   Ajuste o `CMD` no `Dockerfile` para rodar o `app.py`:
   ```dockerfile
   CMD ["python", "app.py"]
   ```

3. **Subir o ambiente Docker**  
   Rode os seguintes comandos para reconstruir e iniciar os contêineres:
   ```sh
   docker-compose down
   docker-compose up -d --build
   ```

4. **Acessar a API no navegador**  
   Agora, abra o navegador e acesse:
   - **`http://localhost:5001/`** → Verifica se a API está rodando.
   - **`http://localhost:5001/clientes`** → Lista todos os clientes cadastrados.

Dessa forma, será possível ver os dados no navegador! 🚀
Faça o mesmo para os produtos, isto é, crie a rota /produtos.

