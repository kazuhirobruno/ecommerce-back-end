# Projeto: E-Commerce Backend com Arquitetura Hexagonal

> Objetivo: Desenvolver um sistema de e-commerce **exclusivamente backend**, utilizando Java e Spring Boot, seguindo boas práticas de arquitetura, microsserviços, mensageria e Docker. O projeto tem finalidade educacional e deve ser construído de forma incremental.

---

# Objetivos do Projeto

Desenvolver um backend completo de e-commerce aplicando:

- Java 21
- Spring Boot 3
- Arquitetura Hexagonal (Ports & Adapters)
- DDD (Domain Driven Design) - nível tático
- Microsserviços
- RabbitMQ (mensageria)
- PostgreSQL
- Docker
- Docker Compose
- Flyway
- Spring Security
- JWT
- OpenAPI (Swagger)
- JUnit 5
- Mockito
- Testcontainers
- MapStruct
- Lombok

---

# Requisitos Arquiteturais

Cada microsserviço deve seguir Arquitetura Hexagonal.

Estrutura padrão:

```
service-name
│
├── application
│   ├── ports
│   │     ├── in
│   │     └── out
│   │
│   └── service
│
├── domain
│   ├── model
│   ├── event
│   ├── exception
│   ├── valueobject
│   └── enums
│
├── adapters
│   ├── in
│   │     ├── rest
│   │     └── messaging
│   │
│   └── out
│         ├── persistence
│         ├── messaging
│         └── client
│
├── config
│
└── EcommerceApplication
```

### Regras

- O domínio não pode conhecer Spring.
- O domínio não pode conhecer JPA.
- O domínio não pode conhecer RabbitMQ.
- Toda comunicação externa deve ocorrer através de Ports.
- Adapters implementam as Ports.
- Controllers nunca possuem regra de negócio.
- Services da aplicação implementam os casos de uso.
- Entidades JPA ficam somente nos adapters de persistência.
- Utilizar DTOs para entrada e saída.
- Utilizar mapeamento com MapStruct.

---

# Arquitetura Geral

```
                     API Gateway
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    User Service    Product Service   Order Service
                                             │
                                             ▼
                                        RabbitMQ
            ┌──────────────┬──────────────┬──────────────┐
            ▼              ▼              ▼
     Inventory Service Payment Service Notification Service
```

Cada serviço possui:

- Banco próprio
- API própria
- Docker próprio
- Documentação Swagger
- Testes

---

# Microsserviços

## User Service

Responsável por:

- Cadastro
- Login
- JWT
- Refresh Token
- Perfis
- Permissões

---

## Product Service

Responsável por:

- Produtos
- Categorias
- Estoque disponível (consulta)
- Imagens (somente URL)

---

## Inventory Service

Responsável por:

- Controle de estoque
- Reserva
- Liberação
- Baixa definitiva

---

## Order Service

Responsável por:

- Carrinho
- Checkout
- Pedido
- Histórico
- Status

---

## Payment Service

Responsável por:

- Simulação de pagamento
- Aprovação
- Recusa
- Cancelamento

---

## Notification Service

Responsável por:

- Email (fake)
- Logs
- Eventos

---

# Fluxo do Checkout

```
Cliente

↓

Order Service

↓

Pedido criado

↓

Evento:
PedidoCriado

↓

RabbitMQ

↓

Inventory Service

↓

Reserva Estoque

↓

Evento:
EstoqueReservado

↓

RabbitMQ

↓

Payment Service

↓

Pagamento

↓

Evento:
PagamentoAprovado

↓

RabbitMQ

↓

Order Service

↓

Pedido Confirmado

↓

RabbitMQ

↓

Notification Service

↓

Email enviado
```

Caso o pagamento falhe:

```
Pagamento Recusado

↓

Evento

↓

Inventory

↓

Libera estoque

↓

Evento

↓

Order

↓

Cancela pedido
```

---

# RabbitMQ

Exchange

```
ecommerce.exchange
```

Routing Keys

```
pedido.criado

estoque.reservado

estoque.falhou

pagamento.aprovado

pagamento.recusado

pedido.confirmado

pedido.cancelado

email.enviar
```

---

# Docker

Utilizar Docker Compose para subir:

```
PostgreSQL

RabbitMQ

User Service

Product Service

Inventory Service

Order Service

Payment Service

Notification Service
```

---

# Banco de Dados

Cada serviço possui banco independente.

Exemplo:

```
user_db

product_db

inventory_db

order_db

payment_db

notification_db
```

Nunca compartilhar banco entre serviços.

---

# Segurança

Implementar:

- JWT
- Refresh Token
- BCrypt
- Roles
- Spring Security

---

# Documentação

Todos os serviços devem possuir:

- Swagger
- OpenAPI

---

# Testes

Implementar:

- Unitários
- Integração
- Testcontainers

---

# Logs

Utilizar:

- SLF4J
- Logback

Logs estruturados.

---

# Tratamento de Erros

Criar:

- Exceptions próprias
- GlobalExceptionHandler
- ErrorResponse

---

# Padrões

Aplicar:

- SOLID
- Clean Code
- Hexagonal
- DDD
- Repository Pattern
- Factory
- Builder
- Strategy (quando necessário)
- Saga Pattern
- Outbox Pattern (opcional)

---

# Etapas de Desenvolvimento

## Etapa 01 - Estrutura do Projeto

### Objetivo

Criar toda estrutura do monorepo.

Checklist

- [ ] Criar monorepo
- [ ] Configurar Maven
- [ ] Configurar Java 21
- [ ] Configurar Spring Boot
- [ ] Criar docker-compose
- [ ] Subir PostgreSQL
- [ ] Subir RabbitMQ
- [ ] Criar README

---

## Etapa 02 - Common Module

Checklist

- [ ] DTOs comuns
- [ ] Eventos
- [ ] Exceptions
- [ ] Utilitários
- [ ] Response padrão

---

## Etapa 03 - User Service

Checklist

- [ ] Estrutura Hexagonal
- [ ] Entidades
- [ ] Casos de uso
- [ ] Cadastro
- [ ] Login
- [ ] JWT
- [ ] Refresh Token
- [ ] Spring Security
- [ ] Swagger
- [ ] Testes

---

## Etapa 04 - Product Service

Checklist

- [ ] Estrutura Hexagonal
- [ ] CRUD Produtos
- [ ] CRUD Categorias
- [ ] Paginação
- [ ] Busca
- [ ] Swagger
- [ ] Testes

---

## Etapa 05 - Inventory Service

Checklist

- [ ] Estrutura Hexagonal
- [ ] Controle de estoque
- [ ] Reserva
- [ ] Liberação
- [ ] Eventos RabbitMQ
- [ ] Testes

---

## Etapa 06 - Order Service

Checklist

- [ ] Estrutura Hexagonal
- [ ] Carrinho
- [ ] Pedido
- [ ] Checkout
- [ ] Histórico
- [ ] Publicação de eventos
- [ ] Testes

---

## Etapa 07 - Payment Service

Checklist

- [ ] Estrutura Hexagonal
- [ ] Simulação pagamento
- [ ] Aprovação
- [ ] Recusa
- [ ] Eventos RabbitMQ
- [ ] Testes

---

## Etapa 08 - Notification Service

Checklist

- [ ] Consumidor RabbitMQ
- [ ] Simulação Email
- [ ] Logs
- [ ] Eventos

---

## Etapa 09 - Saga

Checklist

- [ ] Pedido criado
- [ ] Reserva estoque
- [ ] Pagamento
- [ ] Confirmação
- [ ] Cancelamento
- [ ] Compensação

---

## Etapa 10 - Docker

Checklist

- [ ] Dockerfile User
- [ ] Dockerfile Product
- [ ] Dockerfile Inventory
- [ ] Dockerfile Order
- [ ] Dockerfile Payment
- [ ] Dockerfile Notification
- [ ] Docker Compose funcionando

---

## Etapa 11 - Testes

Checklist

- [ ] Unitários
- [ ] Integração
- [ ] Testcontainers
- [ ] Cobertura mínima de 80%

---

## Etapa 12 - Melhorias

Checklist

- [ ] Logs estruturados
- [ ] Validações
- [ ] Observabilidade
- [ ] Health Check
- [ ] Actuator
- [ ] Performance
- [ ] Refatoração

---

# Regras para a IA durante o desenvolvimento

Sempre que gerar código:

- Seguir Arquitetura Hexagonal.
- Explicar as decisões arquiteturais.
- Gerar código completo, nunca apenas trechos isolados.
- Não simplificar a arquitetura.
- Utilizar boas práticas de Clean Code.
- Priorizar baixo acoplamento e alta coesão.
- Criar testes para cada funcionalidade implementada.
- Documentar APIs com Swagger.
- Utilizar migrations com Flyway.
- Sempre que uma etapa for concluída, marcar os itens correspondentes do checklist.
- Antes de iniciar a próxima etapa, validar que a etapa anterior está funcional.
- Manter consistência de nomenclatura, estrutura de pacotes e padrões em todos os microsserviços.
