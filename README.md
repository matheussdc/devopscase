# Projeto Devops: Docker
Este projeto integra um backend desenvolvido em Ruby on Rails com um frontend React + Vite em repositórios separados utilizando containers Docker para orquestração.

---

## Requisitos

Tenha instalado:

- [Docker](https://www.docker.com/products/docker-desktop/) e [Docker Compose](https://docs.docker.com/compose/install/) para construção dos containers (Compose já está incluso no Docker Desktop).
- [Git](https://git-scm.com/downloads) para clonar os repositórios.

## Rodando os Containers Localmente

1. Clone o repositório deste projeto:
   ```
   git clone https://github.com/matheussdc/devopscase
   ```
2. Acesse a pasta raiz do projeto:
   ```
   cd devopscase
   ```
3. Clone os dois repositórios em suas pastas (`backend` e `frontend`):
   ```
   git clone https://github.com/matheussdc/devops-backend backend
   git clone https://github.com/matheussdc/devops-frontend frontend
   ```

   A estrutura dos diretórios deve estar assim:
   ```
   devopscase/
   ├── backend/            # Código do backend Rails
   │   └── ...
   ├── frontend/           # Código do frontend React
   │   └── ...
   ├── .gitignore
   ├── docker-compose.yml  # Orquestração dos serviços
   ├── Dockerfile.back     # Dockerfile do backend
   ├── Dockerfile.front    # Dockerfile do frontend
   └── README.md           # Documentação do projeto
   ```

4. Pela instrução 2 você deve estar na pasta principal onde está o arquivo `docker-compose.yml`. Construa e inicialize os containers:
   ```
   docker-compose up --build
   ```

5. Após a inicialização, os dois serviços estarão disponíveis para acesso local:
   - **Frontend**: http://localhost:80
   - **Backend**: http://localhost:3000

6. Para interromper os serviços apenas use `Ctrl+C` no terminal e então os containers irão parar. Se quiser remover os containers, imagens e o volume criados rode o seguinte:
   ```
   docker compose down -v --rmi all
   ```

---
## Integração

### Visão Geral

O **backend (Rails)** serve como intermédio de outra API externa, ele fornece API com sua própria logica em Rails na porta 3000. O **frontend (React)** consome consome os dados fornecidos pela API do backend pela porta 3000 enquanto serve o front pela porta 80. Os dois serviços são criados cada um em seu container pelo **Docker Compose** que também cria uma instância de banco de dados **PostgreSQL**.

### Containers

**Backend (Rails)** baseado na imagem `ruby:2.7.7-slim` pois é mais leve que a padrão `ruby:2.7.7`.

| Imagem Utilizada | Tamanho da Imagem construída|
|------------------|:---------------------------:|
| ruby:2.7.7       |1.53 GB                      |
| ruby:2.7.7-slim  |854 MB                       |

Container inicializa executando um script bash `entrypoint.sh` fornecido no repositório backend para checar a presença do banco de dados e rodar o servidor.

**Frontend (React)** feito em dois estágios para reduzir o tamanho da imagem, primeiro construído em imagem `node:20` e depois os arquivos são copiados para uma imagem com o servidor `nginx:1.27.3-alpine-slim`. Método mais leve do que só utilizar a imagem `node:20`.

| Imagem Utilizada | Tamanho da Imagem construída|
|-----------------------------------|:----------:|
| node:20                           |1.93 GB     |
| node:20 + nginx:1.26.2            |273 MB      |
| node:20 + nginx:1.27.3-alpine-slim|27 MB       |