# Docker - Guess Game

## Objetivo

Este projeto tem como objetivo executar uma aplicação utilizando o Docker, a projeto base é o jogo Guess Game, disponível em https://github.com/fams/guess_game

A solução foi estruturada para demonstrar o uso e orquestração de containers, comunicação entre serviços, persistência de dados e balanceamento de carga.

---

## Estrutura do projeto

A aplicação é composta pelos seguintes serviços:

* backend-1, backend-2, backend-3: instâncias da aplicação Flask responsáveis pela lógica do jogo
* postgres: banco de dados PostgreSQL utilizado para armazenamento dos dados
* frontend: container com NGINX responsável por servir o frontend e atuar como proxy reverso

O NGINX recebe todas as requisições e as distribui entre os backends.

---

## Arquitetura

A arquitetura segue um modelo simples de três camadas:

* camada de apresentação: NGINX (frontend)
* camada de aplicação: múltiplas instâncias do backend em Flask
* camada de dados: PostgreSQL

O fluxo de requisições ocorre da seguinte forma:

1. O cliente acessa a aplicação via navegador
2. O NGINX recebe a requisição
3. O NGINX encaminha a requisição para uma das instâncias do backend
4. O backend processa a lógica do jogo
5. Quando necessário, o backend acessa o banco de dados
6. A resposta retorna ao cliente pelo NGINX

---

## Comunicação entre serviços

Os containers se comunicam através de uma rede interna criada pelo Docker Compose.

Para acessar o banco de dados, o backend utiliza o nome do serviço como host:

```
FLASK_DB_HOST=postgres
```

Isso é necessário porque, dentro do ambiente Docker, cada container possui seu próprio ambiente isolado, e o uso de "localhost" não permite a comunicação entre containers diferentes.

---

## Como executar

Para executar o projeto, utilize o comando:

```bash
docker compose up --build
```

Após a inicialização dos containers, a aplicação estará disponível em:

```
Frontend: http://localhost:8080
Backend:  http://localhost:8080/api

```

---

## Limpezado Ambiente

Para parar os containers:

```
docker compose down

```

Para remover containers e volumes:

```
docker compose down -v

```

Para remover as imagens

```
docker compose down -v --rmi all

```


## Banco de dados

O PostgreSQL utiliza um volume para persistência de dados:

```yaml
volumes:
  postgres_data:
```

Dessa forma, os dados permanecem armazenados mesmo que os containers sejam removidos ou recriados.

---

## Resiliência

Os serviços foram configurados com a política:

```yaml
restart: unless-stopped
```

Isso garante que os containers sejam reiniciados automaticamente em caso de falha, aumentando a disponibilidade da aplicação.

---

## Balanceamento de carga

O NGINX foi configurado como proxy reverso com balanceamento de carga.

As requisições são distribuídas entre as três instâncias do backend:

* backend-1
* backend-2
* backend-3

Essa abordagem permite melhor distribuição de carga e simula um ambiente mais próximo de produção. 

---

## Atualização dos serviços

A arquitetura permite atualização independente de cada componente:

* backend: pode ser atualizado com rebuild da imagem
* frontend: pode ser atualizado separadamente
* banco de dados: pode ter a versão alterada diretamente no docker-compose

Isso facilita a manutenção e evolução do sistema.

---

## Decisões de design adotadas

### Uso de múltiplas instâncias do backend

Foram utilizadas três instâncias do backend para permitir o balanceamento de carga e simular escalabilidade horizontal.

Ressaltando que a utilização de 3 instâncias foi completamente arbitrária.

---

### Uso do NGINX como proxy reverso

O NGINX foi escolhido para centralizar o acesso à aplicação e distribuir as requisições entre os backends, além de servir o frontend.

---

### Uso do Docker Compose

O Docker Compose foi utilizado para simplificar a orquestração dos serviços e permitir que todo o ambiente seja iniciado com um único comando.

---

### Uso de volume no banco de dados

A utilização de volume garante a persistência dos dados, evitando perdas durante reinicializações ou recriações dos containers.

---

### Uso do nome do serviço como host

A comunicação entre containers foi feita utilizando o nome do serviço definido no docker-compose, o que é a abordagem correta dentro de redes Docker.

---

## Conclusão

O projeto atende aos seguintes requisitos:

* backend implementado em Flask
* banco de dados PostgreSQL com persistência
* NGINX atuando como proxy reverso
* balanceamento de carga entre múltiplas instâncias
* estrutura modular e de fácil manutenção

A solução é funcional, simples e adequada para demonstrar os conceitos propostos na atividade.
