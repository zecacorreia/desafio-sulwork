# Desafio Técnico Sulwork - Organizador de Café da Manhã

![Java](https://img.shields.io/badge/Java-17-blue)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-green)
![Angular](https://img.shields.io/badge/Angular-17-red)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue)
![Docker](https://img.shields.io/badge/Docker-blue)

Este projeto é a solução para o desafio técnico da Sulwork, que consiste em criar uma aplicação Full-Stack para organizar os itens que os colaboradores trarão para o café da manhã da empresa, evitando repetições e facilitando a gestão.

## Tabela de Conteúdos

1.  [Tecnologias Utilizadas](#-tecnologias-utilizadas)
2.  [Estrutura do Projeto](#-estrutura-do-projeto)
3.  [Pré-requisitos](#-pré-requisitos)
4.  [Como Executar Localmente](#-como-executar-localmente)
5.  [Acessando a Aplicação](#-acessando-a-aplicação)
6.  [Documentação da API](#-documentação-da-api)
7.  [Executando os Testes](#-executando-os-testes)
8.  [Autor](#-autor)

## Tecnologias Utilizadas

Este projeto foi construído utilizando uma stack moderna e robusta, com as seguintes tecnologias:

* **Backend:**
    * Java 17
    * Spring Boot 3
    * Spring Data JPA
    * Maven
    * JUnit 5 (Testes unitários e de integração)
* **Frontend:**
    * Angular 17 (com SSR - Server-Side Rendering)
    * TypeScript
    * HTML5 / CSS3
    * Cypress/Jasmine (Testes e2e/unitários)
* **Banco de Dados:**
    * PostgreSQL 15
* **DevOps & Ferramentas:**
    * Docker & Docker Compose
    * Git & GitHub
    * Swagger (Documentação da API)

## Estrutura do Projeto

O projeto está organizado em um formato de Monorepo, contendo as aplicações de backend e frontend no mesmo repositório para facilitar a gestão e o deploy.

```
/organizador-cafe-sulwork/
|
├── .gitignore
├── README.md                 <-- Você está aqui
├── docker-compose.yml        <-- Orquestrador dos serviços
|
├── backend/                  <-- Projeto Spring Boot (API REST)
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
|
└── frontend/                 <-- Projeto Angular (Interface Web)
    ├── src/
    ├── angular.json
    └── Dockerfile
```

## Pré-requisitos

Antes de começar, certifique-se de que você tem as seguintes ferramentas instaladas em sua máquina:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com/products/docker-desktop/)
* [Docker Compose](https://docs.docker.com/compose/install/) (geralmente já vem com o Docker Desktop)

## Como Executar Localmente

Siga os passos abaixo para clonar e executar a aplicação completa em seu ambiente local. Todo o ambiente (backend, frontend e banco de dados) será orquestrado pelo Docker.

**1. Clone o repositório**

```bash
git clone https://github.com/zecacorreia/desafio-sulwork.git
cd desafio-sulwork
```

**2. Crie o arquivo de variáveis de ambiente**

O backend precisa de um arquivo `.env` para se conectar ao banco de dados. Você pode criá-lo a partir do arquivo de exemplo.

> **Nota:** As credenciais neste arquivo correspondem exatamente às definidas no `docker-compose.yml`.

Crie um arquivo chamado `.env` dentro da pasta `backend/` com o seguinte conteúdo:

```properties
# backend/.env
SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/sulwork_cafe
SPRING_DATASOURCE_USERNAME=sulwork
SPRING_DATASOURCE_PASSWORD=sulwork
```

**3. Suba os contêineres com Docker Compose**

Na raiz do projeto (onde está o arquivo `docker-compose.yml`), execute o comando abaixo. Ele irá construir as imagens do backend e do frontend e iniciar todos os contêineres em modo "detached" (-d).

```bash
docker-compose up -d --build
```

O primeiro build pode demorar alguns minutos, pois o Docker precisará baixar as imagens base e instalar todas as dependências.

**4. Verifique se os contêineres estão rodando**

Para garantir que tudo subiu corretamente, execute:

```bash
docker ps
```

Você deverá ver três contêineres em execução: `sulwork_cafe_frontend`, `sulwork_cafe_backend` e `sulwork_cafe_db`.

## Acessando a Aplicação

Após os contêineres estarem no ar, você pode acessar:

* **Frontend (Aplicação Web):**
    * Acesse: **[http://localhost:4000](http://localhost:4000)**

* **Backend (API):**
    * Acesse: **[http://localhost:8080](http://localhost:8080)**

## Documentação da API

A API REST do backend foi documentada utilizando Swagger (OpenAPI). Você pode explorar todos os endpoints disponíveis e interagir com eles através da interface do Swagger.

* **Swagger UI:**
    * Acesse: **[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)**

## Executando os Testes

Os testes são parte fundamental do projeto e podem ser executados localmente.

### Testes do Backend (JUnit)

Para rodar os testes unitários e de integração do backend, navegue até a pasta `backend` e execute o comando do Maven:

```bash
cd backend
mvn test
```

### Testes do Frontend (Jasmine/Cypress)

Para rodar os testes do frontend, navegue até a pasta `frontend` e utilize os comandos do npm:

```bash
cd frontend

# Para testes unitários (Jasmine/Karma)
npm run test

# Para testes e2e (Cypress/Protractor)
npm run e2e
```

## Validações e Regras de Negócio Implementadas

> Implementadas no serviço ParticipantFacadeService#createOrUpdateParticipantWithBreakfast.

### Entrada e saneamento

- Payload obrigatório
   - Rejeita `null` com erro: `Payload obrigatório`.

- Nome do colaborador obrigatório
   - `name` é “trimado” e verificado; se vazio: `Nome é obrigatório`.

- CPF obrigatório, normalizado e válido
   - Remove qualquer caractere não numérico (ex.: pontos e hífen).
   - Valida 11 dígitos (e demais regras do `CpfValidator`): `CPF inválido (11 dígitos)`.

- Data do café obrigatória e futura
   - Rejeita `null`: `Data do café é obrigatória`.
   - Rejeita data de hoje ou passada: `A data do café deve ser maior que a data atual`.

- Itens obrigatórios
   - Rejeita coleções vazias ou nulas: `Informe ao menos um item`.
   - Cada item é “trimado”; se vazio: `Item inválido`.

### Regras de unicidade

- Colaborador não pode repetir (CPF único)
   - Consulta existsByCpf(cpf); se já existir: Já existe colaborador com este CPF.

- Não pode repetir item no mesmo envio (case-insensitive)
   - Verifica duplicatas no próprio request com TreeSet case-insensitive: `Item duplicado no envio: '...'`.

- Não pode repetir item na mesma data (mesmo que outro colaborador)
   - Para cada item, valida `existsByEventAndItemNameIgnoreCase(event, itemName);` se já houver aquele item no evento: `Item '...' já escolhido para esta data`.

### Persistência e consistência

- Evento (data) é criado on-demand
   - Busca `findByEventDate(date);` cria se não existir.

- Colaborador é criado após passar pelas regras
   - Somente após validar nome/CPF/unicidade.

- Itens são criados em lote
   - `saveAll(...)` com vínculo ao colaborador e ao evento.

- Transação atômica
   - Método anotado com `@Transactional` -> ou tudo persiste, ou nada persiste.

### Respostas de sucesso
- Retorno padronizado (`ParticipantResponse`)
   - `id`, `name`, `cpf`, `breakfastDate` e nomes dos itens persistidos.

### Mensagens de erro padronizadas (exemplos)
Essas mensagens são úteis para asserts (ex.: Cypress):

- `Payload obrigatório`
- `Nome é obrigatório`
- `CPF é obrigatório`
- `CPF inválido (11 dígitos)`
- `Já existe colaborador com este CPF`
- `Data do café é obrigatória`
- `A data do café deve ser maior que a data atual`
- `Informe ao menos um item`
- `Item inválido`
- `Item duplicado no envio: 'Pão de Queijo'`
- `Item 'Pão de Queijo' já escolhido para esta data`

### Como o fluxo funciona (resumo técnico)

1. Valida entrada (nome, CPF, data, itens) e normaliza CPF.
2. Garante regras de unicidade (CPF único; itens sem duplicata no request e no evento).
3. Cria/recupera o evento pela data.
4. Cria o colaborador (após todas as validações).
5. Cria os itens vinculando a colaborador + evento.
6. Tudo dentro de uma transação `@Transactional`.

## Autor

Desenvolvido por **Italo Correia**.

* **LinkedIn:** [https://www.linkedin.com/in/zecacorreia/](https://www.linkedin.com/in/zecacorreia/)
* **GitHub:** [https://github.com/zecacorreia](https://github.com/zecacorreia)
