# Microsserviço de Usuário

Microsserviço responsável pelo cadastro, autenticação e gerenciamento de usuários (dados pessoais, endereços e telefones) do sistema de agendamento de tarefas, com integração à API pública do ViaCEP para consulta de endereços.

## 📌 Sobre o projeto

Este serviço faz parte de uma aplicação distribuída em arquitetura de microsserviços, composta por:

- [**BFF**](https://github.com/thaisrvieira/bff-agendador-tarefas) — orquestra as chamadas para os demais serviços
- **Usuário** (este repositório) — gerenciamento de usuários, autenticação e endereços
- [**Agendador de Tarefas**](https://github.com/thaisrvieira/agendador-tarefas) — CRUD de tarefas agendadas (MongoDB)
- [**Notificação**](https://github.com/thaisrvieira/notificacao) — envio de e-mails de notificação

## 🚀 Tecnologias utilizadas

- **Java 17**
- **Spring Boot 4** / Spring Framework 7
- **Spring Data JPA** + **PostgreSQL** — persistência relacional
- **Spring Security** + **JWT** (jjwt) — autenticação e autorização, com usuário implementando `UserDetails`
- **Spring Cloud OpenFeign** — integração com a API pública do **ViaCEP** para consulta de endereço a partir do CEP
- **Swagger / OpenAPI** (springdoc-openapi) — documentação interativa da API
- **Gradle** — gerenciamento de dependências e build
- **Docker** / Docker Compose — containerização
- **SonarQube** — análise estática de qualidade de código
- **Lombok**

## 🏗️ Arquitetura

Estrutura em camadas:

```
controller/
  UsuarioController           → Endpoints REST de usuário, endereço, telefone e consulta de CEP
  GlobalExceptionHandler       → Tratamento centralizado de exceções (@ControllerAdvice)

business/
  UsuarioService                → Regras de negócio: cadastro, autenticação, CRUD de endereço/telefone
  ViaCepService                  → Validação de CEP e integração com o ViaCEP
  converter/UsuarioConverter      → Conversão entre entidades JPA e DTOs
  dto/                            → UsuarioDTO, EnderecoDTO, TelefoneDTO

infrastructure/
  entity/                         → Usuario (implementa UserDetails), Endereco, Telefone
  repository/                     → UsuarioRepository, EnderecoRepository, TelefoneRepository (Spring Data JPA)
  clients/                        → ViaCepClient (Feign) e ViaCepDTO
  security/                       → SecurityConfig, JwtUtil, JwtRequestFilter, UserDetailsServiceImpl
  exceptions/                     → ConflictException, ResourceNotFoundException, UnauthorizedException, IllegalArgumentException
```

## 📋 Funcionalidades

- Cadastro de usuário com criptografia de senha (BCrypt) e validação de e-mail duplicado
- Login com autenticação via `AuthenticationManager` e geração de token JWT
- Busca e exclusão de usuário por e-mail
- Atualização de dados cadastrais do usuário a partir do token JWT (sem exigir o e-mail no corpo da requisição)
- Cadastro e atualização de endereços e telefones vinculados ao usuário
- Consulta de endereço a partir do CEP, com validação de formato e integração com a API ViaCEP via Feign
- Tratamento centralizado de exceções, com respostas HTTP específicas para cada cenário:
  - `404` — usuário/endereço/telefone não encontrado
  - `409` — e-mail já cadastrado
  - `401` — credenciais inválidas
  - `400` — CEP ou argumento inválido

## ⚙️ Como executar

### Pré-requisitos
- Java 17
- Gradle
- PostgreSQL (ou via Docker Compose)

### Rodando localmente

```bash
./gradlew clean build
./gradlew bootRun
```

### Rodando com Docker Compose

```bash
docker compose up --build
```

## 📖 Documentação da API

Com a aplicação em execução, acesse:

```
http://localhost:8080/swagger-ui/index.html
```

## 🔒 Segurança

A autenticação é feita via **JWT**: o usuário realiza login (`POST /usuario/login`) e recebe um token Bearer, que deve ser enviado no header `Authorization` das requisições às rotas protegidas. As seguintes rotas são públicas, todas as demais exigem autenticação:

- `POST /usuario` — cadastro de usuário
- `POST /usuario/login` — login
- `GET /usuario/endereco/{cep}` — consulta de CEP
- `/swagger-ui/**`, `/v3/api-docs/**` — documentação

## 🧪 Qualidade de código

O projeto é analisado com **SonarQube**:

```bash
./gradlew sonar
```

## 👩‍💻 Autora

**Thaís Rodrigues Vieira**
[LinkedIn](https://www.linkedin.com/in/thais-vieira-8471523a2/) | [GitHub](https://github.com/thaisrvieira)
